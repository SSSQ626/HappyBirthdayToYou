# 交互式礼物实现计划

> **面向 AI 代理的工作者：** 必需子技能：使用 superpowers:subagent-driven-development（推荐）或 superpowers:executing-plans 逐任务实现此计划。步骤使用复选框（`- [ ]`）语法来跟踪进度。

**目标：** 在生日网页的礼物盒撕开后，添加两个交互环节——点亮星星和拼出"生日快乐"——替换原来的被动烟花展示。

**架构：** 单文件 HTML（`F:\birthday\index.html`），在现有 SVG 画布中新增 stars-layer 和 puzzle-layer 图层。交互逻辑通过 JS 事件监听（mousedown/touchstart）实现，音效通过 Web Audio API 合成。

**技术栈：** HTML5 + CSS3 + JavaScript + 内联 SVG + Web Audio API

**规格文档：** `docs/superpowers/specs/2026-06-24-interactive-gift-design.md`

---

### 任务 1：添加 SVG 图层和音效基础设施

**文件：**
- 修改：`F:\birthday\index.html`（SVG defs 区域 + JS 工具函数）

- [ ] **步骤 1：在 SVG defs 中添加星星发光滤镜**

在 `F:\birthday\index.html` 的 `<defs>` 区域内（`</clipPath>` 之后、`</defs>` 之前）添加：

```xml
      <!-- 星星发光（用于点亮后的星星） -->
      <filter id="star-glow" x="-50%" y="-50%" width="200%" height="200%">
        <feGaussianBlur in="SourceGraphic" stdDeviation="6" result="b"/>
        <feMerge>
          <feMergeNode in="b"/>
          <feMergeNode in="b"/>
          <feMergeNode in="SourceGraphic"/>
        </feMerge>
      </filter>
```

- [ ] **步骤 2：在 SVG 中添加 stars-layer 和 puzzle-layer 图层**

在 `F:\birthday\index.html` 的 `<g id="hearts-layer"></g>` 之后添加：

```xml
    <g id="stars-layer" opacity="0"></g>
    <g id="puzzle-layer" opacity="0"></g>
```

- [ ] **步骤 3：添加星星进度文字**

在 puzzle-layer 之后添加：

```xml
    <!-- 星星进度 -->
    <text id="star-progress" x="250" y="750" text-anchor="middle" font-family="Georgia,'宋体',serif"
          font-size="16" fill="rgba(245,230,202,0.5)" opacity="0">✦ 0 / 12</text>
```

- [ ] **步骤 4：在 JS 中添加音效工具函数**

在 `F:\birthday\index.html` 的 JS 部分，在 `function svgEl(tag, attrs) {` 之前添加：

```javascript
  // ============================================================
  // 音效系统（Web Audio API）
  // ============================================================
  var audioCtx = null;

  /** 初始化 AudioContext（需在用户交互后调用） */
  function initAudio() {
    if (audioCtx) return;
    try {
      audioCtx = new (window.AudioContext || window.webkitAudioContext)();
    } catch (e) { /* 静默失败 */ }
  }

  /**
   * 播放简单音效
   * @param {number} freq - 频率 (Hz)
   * @param {number} duration - 持续时间 (秒)
   * @param {number} [vol=0.3] - 音量 (0~1)
   */
  function playTone(freq, duration, vol) {
    if (!audioCtx) return;
    try {
      var osc = audioCtx.createOscillator();
      var gain = audioCtx.createGain();
      osc.type = 'sine';
      osc.frequency.value = freq;
      gain.gain.setValueAtTime(vol || 0.3, audioCtx.currentTime);
      gain.gain.exponentialRampToValueAtTime(0.001, audioCtx.currentTime + duration);
      osc.connect(gain);
      gain.connect(audioCtx.destination);
      osc.start();
      osc.stop(audioCtx.currentTime + duration);
    } catch (e) { /* 静默失败 */ }
  }

  /** "叮"清脆声 — 点亮星星/放对字块 */
  function playDing() { playTone(800, 0.15, 0.3); }

  /** "咚"低沉声 — 放错字块 */
  function playThud() { playTone(300, 0.1, 0.2); }

  /** 和弦上升 — 拼字通关 */
  function playChord() {
    playTone(523, 0.3, 0.25);
    setTimeout(function(){ playTone(659, 0.3, 0.25); }, 150);
    setTimeout(function(){ playTone(784, 0.4, 0.3); }, 300);
  }
```

