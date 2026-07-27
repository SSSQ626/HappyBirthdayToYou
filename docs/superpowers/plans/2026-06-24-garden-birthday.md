# 花园生日互动网页 实现计划

> **面向 AI 代理的工作者：** 必需子技能：使用 superpowers:subagent-driven-development（推荐）或 superpowers:executing-plans 逐任务实现此计划。步骤使用复选框（`- [ ]`）语法来跟踪进度。

**目标：** 为 LSQ 的 2026.6.26 生日制作一个可爱卡通风格的花园互动生日祝福网页

**架构：** 单个 `index.html` 文件，内联所有 CSS 和 JS。页面分为三个场景层：贺卡开场 → 花园互动 → 礼物揭晓。通过 CSS 类切换和 JS 定时器控制场景流转。

**技术栈：** HTML5 + CSS3（动画/3D变换）+ Canvas 2D（粒子/擦除）+ Web Audio API（CDN音频）

---

## 文件结构

```
F:\birthday\
├── index.html                    # 唯一文件，所有代码内联
└── docs/
    ├── superpowers/specs/        # 设计规格（已完成）
    └── superpowers/plans/        # 本计划
```

**index.html 内部代码分区：**

| 区域 | 内容 |
|------|------|
| `<head>` | meta、Google Fonts CDN、`<style>` 全部 CSS |
| `<body>` HTML | 贺卡层、花园层、礼物层、Canvas层、Doraemon、UI控件 |
| `<script>` 变量区 | 可配置变量（OWNER_NAME 等） |
| `<script>` 贺卡逻辑 | 贺卡弹入、3D翻页、过渡到花园 |
| `<script>` 花园逻辑 | 点击特效、emoji弹出、Canvas粒子系统 |
| `<script>` Doraemon逻辑 | 眨眼、点击跳动、气泡框 |
| `<script>` 礼物逻辑 | 自动/手动触发、擦除动画、最终画面 |
| `<script>` 音频逻辑 | 背景音乐、音效、静音按钮 |
| `<script>` 启动 | 页面加载后初始化 |

---

## 任务 1：HTML 骨架 + 可配置变量 + 全局 CSS

**文件：**
- 创建：`F:\birthday\index.html`

- [ ] **步骤 1：创建完整 HTML 骨架**

创建 `F:\birthday\index.html`，写入以下完整内容：

```html
<!DOCTYPE html>
<html lang="zh-CN">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
    <title>🎂 LSQ 生日快乐</title>
    <!-- Google Fonts CDN - 卡通风格字体 -->
    <link rel="preconnect" href="https://fonts.googleapis.com">
    <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
    <link href="https://fonts.googleapis.com/css2?family=Baloo+2:wght@400;600;800&display=swap" rel="stylesheet">
    <style>
        /* ============================================
           ⚙️ CSS 代码将在后续任务中逐步添加
           ============================================ */

        /* ---------- 全局重置 ---------- */
        * { margin: 0; padding: 0; box-sizing: border-box; }
        html, body {
            width: 100%; height: 100%;
            overflow: hidden;
            font-family: 'Baloo 2', cursive, sans-serif;
            -webkit-tap-highlight-color: transparent;
            user-select: none;
        }

        /* ---------- 场景容器 ---------- */
        .scene {
            position: fixed;
            top: 0; left: 0;
            width: 100%; height: 100%;
            display: flex;
            justify-content: center;
            align-items: center;
            transition: opacity 0.8s ease;
        }
        .scene.hidden { opacity: 0; pointer-events: none; }
    </style>
</head>
<body>
    <!-- ====== 第一层：贺卡 ====== -->
    <div id="greeting-scene" class="scene">
        <!-- 贺卡内容将在任务2中添加 -->
    </div>

    <!-- ====== 第二层：花园互动 ====== -->
    <div id="garden-scene" class="scene hidden">
        <!-- 花园内容将在任务3中添加 -->
    </div>

    <!-- ====== 第三层：礼物揭晓 ====== -->
    <div id="gift-scene" class="scene hidden">
        <!-- 礼物内容将在任务7中添加 -->
    </div>

    <!-- ====== 全局Canvas层（粒子+擦除） ====== -->
    <canvas id="fx-canvas"></canvas>

    <script>
        /* ============================================
           ⚙️ 【可编辑区域】在这里修改你的内容
           ============================================ */

        /** 🎂 主人公名字 */
        const OWNER_NAME = "LSQ";

        /** 📅 生日日期 */
        const BIRTHDAY_DATE = "2026.6.26";

        /** 💌 贺卡祝福文字 */
        const GREETING_TEXT = "生日快乐";

        /** 💌 贺卡副标题 */
        const GREETING_SUB = "Happy Birthday";

        /** 🎁 擦除后显示的礼物文字 */
        const GIFT_TEXT = "快递ing，注意查收哦 📦";

        /** 🎁 揭晓卡片底部小字 */
        const GIFT_SUB_TEXT = `${BIRTHDAY_DATE} · ${OWNER_NAME}的专属生日礼物`;

        /** 🐱 Doraemon 气泡框文字（揭晓时） */
        const DORAEMON_SAY = "嘿，有个快递给你！";

        /** 🐱 Doraemon 点击祝福语 */
        const DORAEMON_BLESS = `${OWNER_NAME}，生日快乐呀！`;

        /** ⏱️ 互动多久后自动触发礼物（秒） */
        const INTERACTIVE_DURATION = 15;

        /** ✨ 每次点击粒子数量 */
        const PARTICLE_COUNT = 12;

        /* ============================================
           🚀 后续 JS 逻辑将在后续任务中逐步添加
           ============================================ */
    </script>
</body>
</html>
```

- [ ] **步骤 2：验证文件创建成功**

在浏览器中打开 `F:\birthday\index.html`，确认：
- 页面标题显示 "🎂 LSQ 生日快乐"
- 页面为空白（场景还没内容）
- 控制台无报错

- [ ] **步骤 3：Commit**

```bash
cd F:/birthday
git init
git add index.html
git commit -m "feat: HTML骨架 + 可配置变量 + 全局CSS重置"
```

---

## 任务 2：贺卡开场（3D 翻页效果）

**文件：**
- 修改：`F:\birthday\index.html`（CSS 区域 + HTML 贺卡区域 + JS 贺卡逻辑）

- [ ] **步骤 1：添加贺卡 CSS 样式**

在 `<style>` 标签内，`</style>` 之前，添加以下 CSS：