- [ ] **步骤 5：在首次点击时初始化 AudioContext**

在 `F:\birthday\index.html` 的 JS 中，找到 `document.addEventListener('click', function () {`，在函数体第一行添加 `initAudio();`：

```javascript
  document.addEventListener('click', function () {
    initAudio();  // 浏览器要求用户交互后才能播放音频
    if (storiesDone) return;
    playNextStory();
    skipHint.style.opacity = '0';
  });
```

- [ ] **步骤 6：验证**

在浏览器中打开 `F:\birthday\index.html`，点击屏幕任意位置，检查控制台无报错。

---

### 任务 2：实现星星生成和渲染

**文件：**
- 修改：`F:\birthday\index.html`（JS 部分）

- [ ] **步骤 1：添加星星数据和渲染函数**

在 `F:\birthday\index.html` 的 JS 部分，在 `function launchHearts(count) {` 之前添加：

```javascript
  // ============================================================
  // 阶段 A：点亮星星
  // ============================================================
  var STAR_COUNT = 12;
  var starsLit = 0;
  var stars = []; // {el, x, y, lit}

  /** 生成五角星 path data */
  function starPath(cx, cy, r) {
    var pts = [];
    for (var i = 0; i < 5; i++) {
      var aOuter = (i * 72 - 90) * Math.PI / 180;
      var aInner = ((i * 72 + 36) - 90) * Math.PI / 180;
      pts.push((cx + r * Math.cos(aOuter)).toFixed(1) + ',' + (cy + r * Math.sin(aOuter)).toFixed(1));
      pts.push((cx + r * 0.4 * Math.cos(aInner)).toFixed(1) + ',' + (cy + r * 0.4 * Math.sin(aInner)).toFixed(1));
    }
    return 'M ' + pts.join(' L ') + ' Z';
  }

  /** 创建星星并放入 stars-layer */
  function createStars() {
    starsLayer.innerHTML = '';
    stars = [];
    starsLit = 0;
    starProgress.textContent = '✦ 0 / ' + STAR_COUNT;
    starProgress.setAttribute('opacity', '1');

    for (var i = 0; i < STAR_COUNT; i++) {
      var r = 8 + Math.random() * 8;
      var x = 60 + Math.random() * (VW - 120);
      var y = 150 + Math.random() * (VH - 300);
      var path = svgEl('path', {
        d: starPath(x, y, r),
        fill: '#555',
        opacity: '0.3',
        cursor: 'pointer',
        'data-index': i
      });
      starsLayer.appendChild(path);
      stars.push({ el: path, x: x, y: y, r: r, lit: false });
    }
  }
```

- [ ] **步骤 2：验证星星渲染**

在 `F:\birthday\index.html` 的 JS 中，临时在 `playNextStory();` 之后添加调用（用于测试，测试后删除）：

```javascript
  // 临时测试代码 —— 测试后删除以下 3 行
  mainStage.classList.add('visible');
  createStars();
  starsLayer.setAttribute('opacity', '1');
```

在浏览器中打开页面，确认：12 颗灰色五角星随机分布在屏幕上，大小不一。

测试完成后删除这 3 行临时代码。

---

### 任务 3：实现星星点击交互

**文件：**
- 修改：`F:\birthday\index.html`（JS 部分）

- [ ] **步骤 1：添加星星点击处理和粒子爆开效果**

在 `F:\birthday\index.html` 的 `createStars` 函数之后添加：