```css
/* ---------- 贺卡开场 ---------- */
#greeting-scene {
    background: linear-gradient(135deg, #FFF5E6 0%, #FFE4C4 50%, #FFDAB9 100%);
    perspective: 1200px;
    z-index: 20;
}

.greeting-card {
    width: 320px;
    min-height: 420px;
    background: linear-gradient(160deg, #fff 0%, #FFF8F0 100%);
    border-radius: 20px;
    padding: 40px 30px;
    text-align: center;
    position: relative;
    cursor: pointer;
    transform-style: preserve-3d;
    /* 多层阴影营造厚度感 */
    box-shadow:
        0 2px 4px rgba(0,0,0,0.05),
        0 8px 16px rgba(0,0,0,0.08),
        0 20px 40px rgba(0,0,0,0.1),
        0 0 0 1px rgba(0,0,0,0.03);
    /* 从下方弹入 */
    animation: cardBounceIn 1s cubic-bezier(0.34, 1.56, 0.64, 1) forwards,
               cardSway 3s ease-in-out 1s infinite;
    opacity: 0;
    transform: translateY(100px);
}

@keyframes cardBounceIn {
    0% { opacity: 0; transform: translateY(100px) scale(0.9); }
    100% { opacity: 1; transform: translateY(0) scale(1); }
}

@keyframes cardSway {
    0%, 100% { transform: rotate(0deg); }
    25% { transform: rotate(2deg); }
    75% { transform: rotate(-2deg); }
}

/* 贺卡翻页动画 */
.greeting-card.flip {
    animation: cardFlip 0.8s ease-in forwards;
}

@keyframes cardFlip {
    0% { transform: rotateY(0deg) scale(1); opacity: 1; }
    50% { transform: rotateY(-90deg) scale(1.05); opacity: 0.8; }
    100% { transform: rotateY(-180deg) scale(1.1); opacity: 0; }
}

/* 贺卡内容样式 */
.card-name {
    font-size: 48px;
    font-weight: 800;
    color: #FF8C69;
    margin-bottom: 8px;
    text-shadow: 2px 2px 0 rgba(255, 140, 105, 0.2);
}

.card-greeting {
    font-size: 28px;
    font-weight: 600;
    color: #E8836B;
    margin-bottom: 16px;
}

.card-date {
    font-size: 16px;
    color: #CCAA88;
    letter-spacing: 2px;
    margin-bottom: 24px;
}

.card-deco {
    font-size: 32px;
    margin: 12px 0;
}

.card-heart {
    display: inline-block;
    font-size: 40px;
    animation: heartBeat 1.2s ease-in-out infinite;
}

@keyframes heartBeat {
    0%, 100% { transform: scale(1); }
    15% { transform: scale(1.2); }
    30% { transform: scale(1); }
    45% { transform: scale(1.15); }
}

.card-hint {
    position: absolute;
    bottom: 20px;
    left: 50%;
    transform: translateX(-50%);
    font-size: 14px;
    color: #CCAA88;
    opacity: 0.6;
    animation: hintPulse 2s ease-in-out infinite;
}

@keyframes hintPulse {
    0%, 100% { opacity: 0.4; }
    50% { opacity: 0.8; }
}
```

- [ ] **步骤 2：添加贺卡 HTML 结构**

将 `<div id="greeting-scene" class="scene">` 内部替换为：

```html
<div id="greeting-scene" class="scene">
    <div class="greeting-card" id="greeting-card">
        <div class="card-deco">🌸 🌸 🌸</div>
        <div class="card-name" id="card-name"></div>
        <div class="card-greeting" id="card-greeting"></div>
        <div class="card-date" id="card-date"></div>
        <div class="card-heart">❤️</div>
        <div class="card-hint">👆 点击我</div>
    </div>
</div>
```

- [ ] **步骤 3：添加贺卡 JS 逻辑**

在 `<script>` 标签内，最后一个 `</script>` 之前，添加：

```javascript
/* ============================================
   💌 贺卡逻辑
   ============================================ */
const greetingScene = document.getElementById('greeting-scene');
const gardenScene = document.getElementById('garden-scene');
const greetingCard = document.getElementById('greeting-card');

// 填充贺卡内容
document.getElementById('card-name').textContent = OWNER_NAME;
document.getElementById('card-greeting').textContent = GREETING_TEXT;
document.getElementById('card-date').textContent = BIRTHDAY_DATE;

// 点击贺卡 → 3D翻页 → 过渡到花园
greetingCard.addEventListener('click', () => {
    greetingCard.classList.add('flip');
    // 翻页动画结束后切换场景
    setTimeout(() => {
        greetingScene.classList.add('hidden');
        gardenScene.classList.remove('hidden');
        // 启动花园互动计时器
        startGiftTimer();
    }, 800);
});
```

- [ ] **步骤 4：在浏览器中验证**

打开 `F:\birthday\index.html`，确认：
- 贺卡从下方弹入，显示 "LSQ" + "生日快乐" + "2026.6.26"
- 贺卡有厚度阴影，微微摇摆
- 点击贺卡后有3D翻页效果
- 翻页后贺卡消失，显示花园场景（目前还是空白）

- [ ] **步骤 5：Commit**

```bash
git add index.html
git commit -m "feat: 贺卡开场 + 3D翻页效果"
```

---

## 任务 3：花园场景背景（天空+云+草地+花+树）

**文件：**
- 修改：`F:\birthday\index.html`（CSS + HTML）

- [ ] **步骤 1：添加花园背景 CSS**

在 `<style>` 标签内添加：

```css
/* ---------- 花园场景 ---------- */
#garden-scene {
    background: linear-gradient(180deg,
        #F4A460 0%,
        #FFB88C 25%,
        #FFCBA4 50%,
        #E8B4B8 75%,
        #D4A5C7 100%
    );
    overflow: hidden;
    z-index: 10;
}

/* 白云 */
.cloud {
    position: absolute;
    background: rgba(255, 255, 255, 0.8);
    border-radius: 50px;
    filter: blur(1px);
}
.cloud::before, .cloud::after {
    content: '';
    position: absolute;
    background: inherit;
    border-radius: 50%;
}
.cloud-1 {
    width: 120px; height: 40px;
    top: 8%; left: -150px;
    animation: cloudFloat 25s linear infinite;
}
.cloud-1::before { width: 50px; height: 50px; top: -25px; left: 20px; }
.cloud-1::after { width: 70px; height: 60px; top: -30px; left: 50px; }

.cloud-2 {
    width: 100px; height: 35px;
    top: 15%; left: -120px;
    animation: cloudFloat 30s linear 5s infinite;
    opacity: 0.7;
}
.cloud-2::before { width: 45px; height: 45px; top: -22px; left: 15px; }
.cloud-2::after { width: 55px; height: 50px; top: -25px; left: 40px; }

.cloud-3 {
    width: 90px; height: 30px;
    top: 5%; left: -100px;
    animation: cloudFloat 20s linear 12s infinite;
    opacity: 0.6;
}
.cloud-3::before { width: 40px; height: 40px; top: -20px; left: 10px; }
.cloud-3::after { width: 50px; height: 45px; top: -22px; left: 35px; }

@keyframes cloudFloat {
    0% { transform: translateX(0); }
    100% { transform: translateX(calc(100vw + 200px)); }
}

/* 草地 */
.ground {
    position: absolute;
    bottom: 0;
    width: 100%;
    height: 30%;
    background: linear-gradient(180deg, #7CB342 0%, #558B2F 60%, #33691E 100%);
    border-radius: 50% 50% 0 0 / 20% 20% 0 0;
}
.ground::before {
    content: '';
    position: absolute;
    top: -20px;
    width: 100%;
    height: 40px;
    background: linear-gradient(180deg, transparent, #7CB342);
    border-radius: 50%;
}

/* CSS 小花 */
.flower {
    position: absolute;
    bottom: 28%;
    width: 20px;
    height: 20px;
}
.flower::before {
    content: '🌸';
    font-size: 24px;
    position: absolute;
    bottom: 0;
    animation: flowerSway 3s ease-in-out infinite;
}
.flower-1 { left: 10%; }
.flower-1::before { animation-delay: 0s; }
.flower-2 { left: 25%; bottom: 26%; }
.flower-2::before { content: '🌼'; animation-delay: 0.5s; }
.flower-3 { left: 45%; bottom: 30%; }
.flower-3::before { content: '🌻'; animation-delay: 1s; font-size: 28px; }
.flower-4 { left: 65%; bottom: 27%; }
.flower-4::before { content: '🌷'; animation-delay: 1.5s; }
.flower-5 { left: 82%; bottom: 29%; }
.flower-5::before { content: '🌸'; animation-delay: 2s; }
.flower-6 { left: 35%; bottom: 25%; }
.flower-6::before { content: '🌺'; animation-delay: 0.8s; font-size: 20px; }
.flower-7 { left: 72%; bottom: 26%; }
.flower-7::before { content: '🌼'; animation-delay: 1.2s; }

@keyframes flowerSway {
    0%, 100% { transform: rotate(-3deg); }
    50% { transform: rotate(3deg); }
}

/* 远景树丛 */
.bush {
    position: absolute;
    bottom: 26%;
    border-radius: 50%;
    background: rgba(56, 100, 30, 0.5);
}
.bush-1 { width: 100px; height: 70px; left: 5%; }
.bush-2 { width: 80px; height: 55px; left: 55%; bottom: 27%; }
.bush-3 { width: 120px; height: 80px; right: 5%; bottom: 25%; }
```

- [ ] **步骤 2：添加花园 HTML 结构**

将 `<div id="garden-scene" class="scene hidden">` 内部替换为：

```html
<div id="garden-scene" class="scene hidden">
    <!-- 白云 -->
    <div class="cloud cloud-1"></div>
    <div class="cloud cloud-2"></div>
    <div class="cloud cloud-3"></div>

    <!-- 远景树丛 -->
    <div class="bush bush-1"></div>
    <div class="bush bush-2"></div>
    <div class="bush bush-3"></div>

    <!-- 草地 -->
    <div class="ground"></div>

    <!-- 小花装饰 -->
    <div class="flower flower-1"></div>
    <div class="flower flower-2"></div>
    <div class="flower flower-3"></div>
    <div class="flower flower-4"></div>
    <div class="flower flower-5"></div>
    <div class="flower flower-6"></div>
    <div class="flower flower-7"></div>
</div>
```

- [ ] **步骤 3：在浏览器中验证**

点击贺卡进入花园后，确认：
- 天空是暖色夕阳渐变（橙→粉→紫）
- 白云从左向右缓慢飘动
- 底部有绿色草地（圆弧起伏）
- 草地上有 7 朵不同颜色的小花在轻轻摇摆
- 远景有半透明的深绿色树丛

- [ ] **步骤 4：Commit**

```bash
git add index.html
git commit -m "feat: 花园场景背景（天空/云/草地/花/树丛）"
```

---

## 任务 4：点击特效系统（emoji + Canvas 粒子）

**文件：**
- 修改：`F:\birthday\index.html`（CSS + JS）

- [ ] **步骤 1：添加 Canvas 和特效 CSS**

在 `<style>` 标签内添加：

```css
/* ---------- 全局Canvas层 ---------- */
#fx-canvas {
    position: fixed;
    top: 0; left: 0;
    width: 100%; height: 100%;
    z-index: 15;
    pointer-events: none;
}

/* ---------- 点击特效emoji ---------- */
.click-emoji {
    position: fixed;
    font-size: 40px;
    pointer-events: none;
    z-index: 16;
    animation: emojiPop 2s cubic-bezier(0.34, 1.56, 0.64, 1) forwards;
}

@keyframes emojiPop {
    0% { transform: scale(0) rotate(0deg); opacity: 1; }
    30% { transform: scale(1.3) rotate(10deg); opacity: 1; }
    60% { transform: scale(1) rotate(-5deg); opacity: 1; }
    100% { transform: scale(0.8) translateY(-30px) rotate(0deg); opacity: 0; }
}

/* 蝴蝶特殊动画 */
.click-emoji.butterfly {
    animation: butterflyFly 2.5s cubic-bezier(0.25, 0.46, 0.45, 0.94) forwards;
}

@keyframes butterflyFly {
    0% { transform: scale(0); opacity: 1; }
    20% { transform: scale(1.2) translate(20px, -40px); opacity: 1; }
    50% { transform: scale(1) translate(-10px, -80px); opacity: 1; }
    80% { transform: scale(0.9) translate(30px, -120px); opacity: 0.8; }
    100% { transform: scale(0.8) translate(10px, -150px); opacity: 0; }
}

/* 气球特殊动画 */
.click-emoji.balloon {
    animation: balloonRise 3s ease-out forwards;
}

@keyframes balloonRise {
    0% { transform: scale(0.5); opacity: 1; }
    20% { transform: scale(1) translate(10px, 0); opacity: 1; }
    100% { transform: scale(0.6) translate(-20px, -100vh); opacity: 0; }
}

/* 星星特殊动画 */
.click-emoji.star {
    animation: starSpin 2s ease-out forwards;
}

@keyframes starSpin {
    0% { transform: scale(0) rotate(0deg); opacity: 1; }
    30% { transform: scale(1.5) rotate(180deg); opacity: 1; }
    60% { transform: scale(1) rotate(360deg); opacity: 0.8; }
    100% { transform: scale(0) rotate(540deg); opacity: 0; }
}

/* 彩虹特殊动画 */
.click-emoji.rainbow {
    font-size: 60px;
    animation: rainbowExpand 2s ease-out forwards;
}

@keyframes rainbowExpand {
    0% { transform: scale(0); opacity: 1; }
    30% { transform: scale(1.2); opacity: 1; }
    70% { transform: scale(1); opacity: 0.8; }
    100% { transform: scale(1.5); opacity: 0; }
}
```

- [ ] **步骤 2：添加点击特效 JS 逻辑**

在 `<script>` 标签内添加：