```javascript
  /** 点亮一颗星星 */
  function lightStar(idx) {
    var s = stars[idx];
    if (!s || s.lit) return;
    s.lit = true;
    starsLit++;
    starProgress.textContent = '✦ ' + starsLit + ' / ' + STAR_COUNT;

    // 颜色过渡到金色 + 发光
    s.el.style.transition = 'fill 0.3s ease, opacity 0.3s ease';
    s.el.setAttribute('fill', '#f6c343');
    s.el.setAttribute('opacity', '1');
    s.el.setAttribute('filter', 'url(#star-glow)');
    s.el.style.cursor = 'default';

    // 粒子爆开
    burstParticles(s.x, s.y, 7);

    // 音效
    playDing();

    // 全部点亮 → 通关
    if (starsLit >= STAR_COUNT) {
      setTimeout(onStarsComplete, 500);
    }
  }

  /** 从 (cx,cy) 爆出 count 个小粒子 */
  function burstParticles(cx, cy, count) {
    var colors = ['#f6c343', '#f0a6ca', '#ffeaa7'];
    for (var i = 0; i < count; i++) {
      var angle = (i / count) * Math.PI * 2;
      var dist = 20 + Math.random() * 30;
      var r = 1.5 + Math.random() * 1.5;
      var c = svgEl('circle', {
        cx: cx, cy: cy, r: r,
        fill: colors[i % colors.length],
        opacity: '1'
      });
      starsLayer.appendChild(c);
      // 动画
      var tx = cx + Math.cos(angle) * dist;
      var ty = cy + Math.sin(angle) * dist;
      var st = performance.now();
      (function(circle, sx, sy, ex, ey) {
        function anim(now) {
          var t = Math.min((now - st) / 500, 1);
          var ease = 1 - Math.pow(1 - t, 3);
          circle.setAttribute('cx', sx + (ex - sx) * ease);
          circle.setAttribute('cy', sy + (ey - sy) * ease);
          circle.setAttribute('opacity', 1 - t);
          if (t < 1) requestAnimationFrame(anim);
          else if (starsLayer.contains(circle)) starsLayer.removeChild(circle);
        }
        requestAnimationFrame(anim);
      })(c, cx, cy, tx, ty);
    }
  }

  /** 全部星星点亮后的通关动画 */
  function onStarsComplete() {
    // 所有星闪耀
    stars.forEach(function(s) {
      s.el.style.transition = 'opacity 0.2s ease';
    });
    var flashCount = 0;
    function flash() {
      var op = flashCount % 2 === 0 ? '0.6' : '1';
      stars.forEach(function(s) { s.el.setAttribute('opacity', op); });
      flashCount++;
      if (flashCount < 4) setTimeout(flash, 200);
      else {
        // 画面渐亮，过渡到拼字
        setTimeout(startPuzzlePhase, 300);
      }
    }
    flash();
  }
```

- [ ] **步骤 2：给 stars-layer 添加点击事件监听**

在 `F:\birthday\index.html` 的 JS 中，找到 `playNextStory();` 之前的位置，添加事件监听：

```javascript
  // 星星点击事件（事件委托）
  starsLayer.addEventListener('click', function(e) {
    var target = e.target;
    if (target.tagName === 'path' && target.hasAttribute('data-index')) {
      lightStar(parseInt(target.getAttribute('data-index')));
    }
  });
```

- [ ] **步骤 3：验证**

临时添加测试代码（测试后删除）：

```javascript
  // 临时测试 —— 测试后删除
  mainStage.classList.add('visible');
  createStars();
  starsLayer.setAttribute('opacity', '1');
```

在浏览器中打开，逐个点击星星：确认变金色+发光+粒子爆开+进度更新。全部点亮后确认触发通关动画。

测试完成后删除临时代码。

---

### 任务 4：实现拼字游戏

**文件：**
- 修改：`F:\birthday\index.html`（JS 部分）

- [ ] **步骤 1：添加拼字游戏数据和渲染**

在 `F:\birthday\index.html` 的 JS 中，在 `onStarsComplete` 函数之后添加：