```javascript
/* ============================================
   ✨ 点击特效系统
   ============================================ */
const fxCanvas = document.getElementById('fx-canvas');
const fxCtx = fxCanvas.getContext('2d');

// 调整Canvas尺寸
function resizeFxCanvas() {
    fxCanvas.width = window.innerWidth;
    fxCanvas.height = window.innerHeight;
}
resizeFxCanvas();
window.addEventListener('resize', resizeFxCanvas);

// 特效emoji列表
const EMOJI_LIST = [
    { emoji: '🌸', css: '' },
    { emoji: '🦋', css: 'butterfly' },
    { emoji: '🎈', css: 'balloon' },
    { emoji: '⭐', css: 'star' },
    { emoji: '🌈', css: 'rainbow' }
];

// 粒子颜色（暖色系）
const WARM_COLORS = ['#FFD700', '#FF8C69', '#FFB6C1', '#FFA07A', '#FFDAB9', '#F4A460'];

/**
 * 在点击位置创建emoji特效
 */
function spawnEmoji(x, y) {
    const data = EMOJI_LIST[Math.floor(Math.random() * EMOJI_LIST.length)];
    const el = document.createElement('div');
    el.className = 'click-emoji' + (data.css ? ' ' + data.css : '');
    el.textContent = data.emoji;
    el.style.left = (x - 20) + 'px';
    el.style.top = (y - 20) + 'px';
    document.body.appendChild(el);

    // 动画结束后移除
    const duration = data.css === 'balloon' ? 3000 : 2500;
    setTimeout(() => el.remove(), duration);
}

/**
 * Canvas粒子爆发
 */
let particles = [];

function spawnParticles(x, y) {
    const count = window.innerWidth < 480 ? Math.floor(PARTICLE_COUNT / 2) : PARTICLE_COUNT;
    for (let i = 0; i < count; i++) {
        const angle = (Math.PI * 2 / count) * i + Math.random() * 0.5;
        const speed = Math.random() * 4 + 2;
        particles.push({
            x: x,
            y: y,
            vx: Math.cos(angle) * speed,
            vy: Math.sin(angle) * speed,
            size: Math.random() * 4 + 2,
            color: WARM_COLORS[Math.floor(Math.random() * WARM_COLORS.length)],
            life: 1,
            decay: Math.random() * 0.02 + 0.015
        });
    }
}

/**
 * 粒子动画循环
 */
function animateParticles() {
    fxCtx.clearRect(0, 0, fxCanvas.width, fxCanvas.height);

    for (let i = particles.length - 1; i >= 0; i--) {
        const p = particles[i];
        p.x += p.vx;
        p.y += p.vy;
        p.vy += 0.05; // 微重力
        p.life -= p.decay;

        if (p.life <= 0) {
            particles.splice(i, 1);
            continue;
        }

        fxCtx.globalAlpha = p.life;
        fxCtx.fillStyle = p.color;
        fxCtx.beginPath();
        fxCtx.arc(p.x, p.y, p.size, 0, Math.PI * 2);
        fxCtx.fill();
    }

    fxCtx.globalAlpha = 1;
    requestAnimationFrame(animateParticles);
}

animateParticles();

/**
 * 花园场景的点击事件处理
 */
gardenScene.addEventListener('click', (e) => {
    // 如果礼物揭晓已触发，不再响应点击
    if (giftTriggered) return;
    // 如果点击的是Doraemon或礼物按钮，不触发普通特效
    if (e.target.closest('#doraemon') || e.target.closest('#gift-btn')) return;

    spawnEmoji(e.clientX, e.clientY);
    spawnParticles(e.clientX, e.clientY);
});

// 触摸事件支持
gardenScene.addEventListener('touchstart', (e) => {
    if (giftTriggered) return;
    if (e.target.closest('#doraemon') || e.target.closest('#gift-btn')) return;

    const touch = e.touches[0];
    spawnEmoji(touch.clientX, touch.clientY);
    spawnParticles(touch.clientX, touch.clientY);
}, { passive: true });
```

- [ ] **步骤 3：添加 `giftTriggered` 变量占位**

在 JS 变量区末尾添加（后续任务会完善）：

```javascript
// 礼物是否已触发（防止重复触发）
let giftTriggered = false;
```

- [ ] **步骤 4：在浏览器中验证**

进入花园后，点击屏幕任意位置，确认：
- 点击处弹出随机emoji（🌸🦋🎈⭐🌈）
- 同时有彩色粒子向四周爆发
- emoji和粒子动画结束后自动消失
- 不同emoji有不同动画轨迹（气球向上飘、蝴蝶飞弧线等）

- [ ] **步骤 5：Commit**

```bash
git add index.html
git commit -m "feat: 点击特效系统（emoji弹出 + Canvas粒子爆发）"
```

---

## 任务 5：Doraemon CSS 绘制 + 眨眼动画

**文件：**
- 修改：`F:\birthday\index.html`（CSS + HTML）

- [ ] **步骤 1：添加 Doraemon CSS**

在 `<style>` 标签内添加：

```css
/* ---------- Doraemon ---------- */
#doraemon {
    position: fixed;
    bottom: 30px;
    right: 30px;
    width: 200px;
    height: 220px;
    cursor: pointer;
    z-index: 18;
    transition: transform 0.3s ease;
}

#doraemon:hover {
    transform: scale(1.05);
}

/* 头部 - 蓝色圆 */
.dora-head {
    width: 180px;
    height: 170px;
    background: #0099E6;
    border-radius: 50%;
    position: absolute;
    top: 0;
    left: 10px;
    overflow: hidden;
    box-shadow: inset 0 -10px 20px rgba(0,0,0,0.1);
}

/* 脸部 - 白色椭圆 */
.dora-face {
    width: 140px;
    height: 120px;
    background: #fff;
    border-radius: 50%;
    position: absolute;
    bottom: 10px;
    left: 50%;
    transform: translateX(-50%);
}

/* 眼睛容器 */
.dora-eyes {
    position: absolute;
    top: 25px;
    left: 50%;
    transform: translateX(-50%);
    display: flex;
    gap: 6px;
}

.dora-eye {
    width: 40px;
    height: 44px;
    background: #fff;
    border-radius: 50%;
    position: relative;
    border: 2px solid #333;
}

.dora-pupil {
    width: 14px;
    height: 16px;
    background: #333;
    border-radius: 50%;
    position: absolute;
    bottom: 10px;
    left: 50%;
    transform: translateX(-50%);
}

/* 眨眼动画 */
.dora-eye {
    animation: blink 4s ease-in-out infinite;
}

.dora-eye.right {
    animation-delay: 0.05s;
}

@keyframes blink {
    0%, 90%, 100% { transform: scaleY(1); }
    95% { transform: scaleY(0.1); }
}

/* 鼻子 - 红色圆 */
.dora-nose {
    width: 22px;
    height: 22px;
    background: #E53935;
    border-radius: 50%;
    position: absolute;
    top: 65px;
    left: 50%;
    transform: translateX(-50%);
    border: 2px solid #333;
}

/* 鼻子下方竖线 */
.dora-nose::after {
    content: '';
    position: absolute;
    top: 20px;
    left: 50%;
    transform: translateX(-50%);
    width: 2px;
    height: 20px;
    background: #333;
}

/* 嘴巴 - 弧线 */
.dora-mouth {
    width: 80px;
    height: 40px;
    border: 3px solid #333;
    border-top: none;
    border-radius: 0 0 80px 80px;
    position: absolute;
    top: 90px;
    left: 50%;
    transform: translateX(-50%);
}

/* 胡须 */
.dora-whiskers {
    position: absolute;
    top: 85px;
    width: 100%;
}
.dora-whisker {
    width: 35px;
    height: 2px;
    background: #333;
    position: absolute;
}
.dora-whisker.left-1 { left: 15px; top: 0; transform: rotate(-10deg); }
.dora-whisker.left-2 { left: 12px; top: 10px; transform: rotate(5deg); }
.dora-whisker.left-3 { left: 15px; top: 20px; transform: rotate(15deg); }
.dora-whisker.right-1 { right: 15px; top: 0; transform: rotate(10deg); }
.dora-whisker.right-2 { right: 12px; top: 10px; transform: rotate(-5deg); }
.dora-whisker.right-3 { right: 15px; top: 20px; transform: rotate(-15deg); }

/* 铃铛 */
.dora-bell {
    width: 26px;
    height: 26px;
    background: #FFD700;
    border-radius: 50%;
    position: absolute;
    top: 155px;
    left: 50%;
    transform: translateX(-50%);
    border: 2px solid #B8860B;
    box-shadow: 0 2px 4px rgba(0,0,0,0.2);
}
.dora-bell::before {
    content: '';
    position: absolute;
    top: 50%;
    left: 0;
    width: 100%;
    height: 2px;
    background: #B8860B;
    transform: translateY(-50%);
}
.dora-bell::after {
    content: '';
    position: absolute;
    bottom: -4px;
    left: 50%;
    transform: translateX(-50%);
    width: 8px;
    height: 8px;
    background: #B8860B;
    border-radius: 50%;
}

/* "点我~" 提示 */
.dora-hint {
    position: absolute;
    bottom: -5px;
    left: 50%;
    transform: translateX(-50%);
    font-size: 14px;
    color: #fff;
    background: rgba(0,0,0,0.3);
    padding: 3px 10px;
    border-radius: 10px;
    white-space: nowrap;
    animation: hintFade 5s ease forwards;
}

@keyframes hintFade {
    0%, 70% { opacity: 1; }
    100% { opacity: 0; }
}

/* Doraemon 跳跃动画 */
#doraemon.jumping {
    animation: doraJump 1.5s cubic-bezier(0.34, 1.56, 0.64, 1);
}

@keyframes doraJump {
    0% { transform: translateY(0) rotate(0deg); }
    30% { transform: translateY(-120px) translateX(-200px) rotate(-10deg); }
    50% { transform: translateY(-140px) translateX(calc(-50vw + 100px)) rotate(5deg); }
    70% { transform: translateY(-120px) translateX(calc(-50vw + 100px)) rotate(-3deg); }
    100% { transform: translateY(0) rotate(0deg); }
}

/* 气泡框 */
.dora-bubble {
    position: fixed;
    background: #fff;
    border-radius: 20px;
    padding: 12px 20px;
    font-size: 16px;
    color: #333;
    box-shadow: 0 4px 15px rgba(0,0,0,0.15);
    z-index: 25;
    opacity: 0;
    transform: scale(0);
    transition: all 0.3s cubic-bezier(0.34, 1.56, 0.64, 1);
    pointer-events: none;
}
.dora-bubble.show {
    opacity: 1;
    transform: scale(1);
}
.dora-bubble::after {
    content: '';
    position: absolute;
    bottom: -10px;
    left: 30px;
    width: 0;
    height: 0;
    border-left: 10px solid transparent;
    border-right: 10px solid transparent;
    border-top: 12px solid #fff;
}
```

- [ ] **步骤 2：添加 Doraemon HTML**

在 `<div id="garden-scene">` 内部末尾（`</div>` 之前）添加：

```html
<!-- Doraemon -->
<div id="doraemon">
    <div class="dora-head">
        <div class="dora-face"></div>
        <div class="dora-eyes">
            <div class="dora-eye left">
                <div class="dora-pupil"></div>
            </div>
            <div class="dora-eye right">
                <div class="dora-pupil"></div>
            </div>
        </div>
        <div class="dora-nose"></div>
        <div class="dora-mouth"></div>
        <div class="dora-whiskers">
            <div class="dora-whisker left-1"></div>
            <div class="dora-whisker left-2"></div>
            <div class="dora-whisker left-3"></div>
            <div class="dora-whisker right-1"></div>
            <div class="dora-whisker right-2"></div>
            <div class="dora-whisker right-3"></div>
        </div>
        <div class="dora-bell"></div>
    </div>
    <div class="dora-hint">点我~</div>
</div>

<!-- Doraemon 气泡框 -->
<div class="dora-bubble" id="dora-bubble"></div>
```

- [ ] **步骤 3：在浏览器中验证**

进入花园后，确认：
- 右下角显示蓝色圆形的 Doraemon 头像
- 有白色脸、红鼻子、嘴巴、胡须、金色铃铛
- 眼睛每隔几秒眨一下
- 下方有"点我~"提示，5秒后淡出

- [ ] **步骤 4：Commit**

```bash
git add index.html
git commit -m "feat: Doraemon CSS绘制 + 眨眼动画"
```

---

## 任务 6：Doraemon 点击互动 + 礼物按钮

**文件：**
- 修改：`F:\birthday\index.html`（CSS + HTML + JS）

- [ ] **步骤 1：添加礼物按钮 CSS**

在 `<style>` 标签内添加：

```css
/* ---------- 礼物按钮 ---------- */
#gift-btn {
    position: fixed;
    bottom: 30px;
    right: 240px;
    width: 50px;
    height: 50px;
    background: linear-gradient(135deg, #FF6B9D, #E91E7C);
    border: none;
    border-radius: 50%;
    font-size: 24px;
    cursor: pointer;
    z-index: 18;
    box-shadow: 0 4px 15px rgba(233, 30, 124, 0.4);
    transition: transform 0.3s ease, box-shadow 0.3s ease;
    animation: giftBtnPulse 2s ease-in-out infinite;
}

#gift-btn:hover {
    transform: scale(1.1);
    box-shadow: 0 6px 20px rgba(233, 30, 124, 0.6);
}

@keyframes giftBtnPulse {
    0%, 100% { box-shadow: 0 4px 15px rgba(233, 30, 124, 0.4); }
    50% { box-shadow: 0 4px 25px rgba(233, 30, 124, 0.7); }
}
```

- [ ] **步骤 2：添加礼物按钮 HTML**

在 Doraemon 的 `</div>` 后面添加：

```html
<!-- 礼物按钮 -->
<button id="gift-btn">🎁</button>
```

- [ ] **步骤 3：添加 Doraemon 互动 JS**

在 `<script>` 标签内添加：

```javascript
/* ============================================
   🐱 Doraemon 互动逻辑
   ============================================ */
const doraemon = document.getElementById('doraemon');
const doraBubble = document.getElementById('dora-bubble');
const giftBtn = document.getElementById('gift-btn');

// Doraemon 点击 → 跳到中间送祝福
doraemon.addEventListener('click', (e) => {
    e.stopPropagation(); // 防止触发花园点击特效
    if (doraemon.classList.contains('jumping')) return; // 防止连续点击

    doraemon.classList.add('jumping');

    // 显示气泡框
    doraBubble.textContent = DORAEMON_BLESS;
    doraBubble.style.left = '50%';
    doraBubble.style.top = '30%';
    doraBubble.style.transform = 'translateX(-50%) scale(0)';
    setTimeout(() => {
        doraBubble.classList.add('show');
        doraBubble.style.transform = 'translateX(-50%) scale(1)';
    }, 400);

    // 1.5秒后跳回 + 气泡消失
    setTimeout(() => {
        doraBubble.classList.remove('show');
    }, 1500);

    // 动画结束后移除class
    setTimeout(() => {
        doraemon.classList.remove('jumping');
    }, 1500);
});

// 礼物按钮点击 → 触发礼物揭晓
giftBtn.addEventListener('click', (e) => {
    e.stopPropagation();
    if (giftTriggered) return;
    triggerGiftReveal();
});
```

- [ ] **步骤 4：在浏览器中验证**