```javascript
  // ============================================================
  // 阶段 B：拼出"生日快乐"
  // ============================================================
  var PUZZLE_CHARS = ['生', '日', '快', '乐'];
  var slots = [];       // {el, x, y, char, filled}
  var blocks = [];      // {el, bg, char, x, y, locked, slotIdx}
  var lockedCount = 0;
  var dragging = null;  // 当前拖拽的 block 索引
  var dragOffset = {x: 0, y: 0};

  /** 将 SVG 坐标转换为视口坐标 */
  function svgToScreen(svgX, svgY) {
    var svg = document.getElementById('main-svg');
    var rect = svg.getBoundingClientRect();
    var scaleX = rect.width / VW;
    var scaleY = rect.height / VH;
    // preserveAspectRatio="xMidYMid meet" → 取较小比例
    var scale = Math.min(scaleX, scaleY);
    var offsetX = (rect.width - VW * scale) / 2;
    var offsetY = (rect.height - VH * scale) / 2;
    return {
      x: rect.left + offsetX + svgX * scale,
      y: rect.top + offsetY + svgY * scale
    };
  }

  /** 将屏幕坐标转换为 SVG 坐标 */
  function screenToSvg(screenX, screenY) {
    var svg = document.getElementById('main-svg');
    var rect = svg.getBoundingClientRect();
    var scaleX = rect.width / VW;
    var scaleY = rect.height / VH;
    var scale = Math.min(scaleX, scaleY);
    var offsetX = (rect.width - VW * scale) / 2;
    var offsetY = (rect.height - VH * scale) / 2;
    return {
      x: (screenX - rect.left - offsetX) / scale,
      y: (screenY - rect.top - offsetY) / scale
    };
  }

  /** 创建拼字游戏界面 */
  function createPuzzle() {
    puzzleLayer.innerHTML = '';
    slots = [];
    blocks = [];
    lockedCount = 0;
    dragging = null;

    // 创建 4 个槽位（上方居中排列）
    var slotSize = 60;
    var slotGap = 20;
    var totalW = PUZZLE_CHARS.length * slotSize + (PUZZLE_CHARS.length - 1) * slotGap;
    var startX = (VW - totalW) / 2;

    for (var i = 0; i < PUZZLE_CHARS.length; i++) {
      var sx = startX + i * (slotSize + slotGap) + slotSize / 2;
      var sy = 300;
      // 虚线矩形
      var rect = svgEl('rect', {
        x: sx - slotSize / 2, y: sy - slotSize / 2,
        width: slotSize, height: slotSize, rx: 8,
        fill: 'none', stroke: '#f6c343', 'stroke-width': 1.5,
        'stroke-dasharray': '6,4', opacity: '0.4'
      });
      puzzleLayer.appendChild(rect);
      // 序号提示
      var num = svgEl('text', {
        x: sx, y: sy + 5, 'text-anchor': 'middle',
        'font-family': "Georgia, '宋体', serif", 'font-size': '14',
        fill: 'rgba(246,195,67,0.3)'
      });
      num.textContent = ['①','②','③','④'][i];
      puzzleLayer.appendChild(num);
      slots.push({ el: rect, x: sx, y: sy, char: PUZZLE_CHARS[i], filled: false });
    }

    // 创建 4 个字块（随机散落在下方）
    var blockChars = PUZZLE_CHARS.slice(); // 生日快乐
    // 打乱顺序
    for (var i = blockChars.length - 1; i > 0; i--) {
      var j = Math.floor(Math.random() * (i + 1));
      var tmp = blockChars[i]; blockChars[i] = blockChars[j]; blockChars[j] = tmp;
    }

    var blockSize = 56;
    var blockY = 500 + Math.random() * 80;
    var positions = [];
    // 生成不重叠的 x 位置
    for (var i = 0; i < 4; i++) {
      var bx;
      var attempts = 0;
      do {
        bx = 70 + Math.random() * (VW - 140);
        attempts++;
      } while (attempts < 50 && positions.some(function(p) { return Math.abs(p - bx) < blockSize + 10; }));
      positions.push(bx);
    }

    for (var i = 0; i < blockChars.length; i++) {
      var bx = positions[i];
      var by = blockY + (Math.random() - 0.5) * 40;
      // 背景矩形
      var bg = svgEl('rect', {
        x: bx - blockSize / 2, y: by - blockSize / 2,
        width: blockSize, height: blockSize, rx: 8,
        fill: '#1a1a3e', stroke: '#f6c343', 'stroke-width': 1.5,
        cursor: 'grab'
      });
      puzzleLayer.appendChild(bg);
      // 文字
      var txt = svgEl('text', {
        x: bx, y: by + 8, 'text-anchor': 'middle',
        'font-family': "Georgia, '宋体', serif", 'font-size': '32',
        fill: '#f5e6ca', cursor: 'grab', 'pointer-events': 'none'
      });
      txt.textContent = blockChars[i];
      puzzleLayer.appendChild(txt);
      blocks.push({
        bg: bg, txt: txt, char: blockChars[i],
        x: bx, y: by, locked: false, slotIdx: -1
      });
    }

    // 轻微浮动动画
    startBlockFloat();
  }

  /** 字块轻微浮动 */
  var floatAnimId = null;
  function startBlockFloat() {
    var st = performance.now();
    function tick(now) {
      var t = (now - st) / 1000;
      blocks.forEach(function(b, i) {
        if (b.locked) return;
        var dy = Math.sin(t * 1.5 + i * 1.2) * 4;
        b.bg.setAttribute('y', b.y - 28 + dy);
        b.txt.setAttribute('y', b.y + 8 + dy);
      });
      floatAnimId = requestAnimationFrame(tick);
    }
    floatAnimId = requestAnimationFrame(tick);
  }

  function stopBlockFloat() {
    if (floatAnimId) { cancelAnimationFrame(floatAnimId); floatAnimId = null; }
  }
```