- 点击 Doraemon → 它跳到屏幕中央，头顶显示气泡框 "LSQ，生日快乐呀！"
- 1.5秒后跳回右下角，气泡消失
- Doraemon 旁边有粉色圆形礼物按钮 🎁
- 点击礼物按钮暂时没反应（任务7实现）

- [ ] **步骤 5：Commit**

```bash
git add index.html
git commit -m "feat: Doraemon点击互动 + 礼物按钮"
```

---

## 任务 7：礼物揭晓流程（完整动画序列）

**文件：**
- 修改：`F:\birthday\index.html`（CSS + HTML + JS）

- [ ] **步骤 1：添加礼物揭晓 CSS**

在 `<style>` 标签内添加：

```css
/* ---------- 礼物揭晓 ---------- */
#gift-scene {
    background: rgba(0, 0, 0, 0.6);
    z-index: 30;
    flex-direction: column;
}

.gift-card {
    background: linear-gradient(160deg, #fff 0%, #FFF8F0 100%);
    border-radius: 20px;
    padding: 50px 40px;
    text-align: center;
    position: relative;
    box-shadow: 0 20px 60px rgba(0,0,0,0.3);
    min-width: 320px;
    max-width: 90vw;
}

.gift-card-text {
    font-size: 28px;
    font-weight: 600;
    color: #FF8C69;
    margin-bottom: 12px;
}

.gift-card-sub {
    font-size: 16px;
    color: #CCAA88;
    letter-spacing: 1px;
}

.gift-card-hint {
    margin-top: 30px;
    font-size: 14px;
    color: #999;
}

/* 擦除Canvas覆盖层 */
.scratch-overlay {
    position: absolute;
    top: 0; left: 0;
    width: 100%; height: 100%;
    border-radius: 20px;
    z-index: 2;
}

/* Doraemon在揭晓画面中 */
.gift-dora {
    position: absolute;
    bottom: -60px;
    right: -40px;
    width: 120px;
    height: 130px;
    transform: scale(0.6);
    pointer-events: none;
}
```

- [ ] **步骤 2：添加礼物揭晓 HTML**

将 `<div id="gift-scene" class="scene hidden">` 内部替换为：

```html
<div id="gift-scene" class="scene hidden">
    <div class="gift-card" id="gift-card">
        <div class="gift-card-text" id="gift-card-text"></div>
        <div class="gift-card-sub" id="gift-card-sub"></div>
        <canvas class="scratch-overlay" id="scratch-canvas"></canvas>
        <!-- 小号Doraemon -->
        <div class="gift-dora">
            <div class="dora-head">
                <div class="dora-face"></div>
                <div class="dora-eyes">
                    <div class="dora-eye left"><div class="dora-pupil"></div></div>
                    <div class="dora-eye right"><div class="dora-pupil"></div></div>
                </div>
                <div class="dora-nose"></div>
                <div class="dora-mouth"></div>
                <div class="dora-bell"></div>
            </div>
        </div>
    </div>
    <div class="gift-card-hint" id="gift-card-hint"></div>
</div>
```

- [ ] **步骤 3：添加礼物揭晓 JS 逻辑**

在 `<script>` 标签内添加：

```javascript
/* ============================================
   🎁 礼物揭晓逻辑
   ============================================ */
const giftScene = document.getElementById('gift-scene');
const giftCardText = document.getElementById('gift-card-text');
const giftCardSub = document.getElementById('gift-card-sub');
const giftCardHint = document.getElementById('gift-card-hint');
const scratchCanvas = document.getElementById('scratch-canvas');

// 填充礼物文字
giftCardText.textContent = GIFT_TEXT;
giftCardSub.textContent = GIFT_SUB_TEXT;
giftCardHint.textContent = '快去查收吧！';

/**
 * 触发礼物揭晓完整流程
 */
function triggerGiftReveal() {
    if (giftTriggered) return;
    giftTriggered = true;

    // 阶段1：Doraemon跳到中间送祝福
    doraemon.classList.add('jumping');
    doraBubble.textContent = DORAEMON_BLESS;
    doraBubble.style.left = '50%';
    doraBubble.style.top = '30%';
    doraBubble.style.transform = 'translateX(-50%) scale(0)';
    setTimeout(() => {
        doraBubble.classList.add('show');
        doraBubble.style.transform = 'translateX(-50%) scale(1)';
    }, 400);

    // 阶段2：跳回后显示气泡 "有个快递给你"
    setTimeout(() => {
        doraBubble.classList.remove('show');
        doraemon.classList.remove('jumping');
    }, 1500);

    setTimeout(() => {
        doraBubble.textContent = DORAEMON_SAY;
        doraBubble.style.top = '25%';
        setTimeout(() => {
            doraBubble.classList.add('show');
            doraBubble.style.transform = 'translateX(-50%) scale(1)';
        }, 100);
    }, 2000);

    // 阶段3：气泡消失，显示礼物卡片
    setTimeout(() => {
        doraBubble.classList.remove('show');
        giftScene.classList.remove('hidden');
        // 开始擦除效果
        setTimeout(() => initScratch(), 500);
    }, 3500);
}

/**
 * Canvas擦除效果 - 从中心向外扩散
 */
function initScratch() {
    const card = document.getElementById('gift-card');
    const canvas = scratchCanvas;
    const ctx = canvas.getContext('2d');
    const rect = card.getBoundingClientRect();

    // 设置Canvas尺寸
    const w = Math.round(rect.width);
    const h = Math.round(rect.height);
    canvas.width = w;
    canvas.height = h;
    canvas.style.width = w + 'px';
    canvas.style.height = h + 'px';

    // 绘制金色涂层
    ctx.fillStyle = '#C9A96E';
    ctx.fillRect(0, 0, w, h);

    // 涂层纹理
    ctx.fillStyle = 'rgba(255, 215, 0, 0.3)';
    for (let i = 0; i < 30; i++) {
        ctx.beginPath();
        ctx.arc(
            Math.random() * w,
            Math.random() * h,
            Math.random() * 3 + 1,
            0, Math.PI * 2
        );
        ctx.fill();
    }

    // 涂层文字
    ctx.fillStyle = 'rgba(255, 255, 255, 0.6)';
    ctx.font = '18px Baloo 2';
    ctx.textAlign = 'center';
    ctx.fillText('✨ 刮开查看 ✨', w / 2, h / 2);

    // 开始擦除动画
    animateScratch(ctx, w, h);
}

function animateScratch(ctx, w, h) {
    const cx = w / 2;
    const cy = h / 2;
    const duration = 1500;
    const startTime = performance.now();

    function step(now) {
        const t = Math.min((now - startTime) / duration, 1);
        const ease = 1 - Math.pow(1 - t, 3); // easeOutCubic
        const radius = Math.max(w, h) * ease;

        ctx.save();
        ctx.globalCompositeOperation = 'destination-out';
        ctx.beginPath();
        ctx.arc(cx, cy, radius, 0, Math.PI * 2);
        ctx.fillStyle = 'rgba(0,0,0,1)';
        ctx.fill();
        ctx.restore();

        if (t < 1) {
            requestAnimationFrame(step);
        } else {
            // 擦除完成，淡出canvas
            canvas.style.transition = 'opacity 0.5s ease';
            canvas.style.opacity = '0';
        }
    }

    requestAnimationFrame(step);
}
```

- [ ] **步骤 4：添加自动触发定时器**

在启动逻辑区域添加：

```javascript
/**
 * 启动礼物自动触发计时器
 */
function startGiftTimer() {
    setTimeout(() => {
        if (!giftTriggered) {
            triggerGiftReveal();
        }
    }, INTERACTIVE_DURATION * 1000);
}
```

- [ ] **步骤 5：在浏览器中验证**

完整流程测试：
1. 打开页面 → 贺卡弹出
2. 点击贺卡 → 3D翻页 → 进入花园
3. 在花园中点击触发特效（15秒内）
4. 15秒后自动触发：
   - Doraemon跳到中间说"LSQ，生日快乐呀！"
   - 跳回后气泡显示"LSQ，有个快递给你！"
   - 显示礼物卡片（金色涂层覆盖）
   - 涂层从中心向外擦除
   - 露出"快递ing，注意查收哦 📦"
5. 也可随时点击🎁按钮手动触发

- [ ] **步骤 6：Commit**

```bash
git add index.html
git commit -m "feat: 礼物揭晓完整流程（Doraemon送祝福 → 气泡 → 擦除 → 揭晓）"
```

---

## 任务 8：音频系统（背景音乐 + 音效 + 静音按钮）

**文件：**
- 修改：`F:\birthday\index.html`（CSS + HTML + JS）

- [ ] **步骤 1：添加静音按钮 CSS**

在 `<style>` 标签内添加：

```css
/* ---------- 静音按钮 ---------- */
#mute-btn {
    position: fixed;
    bottom: 20px;
    left: 20px;
    width: 44px;
    height: 44px;
    background: rgba(255, 255, 255, 0.2);
    backdrop-filter: blur(10px);
    border: 1px solid rgba(255, 255, 255, 0.3);
    border-radius: 50%;
    font-size: 20px;
    cursor: pointer;
    z-index: 100;
    display: flex;
    align-items: center;
    justify-content: center;
    transition: background 0.3s ease;
}

#mute-btn:hover {
    background: rgba(255, 255, 255, 0.35);
}
```

- [ ] **步骤 2：添加静音按钮 HTML**

在 `<body>` 内最后（`<script>` 之前）添加：

```html
<!-- 静音按钮 -->
<button id="mute-btn">🔊</button>
```

- [ ] **步骤 3：添加音频系统 JS**

在 `<script>` 标签内添加：

```javascript
/* ============================================
   🔊 音频系统
   ============================================ */
const muteBtn = document.getElementById('mute-btn');
let audioCtx = null;
let bgmGain = null;
let isMuted = false;
let audioInitialized = false;

// CDN 音频链接（使用公开的音效资源）
const BGM_URL = 'https://cdn.jsdelivr.net/gh/nicehash/Coin Sounds@main/sounds/coin-drop.mp3';
// 注意：这里使用通用音效CDN，实际部署时可替换为合适的Happy Birthday旋律

/**
 * 初始化音频上下文（需要用户交互后调用）
 */
function initAudio() {
    if (audioInitialized) return;
    audioInitialized = true;

    try {
        audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        bgmGain = audioCtx.createGain();
        bgmGain.connect(audioCtx.destination);
        bgmGain.gain.value = 0.3;
    } catch (e) {
        console.log('Web Audio API 不可用');
    }
}

/**
 * 播放点击音效（用Web Audio API合成简单的"啵"声）
 */
function playClickSound() {
    if (isMuted || !audioCtx) return;

    const osc = audioCtx.createOscillator();
    const gain = audioCtx.createGain();
    osc.connect(gain);
    gain.connect(audioCtx.destination);

    osc.frequency.setValueAtTime(800, audioCtx.currentTime);
    osc.frequency.exponentialRampToValueAtTime(400, audioCtx.currentTime + 0.1);
    gain.gain.setValueAtTime(0.3, audioCtx.currentTime);
    gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + 0.15);

    osc.start(audioCtx.currentTime);
    osc.stop(audioCtx.currentTime + 0.15);
}

/**
 * 播放揭晓音效（魔法叮咚声）
 */
function playRevealSound() {
    if (isMuted || !audioCtx) return;

    const notes = [523, 659, 784, 1047]; // C5 E5 G5 C6
    notes.forEach((freq, i) => {
        const osc = audioCtx.createOscillator();
        const gain = audioCtx.createGain();
        osc.connect(gain);
        gain.connect(audioCtx.destination);

        osc.type = 'sine';
        osc.frequency.value = freq;
        gain.gain.setValueAtTime(0.2, audioCtx.currentTime + i * 0.15);
        gain.gain.exponentialRampToValueAtTime(0.01, audioCtx.currentTime + i * 0.15 + 0.4);

        osc.start(audioCtx.currentTime + i * 0.15);
        osc.stop(audioCtx.currentTime + i * 0.15 + 0.4);
    });
}

// 静音按钮
muteBtn.addEventListener('click', () => {
    isMuted = !isMuted;
    muteBtn.textContent = isMuted ? '🔇' : '🔊';
    if (bgmGain) {
        bgmGain.gain.value = isMuted ? 0 : 0.3;
    }
});

// 首次交互时初始化音频
document.addEventListener('click', () => {
    if (!audioInitialized) initAudio();
}, { once: true });

document.addEventListener('touchstart', () => {
    if (!audioInitialized) initAudio();
}, { once: true });
```

- [ ] **步骤 4：在点击特效中集成音效**

修改花园点击事件处理函数，在 `spawnEmoji` 调用后添加音效：

找到花园点击事件中的 `spawnEmoji` 行，在其后添加：

```javascript
    spawnEmoji(e.clientX, e.clientY);
    spawnParticles(e.clientX, e.clientY);
    playClickSound(); // 添加这行
```

对触摸事件也做同样修改：

```javascript
    spawnEmoji(touch.clientX, touch.clientY);
    spawnParticles(touch.clientX, touch.clientY);
    playClickSound(); // 添加这行
```

- [ ] **步骤 5：在揭晓流程中集成音效**

在 `triggerGiftReveal` 函数中，在显示礼物卡片的位置（`giftScene.classList.remove('hidden')` 之前）添加：

```javascript
    playRevealSound(); // 添加这行
    giftScene.classList.remove('hidden');
```

- [ ] **步骤 6：在浏览器中验证**

- 页面左下角有 🔊 按钮
- 点击花园时有"啵"的音效
- 礼物揭晓时有叮咚音效
- 点击 🔊 切换为 🔇，再点击无音效
- 音频在首次交互后才激活（遵守浏览器策略）

- [ ] **步骤 7：Commit**

```bash
git add index.html
git commit -m "feat: 音频系统（Web Audio合成音效 + 静音按钮）"
```

---

## 任务 9：响应式适配 + 响应式优化

**文件：**
- 修改：`F:\birthday\index.html`（CSS）

- [ ] **步骤 1：添加响应式媒体查询**

在 `<style>` 标签末尾（`</style>` 之前）添加：