- [ ] **步骤 2：添加拖拽交互逻辑**

在 `createPuzzle` 函数之后添加：

```javascript
  /** 获取事件的 SVG 坐标 */
  function getEventPos(e) {
    if (e.touches && e.touches.length > 0) {
      return screenToSvg(e.touches[0].clientX, e.touches[0].clientY);
    }
    return screenToSvg(e.clientX, e.clientY);
  }

  /** 查找点击/触摸到的字块索引 */
  function findBlockAt(svgX, svgY) {
    for (var i = blocks.length - 1; i >= 0; i--) {
      var b = blocks[i];
      if (b.locked) continue;
      if (Math.abs(svgX - b.x) < 30 && Math.abs(svgY - b.y) < 30) return i;
    }
    return -1;
  }

  /** 拖拽开始 */
  function onDragStart(e) {
    if (lockedCount >= PUZZLE_CHARS.length) return;
    var pos = getEventPos(e);
    var idx = findBlockAt(pos.x, pos.y);
    if (idx < 0) return;
    e.preventDefault();
    dragging = idx;
    dragOffset.x = pos.x - blocks[idx].x;
    dragOffset.y = pos.y - blocks[idx].y;
    blocks[idx].bg.setAttribute('cursor', 'grabbing');
    blocks[idx].bg.setAttribute('stroke-width', '2.5');
  }

  /** 拖拽移动 */
  function onDragMove(e) {
    if (dragging === null) return;
    e.preventDefault();
    var pos = getEventPos(e);
    var b = blocks[dragging];
    b.x = pos.x - dragOffset.x;
    b.y = pos.y - dragOffset.y;
    b.bg.setAttribute('x', b.x - 28);
    b.bg.setAttribute('y', b.y - 28);
    b.txt.setAttribute('x', b.x);
    b.txt.setAttribute('y', b.y + 8);
  }

  /** 拖拽结束 */
  function onDragEnd(e) {
    if (dragging === null) return;
    var b = blocks[dragging];
    b.bg.setAttribute('cursor', 'grab');
    b.bg.setAttribute('stroke-width', '1.5');

    // 检查是否落在对应槽位
    var targetSlot = -1;
    for (var i = 0; i < slots.length; i++) {
      if (slots[i].char === b.char && !slots[i].filled) {
        var dx = b.x - slots[i].x;
        var dy = b.y - slots[i].y;
        if (Math.sqrt(dx * dx + dy * dy) < 35) {
          targetSlot = i;
          break;
        }
      }
    }

    if (targetSlot >= 0) {
      // 放对位置 → 锁定
      b.locked = true;
      b.slotIdx = targetSlot;
      slots[targetSlot].filled = true;
      lockedCount++;
      // 吸附到槽位中心
      b.x = slots[targetSlot].x;
      b.y = slots[targetSlot].y;
      b.bg.setAttribute('x', b.x - 28);
      b.bg.setAttribute('y', b.y - 28);
      b.bg.setAttribute('cursor', 'default');
      b.bg.setAttribute('fill', '#1a1a3e');
      b.bg.setAttribute('stroke', '#f6c343');
      b.txt.setAttribute('x', b.x);
      b.txt.setAttribute('y', b.y + 8);
      // 发光
      b.txt.setAttribute('filter', 'url(#glow-soft)');
      // 音效
      playDing();

      // 全部拼完
      if (lockedCount >= PUZZLE_CHARS.length) {
        stopBlockFloat();
        setTimeout(onPuzzleComplete, 300);
      }
    } else {
      // 放错 → 弹回随机位置
      playThud();
      var newX = 70 + Math.random() * (VW - 140);
      var newY = 500 + Math.random() * 80;
      b.x = newX; b.y = newY;
      b.bg.style.transition = 'x 0.3s ease-out, y 0.3s ease-out';
      b.txt.style.transition = 'x 0.3s ease-out, y 0.3s ease-out';
      b.bg.setAttribute('x', newX - 28);
      b.bg.setAttribute('y', newY - 28);
      b.txt.setAttribute('x', newX);
      b.txt.setAttribute('y', newY + 8);
      setTimeout(function() {
        b.bg.style.transition = '';
        b.txt.style.transition = '';
      }, 350);
    }

    dragging = null;
  }

  /** 拼字通关动画 */
  function onPuzzleComplete() {
    playChord();
    // 四个字放大 + 发光
    blocks.forEach(function(b) {
      b.bg.style.transition = 'all 0.4s ease';
      b.txt.style.transition = 'all 0.4s ease';
      var scale = 1.2;
      b.bg.setAttribute('x', b.x - 28 * scale);
      b.bg.setAttribute('y', b.y - 28 * scale);
      b.bg.setAttribute('width', 56 * scale);
      b.bg.setAttribute('height', 56 * scale);
      b.txt.setAttribute('filter', 'url(#glow-strong)');
    });
    // 淡出
    setTimeout(function() {
      puzzleLayer.style.transition = 'opacity 0.5s ease';
      puzzleLayer.setAttribute('opacity', '0');
      // 阶段 C
      setTimeout(startCelebration, 600);
    }, 600);
  }
```

- [ ] **步骤 3：绑定拖拽事件**

在 `F:\birthday\index.html` 的 JS 中，找到 `starsLayer.addEventListener('click', ...)` 之后，添加：

```javascript
  // 拖拽事件（鼠标 + 触摸）
  puzzleLayer.addEventListener('mousedown', onDragStart);
  document.addEventListener('mousemove', onDragMove);
  document.addEventListener('mouseup', onDragEnd);
  puzzleLayer.addEventListener('touchstart', onDragStart, { passive: false });
  document.addEventListener('touchmove', onDragMove, { passive: false });
  document.addEventListener('touchend', onDragEnd);
```

- [ ] **步骤 4：验证**

临时添加测试代码（测试后删除）：

```javascript
  // 临时测试 —— 测试后删除
  mainStage.classList.add('visible');
  createPuzzle();
  puzzleLayer.setAttribute('opacity', '1');
```

在浏览器中打开，测试：
1. 四个字块散落在下方，有轻微浮动
2. 拖拽字块到对应槽位 → 吸附锁定 + "叮"声
3. 拖到错误位置 → 弹回 + "咚"声
4. 全部拼完 → 放大发光 → 淡出

测试完成后删除临时代码。

---

### 任务 5：串联完整流程

**文件：**
- 修改：`F:\birthday\index.html`（JS 中的 `onTearDone` 函数）