```css
/* ============================================
   📱 响应式适配
   ============================================ */

/* 平板竖屏 */
@media (max-width: 768px) {
    .greeting-card {
        width: 280px;
        min-height: 380px;
        padding: 30px 24px;
    }
    .card-name { font-size: 38px; }
    .card-greeting { font-size: 22px; }
    .card-date { font-size: 14px; }

    #doraemon {
        width: 150px;
        height: 165px;
        bottom: 20px;
        right: 20px;
    }
    .dora-head { width: 135px; height: 128px; }
    .dora-face { width: 105px; height: 90px; }
    .dora-eye { width: 30px; height: 34px; }
    .dora-pupil { width: 10px; height: 12px; }
    .dora-nose { width: 18px; height: 18px; top: 50px; }
    .dora-mouth { width: 60px; height: 30px; top: 68px; }
    .dora-bell { width: 20px; height: 20px; top: 118px; }
    .dora-whisker { width: 28px; }
    .dora-whisker.left-1 { left: 10px; }
    .dora-whisker.left-2 { left: 8px; }
    .dora-whisker.left-3 { left: 10px; }
    .dora-whisker.right-1 { right: 10px; }
    .dora-whisker.right-2 { right: 8px; }
    .dora-whisker.right-3 { right: 10px; }

    #gift-btn {
        right: 180px;
        bottom: 20px;
        width: 42px;
        height: 42px;
        font-size: 20px;
    }

    .gift-card {
        padding: 35px 28px;
        min-width: 280px;
    }
    .gift-card-text { font-size: 22px; }
    .gift-card-sub { font-size: 14px; }
}

/* 手机竖屏 */
@media (max-width: 480px) {
    .greeting-card {
        width: 260px;
        min-height: 350px;
        padding: 25px 20px;
    }
    .card-name { font-size: 32px; }
    .card-greeting { font-size: 20px; }
    .card-date { font-size: 13px; }
    .card-deco { font-size: 24px; }
    .card-heart { font-size: 32px; }

    #doraemon {
        width: 120px;
        height: 132px;
        bottom: 15px;
        right: 15px;
    }
    .dora-head { width: 108px; height: 102px; }
    .dora-face { width: 84px; height: 72px; }
    .dora-eye { width: 24px; height: 27px; }
    .dora-pupil { width: 8px; height: 10px; }
    .dora-nose { width: 14px; height: 14px; top: 40px; }
    .dora-mouth { width: 48px; height: 24px; top: 54px; }
    .dora-bell { width: 16px; height: 16px; top: 94px; }
    .dora-whiskers { top: 68px; }
    .dora-whisker { width: 22px; }

    #gift-btn {
        right: 140px;
        bottom: 15px;
        width: 38px;
        height: 38px;
        font-size: 18px;
    }

    .gift-card {
        padding: 28px 20px;
        min-width: 260px;
    }
    .gift-card-text { font-size: 20px; }
    .gift-card-sub { font-size: 13px; }

    .click-emoji { font-size: 32px; }
    .click-emoji.rainbow { font-size: 48px; }

    .cloud-1 { width: 90px; height: 30px; }
    .cloud-2 { width: 75px; height: 26px; }
    .cloud-3 { width: 65px; height: 22px; }

    .bush-1 { width: 70px; height: 50px; }
    .bush-2 { width: 60px; height: 42px; }
    .bush-3 { width: 85px; height: 58px; }
}

/* 横屏适配 */
@media (orientation: landscape) and (max-height: 500px) {
    .greeting-card {
        width: 240px;
        min-height: 280px;
        padding: 20px;
    }
    .card-name { font-size: 28px; }
    .card-greeting { font-size: 18px; }
    .card-deco { font-size: 20px; }
    .card-heart { font-size: 28px; }
    .card-hint { bottom: 10px; font-size: 12px; }

    #doraemon {
        width: 100px;
        height: 110px;
        bottom: 10px;
        right: 10px;
    }
    .dora-head { width: 90px; height: 85px; }
    .dora-face { width: 70px; height: 60px; }
    .dora-eye { width: 20px; height: 22px; }
    .dora-pupil { width: 7px; height: 8px; }
    .dora-nose { width: 12px; height: 12px; top: 34px; }
    .dora-mouth { width: 40px; height: 20px; top: 46px; }
    .dora-bell { width: 14px; height: 14px; top: 78px; }
    .dora-whiskers { top: 56px; }
    .dora-whisker { width: 18px; }

    #gift-btn {
        right: 120px;
        bottom: 10px;
        width: 34px;
        height: 34px;
        font-size: 16px;
    }

    .gift-card {
        padding: 24px 20px;
    }
    .gift-card-text { font-size: 18px; }

    .ground { height: 25%; }
    .flower { bottom: 23%; }
}
```

- [ ] **步骤 2：在浏览器中验证**

- 缩小浏览器窗口至手机宽度 → 贺卡、Doraemon、礼物按钮都缩小
- 切换到横屏 → 布局自适应，元素不会溢出
- 在手机上打开 → 触摸事件正常工作

- [ ] **步骤 3：Commit**

```bash
git add index.html
git commit -m "feat: 响应式适配（平板/手机/横屏）"
```

---

## 任务 10：最终整合测试 + 性能优化

**文件：**
- 修改：`F:\birthday\index.html`（微调）

- [ ] **步骤 1：完整流程测试清单**

逐项测试以下场景：

| # | 场景 | 预期结果 |
|---|------|----------|
| 1 | 页面加载 | 贺卡从下方弹入，显示"LSQ"+"生日快乐"+"2026.6.26" |
| 2 | 贺卡摇摆 | 微微左右摇摆，底部有"👆 点击我"提示 |
| 3 | 点击贺卡 | 3D翻页效果，贺卡向左翻开消失 |
| 4 | 花园显示 | 暖色天空+白云飘动+草地+花朵+树丛 |
| 5 | 点击花园 | 随机emoji弹出+粒子爆发+音效 |
| 6 | 连续点击 | 每次只出一种emoji，不会叠加卡顿 |
| 7 | Doraemon | 右下角显示，眼睛会眨，有"点我~"提示 |
| 8 | 点击Doraemon | 跳到中间，气泡显示"LSQ，生日快乐呀！" |
| 9 | 礼物按钮 | Doraemon旁有粉色🎁按钮 |
| 10 | 15秒自动触发 | Doraemon送祝福→气泡→礼物卡片→擦除→揭晓 |
| 11 | 手动触发 | 点击🎁按钮触发同样流程 |
| 12 | 擦除效果 | 金色涂层从中心向外扩散消失 |
| 13 | 揭晓文字 | "快递ing，注意查收哦 📦" + "2026.6.26 · LSQ的专属生日礼物" |
| 14 | 静音按钮 | 左下角🔊/🔇切换正常 |
| 15 | 手机竖屏 | 所有元素缩小但完整显示 |
| 16 | 手机横屏 | 布局自适应，不溢出 |

- [ ] **步骤 2：性能检查**

- 打开浏览器开发者工具 Performance 面板
- 录制一次完整流程（15秒互动+揭晓）
- 检查帧率是否稳定在 50fps 以上
- 检查是否有内存泄漏（DOM元素是否被正确移除）

- [ ] **步骤 3：最终 Commit**

```bash
git add index.html
git commit -m "feat: 花园生日互动网页完成 — LSQ 2026.6.26"
```

- [ ] **步骤 4：输出最终文件路径**

完成所有任务后，最终文件位于：

```
F:\birthday\index.html
```

用浏览器直接打开即可体验完整效果。