- [ ] **步骤 1：修改 onTearDone 触发星星阶段**

在 `F:\birthday\index.html` 的 JS 中，找到 `function onTearDone() {`，将整个函数体替换为：

```javascript
  function onTearDone() {
    // 礼物盒淡出
    giftBox.style.transition = 'opacity 0.6s ease';
    giftBox.setAttribute('opacity', '0');

    // 画面渐暗
    setTimeout(function() {
      mainStage.style.transition = 'background 0.8s ease';
      mainStage.style.background = 'radial-gradient(ellipse at center, #0a0a1a 0%, #050510 70%)';
    }, 300);

    // 启动星星阶段
    setTimeout(function() {
      createStars();
      starsLayer.style.transition = 'opacity 0.6s ease';
      starsLayer.setAttribute('opacity', '1');
    }, 800);
  }
```

- [ ] **步骤 2：添加 startPuzzlePhase 函数**

在 `onTearDone` 函数之后、`startCelebration` 函数之前添加：

```javascript
  /** 从星星阶段过渡到拼字阶段 */
  function startPuzzlePhase() {
    // 星星淡出
    starsLayer.style.transition = 'opacity 0.4s ease';
    starsLayer.setAttribute('opacity', '0');
    starProgress.style.transition = 'opacity 0.4s ease';
    starProgress.setAttribute('opacity', '0');

    // 拼字淡入
    setTimeout(function() {
      createPuzzle();
      puzzleLayer.style.transition = 'opacity 0.5s ease';
      puzzleLayer.setAttribute('opacity', '1');
    }, 500);
  }
```

- [ ] **步骤 3：验证完整流程**

在浏览器中打开 `F:\birthday\index.html`，完整测试：

1. 故事放映 → 点击跳过 → 礼物盒弹出 → 撕开
2. 画面渐暗 → 星星出现 → 逐个点击点亮 → 通关动画
3. 拼字界面出现 → 拖拽拼出"生日快乐" → 通关动画
4. 烟花 → 祝福卡 → 快递提醒 → 永久定格

确认所有环节衔接流畅，无报错。

---

### 任务 6：清理和最终验证

**文件：**
- 修改：`F:\birthday\index.html`

- [ ] **步骤 1：移除所有临时测试代码**

搜索并删除所有 `// 临时测试` 注释及相关代码行。

- [ ] **步骤 2：移除旧的 post-fw-text 相关代码**

在 `startCelebration` 函数中，找到并删除以下旧代码（这些是原来烟花后的过渡文字，现在不再需要）：

```javascript
    // === 5.5s：飘浮爱心 + "Happy Birthday" ===
    setTimeout(function(){
      launchHearts(15);
      postFwText.style.transition='opacity 0.8s ease';
      postFwText.setAttribute('opacity','1');
    }, 5500);

    // === 7s：爱心和文字淡出 ===
    setTimeout(function(){
      postFwText.style.transition='opacity 0.8s ease';
      postFwText.setAttribute('opacity','0');
    }, 7000);
```

同时删除 SVG 中的 `<g id="post-fw-text">` 元素。

- [ ] **步骤 3：移除不再需要的 hearts-layer 引用**

在 JS DOM 引用中删除 `const heartsLayer = ...` 和 `function launchHearts(count)` 相关代码（因为阶段 C 中的爱心已经不再需要，由拼字通关替代）。

同时删除 SVG 中的 `<g id="hearts-layer"></g>`。

- [ ] **步骤 4：移动端适配检查**

在 Chrome DevTools 中切换到移动端视口（375px 宽），确认：
- 星星足够大可点击
- 字块足够大可拖拽
- 槽位清晰可见
- 进度文字不溢出

- [ ] **步骤 5：最终全流程验证**

在桌面端和移动端各完整测试一次全流程，确认无报错、无视觉异常。
