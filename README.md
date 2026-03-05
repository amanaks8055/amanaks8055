<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>GitHub City — Aman Sharma</title>
<link href="https://fonts.googleapis.com/css2?family=Press+Start+2P&family=VT323&family=Share+Tech+Mono&display=swap" rel="stylesheet">
<style>
* { margin:0; padding:0; box-sizing:border-box; }

:root {
  --sky1: #0a0015;
  --sky2: #0d0028;
  --neon1: #ff2d78;
  --neon2: #00ffe7;
  --neon3: #ffe600;
  --neon4: #a855f7;
  --neon5: #3b82f6;
  --ground: #0f0020;
}

body {
  background: var(--sky1);
  overflow: hidden;
  font-family: 'Share Tech Mono', monospace;
  height: 100vh;
  width: 100vw;
  cursor: none;
}

/* CUSTOM CURSOR */
.cursor {
  width: 16px; height: 16px;
  border: 2px solid var(--neon2);
  position: fixed;
  pointer-events: none;
  z-index: 99999;
  transform: translate(-50%,-50%);
  transition: transform 0.1s;
  image-rendering: pixelated;
}
.cursor::before {
  content:'';
  position:absolute;
  top:50%;left:50%;
  transform:translate(-50%,-50%);
  width:4px;height:4px;
  background:var(--neon2);
}

/* MAIN CANVAS */
#cityCanvas {
  position: fixed;
  inset: 0;
  width: 100%;
  height: 100%;
}

/* SCANLINES */
.scanlines {
  position: fixed;
  inset: 0;
  background: repeating-linear-gradient(
    0deg,
    transparent,
    transparent 3px,
    rgba(0,0,0,0.08) 3px,
    rgba(0,0,0,0.08) 4px
  );
  pointer-events: none;
  z-index: 10;
}

/* VIGNETTE */
.vignette {
  position: fixed;
  inset: 0;
  background: radial-gradient(ellipse at center, transparent 50%, rgba(0,0,0,0.7) 100%);
  pointer-events: none;
  z-index: 11;
}

/* HUD OVERLAY — top */
.hud-top {
  position: fixed;
  top: 0; left: 0; right: 0;
  z-index: 50;
  padding: 16px 24px;
  display: flex;
  align-items: center;
  justify-content: space-between;
  background: linear-gradient(180deg, rgba(0,0,0,0.8) 0%, transparent 100%);
}

.hud-logo {
  font-family: 'Press Start 2P', monospace;
  font-size: clamp(0.55rem, 1.5vw, 0.85rem);
  color: var(--neon2);
  text-shadow: 0 0 10px var(--neon2), 0 0 20px var(--neon2);
  letter-spacing: 2px;
  animation: flicker 8s infinite;
}

@keyframes flicker {
  0%,95%,97%,100% { opacity:1; }
  96% { opacity:0.3; }
}

.hud-stats {
  display: flex;
  gap: 20px;
}

.hud-stat {
  text-align: center;
  font-family: 'VT323', monospace;
  font-size: 1.1rem;
}

.hud-stat-val {
  color: var(--neon3);
  font-size: 1.4rem;
  text-shadow: 0 0 8px var(--neon3);
}

.hud-stat-label {
  color: rgba(255,255,255,0.4);
  font-size: 0.65rem;
  font-family: 'Share Tech Mono', monospace;
  letter-spacing: 1px;
}

/* BOTTOM PANEL */
.panel {
  position: fixed;
  bottom: 0; left: 0; right: 0;
  z-index: 50;
  background: linear-gradient(0deg, rgba(0,0,0,0.95) 0%, rgba(0,0,10,0.85) 80%, transparent 100%);
  padding: 20px 24px 24px;
}

.panel-title {
  font-family: 'Press Start 2P', monospace;
  font-size: clamp(0.45rem, 1.2vw, 0.7rem);
  color: var(--neon1);
  text-shadow: 0 0 12px var(--neon1);
  letter-spacing: 2px;
  margin-bottom: 14px;
  display: flex;
  align-items: center;
  gap: 10px;
}

.blink { animation: blink 1s step-end infinite; }
@keyframes blink { 50% { opacity:0; } }

.steps-row {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 10px;
  margin-bottom: 14px;
}

.step-tile {
  background: rgba(255,255,255,0.03);
  border: 1px solid rgba(255,255,255,0.08);
  border-radius: 4px;
  padding: 10px 12px;
  display: flex;
  align-items: flex-start;
  gap: 10px;
  transition: all 0.3s;
  cursor: pointer;
  text-decoration: none;
  color: inherit;
  image-rendering: pixelated;
}

.step-tile:hover {
  border-color: var(--neon2);
  background: rgba(0,255,231,0.06);
  box-shadow: 0 0 16px rgba(0,255,231,0.15), inset 0 0 16px rgba(0,255,231,0.04);
}

.step-tile.done { border-color: rgba(0,255,100,0.4); background: rgba(0,255,100,0.05); }

.step-icon {
  font-family: 'VT323', monospace;
  font-size: 1.5rem;
  color: var(--neon4);
  text-shadow: 0 0 8px var(--neon4);
  min-width: 24px;
}

.step-tile.done .step-icon { color: #00ff64; text-shadow: 0 0 8px #00ff64; }

.step-info h4 {
  font-family: 'Share Tech Mono', monospace;
  font-size: 0.65rem;
  color: #fff;
  letter-spacing: 1px;
  margin-bottom: 3px;
}

.step-info p {
  font-family: 'VT323', monospace;
  font-size: 0.85rem;
  color: rgba(255,255,255,0.35);
  line-height: 1.2;
}

.btn-row { display: flex; gap: 10px; }

.btn-copy {
  flex: 1;
  padding: 14px 20px;
  font-family: 'Press Start 2P', monospace;
  font-size: clamp(0.4rem, 1vw, 0.6rem);
  letter-spacing: 1px;
  color: #000;
  background: var(--neon1);
  border: none;
  border-radius: 3px;
  cursor: pointer;
  position: relative;
  overflow: hidden;
  transition: all 0.2s;
  text-transform: uppercase;
  box-shadow: 0 0 20px rgba(255,45,120,0.5), 0 4px 0 #8b0037;
}

.btn-copy::before {
  content: '';
  position: absolute;
  top: 0; left: -100%;
  width: 60%; height: 100%;
  background: linear-gradient(90deg, transparent, rgba(255,255,255,0.3), transparent);
  transition: left 0.5s;
}
.btn-copy:hover::before { left: 150%; }
.btn-copy:hover { transform: translateY(-2px); box-shadow: 0 0 30px rgba(255,45,120,0.8), 0 6px 0 #8b0037; }
.btn-copy:active { transform: translateY(2px); box-shadow: 0 0 20px rgba(255,45,120,0.5), 0 2px 0 #8b0037; }
.btn-copy.done { background: #00ff64; box-shadow: 0 0 20px rgba(0,255,100,0.5), 0 4px 0 #006428; color: #000; }

.btn-github {
  flex: 1;
  padding: 14px 20px;
  font-family: 'Press Start 2P', monospace;
  font-size: clamp(0.4rem, 1vw, 0.6rem);
  letter-spacing: 1px;
  color: var(--neon2);
  background: transparent;
  border: 2px solid var(--neon2);
  border-radius: 3px;
  cursor: pointer;
  transition: all 0.2s;
  text-decoration: none;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 0 16px rgba(0,255,231,0.2), inset 0 0 16px rgba(0,255,231,0.05);
  text-transform: uppercase;
}
.btn-github:hover {
  background: rgba(0,255,231,0.1);
  box-shadow: 0 0 30px rgba(0,255,231,0.4), inset 0 0 20px rgba(0,255,231,0.1);
  transform: translateY(-2px);
}

/* TOAST */
.toast {
  position: fixed;
  top: 80px; left: 50%;
  transform: translateX(-50%) scale(0.8);
  opacity: 0;
  background: rgba(0,0,0,0.95);
  border: 2px solid #00ff64;
  border-radius: 3px;
  padding: 12px 24px;
  font-family: 'Press Start 2P', monospace;
  font-size: 0.55rem;
  color: #00ff64;
  text-shadow: 0 0 10px #00ff64;
  box-shadow: 0 0 30px rgba(0,255,100,0.4);
  z-index: 9999;
  letter-spacing: 1px;
  transition: all 0.3s cubic-bezier(0.175, 0.885, 0.32, 1.275);
}
.toast.show { opacity:1; transform: translateX(-50%) scale(1); }

/* MINI MAP */
.minimap {
  position: fixed;
  top: 70px; right: 20px;
  width: 120px; height: 80px;
  border: 1px solid rgba(0,255,231,0.3);
  background: rgba(0,0,0,0.6);
  z-index: 50;
  border-radius: 2px;
  overflow: hidden;
}
.minimap-label {
  position: absolute;
  bottom: 4px; left: 50%;
  transform: translateX(-50%);
  font-family: 'VT323', monospace;
  font-size: 0.7rem;
  color: rgba(0,255,231,0.5);
  letter-spacing: 1px;
  white-space: nowrap;
}

@media (max-width: 600px) {
  .hud-stats { gap: 10px; }
  .minimap { display: none; }
  .steps-row { grid-template-columns: 1fr; gap: 6px; }
  .step-tile { padding: 8px 10px; }
}
</style>
</head>
<body>

<div class="cursor" id="cursor"></div>
<canvas id="cityCanvas"></canvas>
<div class="scanlines"></div>
<div class="vignette"></div>

<!-- TOP HUD -->
<div class="hud-top">
  <div class="hud-logo">◈ GITHUB.CITY / AMAN_SHARMA ◈</div>
  <div class="hud-stats">
    <div class="hud-stat">
      <div class="hud-stat-val">29</div>
      <div class="hud-stat-label">REPOS</div>
    </div>
    <div class="hud-stat">
      <div class="hud-stat-val">∞</div>
      <div class="hud-stat-label">POWER</div>
    </div>
    <div class="hud-stat">
      <div class="hud-stat-val" id="livetime">--:--</div>
      <div class="hud-stat-label">CITY TIME</div>
    </div>
  </div>
</div>

<!-- MINIMAP -->
<div class="minimap">
  <canvas id="minimapCanvas" width="120" height="80"></canvas>
  <div class="minimap-label">AMAN'S CITY</div>
</div>

<!-- BOTTOM PANEL -->
<div class="panel">
  <div class="panel-title">
    <span class="blink">▶</span> PROFILE UPLOAD MISSION
  </div>

  <div class="steps-row">
    <div class="step-tile" id="st1" onclick="copyReadme()">
      <div class="step-icon">①</div>
      <div class="step-info">
        <h4>COPY CODE</h4>
        <p>Neeche button dabao → README copy</p>
      </div>
    </div>
    <a class="step-tile" id="st2" href="https://github.com/amanaks8055/amanaks8055/edit/main/README.md" target="_blank">
      <div class="step-icon">②</div>
      <div class="step-info">
        <h4>OPEN GITHUB</h4>
        <p>amanaks8055/amanaks8055 → ✏️ pencil</p>
      </div>
    </a>
    <div class="step-tile" id="st3">
      <div class="step-icon">③</div>
      <div class="step-info">
        <h4>PASTE & COMMIT</h4>
        <p>Ctrl+A → Del → Ctrl+V → Commit</p>
      </div>
    </div>
  </div>

  <div class="btn-row">
    <button class="btn-copy" id="copyBtn" onclick="copyReadme()">⚡ COPY README.MD</button>
    <a class="btn-github" href="https://github.com/amanaks8055/amanaks8055/edit/main/README.md" target="_blank">🔮 GITHUB EDITOR →</a>
  </div>
</div>

<div class="toast" id="toast">✓ COPIED! AB GITHUB MEIN PASTE KARO</div>

<script>
// ── CURSOR ──────────────────────────────────────────────────────
const cur = document.getElementById('cursor');
document.addEventListener('mousemove', e => {
  cur.style.left = e.clientX + 'px';
  cur.style.top = e.clientY + 'px';
});

// ── LIVE TIME ────────────────────────────────────────────────────
function updateTime() {
  const now = new Date();
  document.getElementById('livetime').textContent =
    String(now.getHours()).padStart(2,'0') + ':' + String(now.getMinutes()).padStart(2,'0');
}
setInterval(updateTime, 1000);
updateTime();

// ── CITY CANVAS ──────────────────────────────────────────────────
const canvas = document.getElementById('cityCanvas');
const ctx = canvas.getContext('2d');
let W, H;

function resize() {
  W = canvas.width = window.innerWidth;
  H = canvas.height = window.innerHeight;
}
resize();
window.addEventListener('resize', resize);

// Seeded random
function seededRand(seed) {
  let s = seed;
  return () => { s = (s * 9301 + 49297) % 233280; return s / 233280; };
}
const rng = seededRand(42);

// ── BUILDINGS ────────────────────────────────────────────────────
const NEON_COLORS = ['#ff2d78','#00ffe7','#ffe600','#a855f7','#3b82f6','#ff6b00','#00ff64'];
const SIGNS = ['AI','ML','REACT','FLUTTER','PYTHON','TS','NODE','AZURE','SQL','DOCKER','GIT','API'];

const buildings = [];
function generateBuildings() {
  buildings.length = 0;
  let x = 0;
  while (x < W + 80) {
    const w = 40 + rng() * 80;
    const h = 80 + rng() * (H * 0.55);
    const floors = Math.floor(h / 18);
    const color = NEON_COLORS[Math.floor(rng() * NEON_COLORS.length)];
    const windows = [];
    const cols = Math.floor((w - 8) / 14);
    for (let f = 0; f < floors; f++) {
      for (let c = 0; c < cols; c++) {
        windows.push({
          c, f,
          lit: rng() > 0.35,
          blink: rng() > 0.85,
          blinkPhase: rng() * Math.PI * 2
        });
      }
    }
    buildings.push({
      x, w, h,
      color,
      sign: rng() > 0.5 ? SIGNS[Math.floor(rng() * SIGNS.length)] : null,
      signColor: NEON_COLORS[Math.floor(rng() * NEON_COLORS.length)],
      windows,
      cols,
      antennaH: rng() > 0.6 ? 8 + rng() * 30 : 0,
      offset: rng() * 20
    });
    x += w + 2 + rng() * 4;
  }
}

// ── STARS ─────────────────────────────────────────────────────────
const stars = Array.from({length: 200}, () => ({
  x: rng() * 1, y: rng() * 0.55,
  r: rng() * 1.4 + 0.3,
  twinkle: rng() * Math.PI * 2,
  speed: rng() * 0.03 + 0.01
}));

// ── CARS ─────────────────────────────────────────────────────────
const GROUND_Y_FRAC = 0.78;
const CAR_COLORS = ['#ff2d78','#00ffe7','#ffe600','#ffffff','#ff6b00','#a855f7'];
const cars = Array.from({length: 12}, (_, i) => ({
  x: rng() * W,
  speed: 1.5 + rng() * 3.5,
  color: CAR_COLORS[Math.floor(rng() * CAR_COLORS.length)],
  lane: Math.floor(rng() * 2),
  w: 22 + rng() * 14, h: 8
}));

// ── RAIN ──────────────────────────────────────────────────────────
const rainDrops = Array.from({length: 180}, () => ({
  x: rng() * 1, y: rng() * 1,
  speed: 6 + rng() * 8,
  len: 10 + rng() * 14,
  opacity: 0.08 + rng() * 0.15
}));

// ── MOON ─────────────────────────────────────────────────────────
const moon = { x: 0.82, y: 0.1, r: 22 };

// ── DRAW ─────────────────────────────────────────────────────────
let t = 0;

function drawSky() {
  const g = ctx.createLinearGradient(0, 0, 0, H * GROUND_Y_FRAC);
  g.addColorStop(0, '#000008');
  g.addColorStop(0.5, '#0a0025');
  g.addColorStop(1, '#1a0035');
  ctx.fillStyle = g;
  ctx.fillRect(0, 0, W, H * GROUND_Y_FRAC);
}

function drawMoon() {
  const mx = moon.x * W, my = moon.y * H;
  // glow
  const glow = ctx.createRadialGradient(mx, my, 0, mx, my, moon.r * 4);
  glow.addColorStop(0, 'rgba(200,200,255,0.12)');
  glow.addColorStop(1, 'transparent');
  ctx.fillStyle = glow; ctx.beginPath();
  ctx.arc(mx, my, moon.r * 4, 0, Math.PI * 2); ctx.fill();
  // moon
  ctx.fillStyle = '#d4d4ff';
  ctx.beginPath(); ctx.arc(mx, my, moon.r, 0, Math.PI * 2); ctx.fill();
  // craters
  ctx.fillStyle = 'rgba(0,0,0,0.12)';
  ctx.beginPath(); ctx.arc(mx - 6, my - 4, 5, 0, Math.PI * 2); ctx.fill();
  ctx.beginPath(); ctx.arc(mx + 7, my + 5, 3, 0, Math.PI * 2); ctx.fill();
}

function drawStars() {
  stars.forEach(s => {
    const bri = 0.5 + 0.5 * Math.sin(s.twinkle + t * s.speed);
    ctx.beginPath();
    ctx.arc(s.x * W, s.y * H, s.r * bri, 0, Math.PI * 2);
    ctx.fillStyle = `rgba(255,255,255,${0.4 + 0.6 * bri})`;
    ctx.fill();
  });
}

function drawBuildings() {
  const groundY = H * GROUND_Y_FRAC;
  buildings.forEach(b => {
    const bx = b.x, by = groundY - b.h;

    // building body
    const bg = ctx.createLinearGradient(bx, by, bx + b.w, by);
    bg.addColorStop(0, 'rgba(10,0,25,0.98)');
    bg.addColorStop(0.5, 'rgba(15,5,35,0.98)');
    bg.addColorStop(1, 'rgba(8,0,20,0.98)');
    ctx.fillStyle = bg;
    ctx.fillRect(bx, by, b.w, b.h);

    // edge glow
    ctx.strokeStyle = b.color + '44';
    ctx.lineWidth = 1;
    ctx.strokeRect(bx, by, b.w, b.h);

    // windows
    b.windows.forEach(win => {
      const lit = win.blink
        ? (Math.sin(t * 2 + win.blinkPhase) > 0 ? win.lit : !win.lit)
        : win.lit;
      if (!lit) return;
      const wx = bx + 5 + win.c * 14;
      const wy = by + b.h - 16 - win.f * 18;
      if (wy < by + 2) return;
      const alpha = 0.6 + 0.4 * Math.sin(t + win.blinkPhase);
      ctx.fillStyle = `rgba(255,240,180,${alpha * 0.85})`;
      ctx.fillRect(wx, wy, 8, 10);
      // window glow
      ctx.shadowBlur = 6;
      ctx.shadowColor = '#ffe8a0';
      ctx.fillRect(wx, wy, 8, 10);
      ctx.shadowBlur = 0;
    });

    // antenna
    if (b.antennaH > 0) {
      ctx.strokeStyle = b.color;
      ctx.lineWidth = 1.5;
      ctx.beginPath();
      ctx.moveTo(bx + b.w / 2, by);
      ctx.lineTo(bx + b.w / 2, by - b.antennaH);
      ctx.stroke();
      // blinking light
      const on = Math.sin(t * 3 + b.offset) > 0;
      if (on) {
        ctx.shadowBlur = 10; ctx.shadowColor = '#ff2d78';
        ctx.fillStyle = '#ff2d78';
        ctx.beginPath();
        ctx.arc(bx + b.w / 2, by - b.antennaH, 2.5, 0, Math.PI * 2);
        ctx.fill();
        ctx.shadowBlur = 0;
      }
    }

    // neon sign
    if (b.sign) {
      const pulse = 0.7 + 0.3 * Math.sin(t * 1.5 + b.offset);
      ctx.shadowBlur = 12 * pulse;
      ctx.shadowColor = b.signColor;
      ctx.fillStyle = b.signColor;
      ctx.font = `bold ${Math.min(9, Math.floor(b.w / b.sign.length) - 1)}px 'Share Tech Mono'`;
      ctx.textAlign = 'center';
      ctx.fillText(b.sign, bx + b.w / 2, by + 20);
      ctx.shadowBlur = 0;
    }

    // top edge highlight
    ctx.fillStyle = b.color + '55';
    ctx.fillRect(bx, by, b.w, 2);
  });
}

function drawGround() {
  const gy = H * GROUND_Y_FRAC;
  // ground fill
  const g = ctx.createLinearGradient(0, gy, 0, H);
  g.addColorStop(0, '#0f0020');
  g.addColorStop(1, '#050010');
  ctx.fillStyle = g;
  ctx.fillRect(0, gy, W, H - gy);

  // road
  ctx.fillStyle = '#110022';
  ctx.fillRect(0, gy + 2, W, 36);
  ctx.fillStyle = '#1a0035';
  ctx.fillRect(0, gy + 38, W, 24);

  // road line
  ctx.setLineDash([30, 20]);
  ctx.strokeStyle = 'rgba(255,230,0,0.25)';
  ctx.lineWidth = 2;
  ctx.beginPath();
  ctx.moveTo(0, gy + 20);
  ctx.lineTo(W, gy + 20);
  ctx.stroke();
  ctx.setLineDash([]);

  // reflection glow on road
  buildings.forEach(b => {
    if (!b.color) return;
    const grd = ctx.createLinearGradient(b.x + b.w/2, gy, b.x + b.w/2, gy + 40);
    grd.addColorStop(0, b.color + '18');
    grd.addColorStop(1, 'transparent');
    ctx.fillStyle = grd;
    ctx.fillRect(b.x, gy, b.w, 40);
  });
}

function drawCars() {
  const gy = H * GROUND_Y_FRAC;
  cars.forEach(car => {
    car.x += car.speed;
    if (car.x > W + 60) car.x = -60;
    const cy = gy + 6 + car.lane * 20;
    const cx = car.x;

    // headlights glow
    ctx.shadowBlur = 18; ctx.shadowColor = car.color;
    ctx.fillStyle = car.color + 'cc';
    ctx.fillRect(cx + car.w - 4, cy + 1, 4, car.h - 2);
    ctx.shadowBlur = 0;

    // car body
    ctx.fillStyle = '#1a1a2e';
    ctx.fillRect(cx, cy, car.w, car.h);
    ctx.fillStyle = car.color + '88';
    ctx.fillRect(cx, cy, 2, car.h);

    // tail lights
    ctx.fillStyle = '#ff000099';
    ctx.fillRect(cx, cy + 1, 3, car.h - 2);
  });
}

function drawRain() {
  ctx.save();
  rainDrops.forEach(d => {
    d.y += d.speed / H;
    if (d.y > 1) { d.y = 0; d.x = Math.random(); }
    ctx.strokeStyle = `rgba(180,220,255,${d.opacity})`;
    ctx.lineWidth = 0.8;
    ctx.beginPath();
    ctx.moveTo(d.x * W, d.y * H);
    ctx.lineTo(d.x * W + 1, d.y * H + d.len);
    ctx.stroke();
  });
  ctx.restore();
}

function drawHorizonGlow() {
  const gy = H * GROUND_Y_FRAC;
  const g = ctx.createLinearGradient(0, gy - 60, 0, gy);
  g.addColorStop(0, 'transparent');
  g.addColorStop(1, 'rgba(168,85,247,0.12)');
  ctx.fillStyle = g;
  ctx.fillRect(0, gy - 60, W, 60);
}

function loop() {
  t += 0.016;
  ctx.clearRect(0, 0, W, H);
  drawSky();
  drawStars();
  drawMoon();
  drawBuildings();
  drawHorizonGlow();
  drawGround();
  drawCars();
  drawRain();
  drawMinimap();
  requestAnimationFrame(loop);
}

// ── MINIMAP ──────────────────────────────────────────────────────
const mmc = document.getElementById('minimapCanvas');
const mmctx = mmc.getContext('2d');

function drawMinimap() {
  mmctx.clearRect(0, 0, 120, 80);
  mmctx.fillStyle = '#05001a';
  mmctx.fillRect(0, 0, 120, 80);
  const scaleX = 120 / W, scaleY = 55 / H;
  buildings.forEach(b => {
    const gy = H * GROUND_Y_FRAC;
    mmctx.fillStyle = b.color + '88';
    mmctx.fillRect(b.x * scaleX, (gy - b.h) * scaleY, Math.max(b.w * scaleX, 2), b.h * scaleY);
  });
  // cars
  cars.forEach(car => {
    const gy = H * GROUND_Y_FRAC;
    mmctx.fillStyle = car.color;
    mmctx.fillRect(car.x * scaleX, gy * scaleY + 1, 3, 2);
  });
}

// ── README CONTENT ───────────────────────────────────────────────
const README = `<!-- Animated Typing Banner -->
<div align="center">

[![Typing SVG](https://readme-typing-svg.demolab.com?font=Fira+Code&weight=900&size=28&duration=3000&pause=1000&color=9370DB&background=0D1117FF&center=true&vCenter=true&multiline=true&repeat=true&width=800&height=200&lines=%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88%E2%96%88;LEGENDARY+DEVELOPER+%F0%9F%94%AE+SUSANOO+ACTIVATED;%F0%9F%94%AE+AMAN+SHARMA+%F0%9F%94%AE+SUSANOO+ACTIVATED+%F0%9F%94%AE)](https://git.io/typing-svg)

</div>

---

## 🌟 THE LEGENDARY CODE ARCHITECT

\`\`\`rust
struct LegendaryDeveloper {
    name: &'static str,
    title: Vec<&'static str>,
    forbidden_techniques: Vec<&'static str>,
}
impl LegendaryDeveloper {
    fn new() -> Self {
        Self {
            name: "Aman Sharma",
            title: vec!["AI Architect","Full Stack Sorcerer","Legendary Builder"],
            forbidden_techniques: vec![
                "🔥 AI-Powered App Rasengan",
                "⚡ Async TypeScript Chidori",
                "🌀 Deep Learning Amaterasu",
                "🔮 Flutter Mangekyo",
            ],
        }
    }
    fn activate_susanoo(&self) -> &str { "🔮 PERFECT SUSANOO ACTIVATED! 💜" }
}
let aman = LegendaryDeveloper::new();
println!("{}", aman.activate_susanoo());
// Output: 🔮 PERFECT SUSANOO ACTIVATED! 💜
\`\`\`

---

## ⚡ CURRENT POWER LEVEL

<div align="center">

![SUSANOO MODE](https://img.shields.io/badge/⚡_SUSANOO_MODE-PERFECT_FORM-blueviolet?style=for-the-badge&labelColor=4B0082)
![CHAKRA LEVEL](https://img.shields.io/badge/🔥_CHAKRA_LEVEL-INFINITE-FF6B35?style=for-the-badge&labelColor=8B0000)
![RARITY TIER](https://img.shields.io/badge/💎_RARITY_TIER-MYTHICAL-9370DB?style=for-the-badge&labelColor=4B0082)

</div>

---

## ⚡ LEGENDARY GITHUB DOMINANCE

<div align="center">

![Aman's GitHub Stats](https://github-readme-stats.vercel.app/api?username=amanaks8055&show_icons=true&theme=synthwave&hide_border=true&bg_color=0d1117,240046,10002b,240046,0d1117&title_color=9370db&icon_color=bb86fc&text_color=e1bee7&ring_color=9370db)

![Top Languages](https://github-readme-stats.vercel.app/api/top-langs/?username=amanaks8055&layout=compact&theme=synthwave&hide_border=true&bg_color=0d1117,240046,10002b,240046,0d1117&title_color=9370db&text_color=e1bee7&langs_count=8)

[![GitHub Streak](https://streak-stats.demolab.com?user=amanaks8055&theme=synthwave&hide_border=true&background=0D1117,240046,10002B,240046,0D1117&ring=9370DB&fire=BB86FC&currStreakLabel=E1BEE7&sideLabels=E1BEE7&dates=E1BEE7&stroke=9370DB&currStreakNum=BB86FC&sideNums=BB86FC)](https://git.io/streak-stats)

[![Activity Graph](https://github-readme-activity-graph.vercel.app/graph?username=amanaks8055&custom_title=⚡%20SUSANOO%20POWER%20FLOW%20CHART%20⚡&bg_color=0d1117&color=9370db&line=bb86fc&point=e1bee7&area=true&area_color=240046&title_color=9370db&hide_border=true)](https://github.com/amanaks8055)

</div>

---

## 💜 LEGENDARY TECH ARSENAL

### ⚡ FORBIDDEN LANGUAGES
<div align="center">

![JavaScript](https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black&labelColor=4B0082)
![TypeScript](https://img.shields.io/badge/TypeScript-3178C6?style=for-the-badge&logo=typescript&logoColor=white&labelColor=4B0082)
![Python](https://img.shields.io/badge/Python-3776AB?style=for-the-badge&logo=python&logoColor=white&labelColor=4B0082)
![Dart](https://img.shields.io/badge/Dart-0175C2?style=for-the-badge&logo=dart&logoColor=white&labelColor=4B0082)
![SQL](https://img.shields.io/badge/SQL-4479A1?style=for-the-badge&logo=mysql&logoColor=white&labelColor=4B0082)
![HTML5](https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white&labelColor=4B0082)
![CSS3](https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white&labelColor=4B0082)

</div>

### 🔥 SUSANOO FRONTEND MASTERY
<div align="center">

![React](https://img.shields.io/badge/React-20232A?style=for-the-badge&logo=react&logoColor=61DAFB&labelColor=240046)
![Flutter](https://img.shields.io/badge/Flutter-02569B?style=for-the-badge&logo=flutter&logoColor=white&labelColor=240046)
![Next.js](https://img.shields.io/badge/Next.js-000000?style=for-the-badge&logo=next.js&logoColor=white&labelColor=240046)
![Tailwind CSS](https://img.shields.io/badge/Tailwind_CSS-38B2AC?style=for-the-badge&logo=tailwind-css&logoColor=white&labelColor=240046)

</div>

### 💎 LEGENDARY BACKEND & AI POWERS
<div align="center">

![Node.js](https://img.shields.io/badge/Node.js-339933?style=for-the-badge&logo=node.js&logoColor=white&labelColor=10002B)
![FastAPI](https://img.shields.io/badge/FastAPI-009688?style=for-the-badge&logo=fastapi&logoColor=white&labelColor=10002B)
![OpenAI](https://img.shields.io/badge/OpenAI-412991?style=for-the-badge&logo=openai&logoColor=white&labelColor=10002B)
![TensorFlow](https://img.shields.io/badge/TensorFlow-FF6F00?style=for-the-badge&logo=tensorflow&logoColor=white&labelColor=10002B)
![OpenCV](https://img.shields.io/badge/OpenCV-5C3EE8?style=for-the-badge&logo=opencv&logoColor=white&labelColor=10002B)

</div>

### ☁️ CLOUD SUSANOO MASTERY
<div align="center">

![Azure](https://img.shields.io/badge/Azure-0078D4?style=for-the-badge&logo=microsoft-azure&logoColor=white&labelColor=240046)
![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazon-aws&logoColor=white&labelColor=240046)
![Docker](https://img.shields.io/badge/Docker-2496ED?style=for-the-badge&logo=docker&logoColor=white&labelColor=240046)
![ServiceNow](https://img.shields.io/badge/ServiceNow-81B5A1?style=for-the-badge&logo=servicenow&logoColor=white&labelColor=240046)
![Databricks](https://img.shields.io/badge/Databricks-FF3621?style=for-the-badge&logo=databricks&logoColor=white&labelColor=240046)

</div>

---

## 🎯 LEGENDARY PROJECT SHOWCASE

<div align="center">

[![QuerySense](https://github-readme-stats.vercel.app/api/pin/?username=amanaks8055&repo=QuerySense&theme=synthwave&hide_border=true&bg_color=0d1117&title_color=9370db&icon_color=bb86fc&text_color=e1bee7)](https://github.com/amanaks8055/QuerySense)
[![Synap](https://github-readme-stats.vercel.app/api/pin/?username=amanaks8055&repo=Synap&theme=synthwave&hide_border=true&bg_color=0d1117&title_color=9370db&icon_color=bb86fc&text_color=e1bee7)](https://github.com/amanaks8055/Synap)
[![FactoryVision](https://github-readme-stats.vercel.app/api/pin/?username=amanaks8055&repo=FactoryVision-Defect-Detection-AI&theme=synthwave&hide_border=true&bg_color=0d1117&title_color=9370db&icon_color=bb86fc&text_color=e1bee7)](https://github.com/amanaks8055/FactoryVision-Defect-Detection-AI)
[![Parkly](https://github-readme-stats.vercel.app/api/pin/?username=amanaks8055&repo=Parkly&theme=synthwave&hide_border=true&bg_color=0d1117&title_color=9370db&icon_color=bb86fc&text_color=e1bee7)](https://github.com/amanaks8055/Parkly)

</div>

---

## 🔥 CURRENT LEGENDARY MISSIONS

\`\`\`json
{
  "legendary_status": "🌟 SUSANOO PERFECT FORM ACTIVATED",
  "active_missions": {
    "primary": "🚀 Building Next-Gen AI Applications",
    "ultimate": "🔮 Creating AI That Changes Everything"
  },
  "learning_2025": ["🧠 Deep Learning","⚡ Microservices","🌐 Full Stack AI"],
  "wisdom": "💜 Enthusiasm is the fuel. Code is the weapon."
}
\`\`\`

---

## 🐍 LEGENDARY CONTRIBUTION BEAST

<div align="center">

![Snake Animation](https://raw.githubusercontent.com/platane/platane/output/github-contribution-grid-snake.svg)

</div>

---

## 🌐 LEGENDARY SOCIAL DIMENSION

<div align="center">

[![LinkedIn](https://img.shields.io/badge/LinkedIn-LEGENDARY%20NETWORK-0077B5?style=for-the-badge&logo=linkedin&logoColor=white&labelColor=4B0082)](https://www.linkedin.com/in/aman-sharma-6a3365254/)
[![GitHub](https://img.shields.io/badge/GitHub-LEGENDARY%20ARMY-9370DB?style=for-the-badge&logo=github&logoColor=white&labelColor=333)](https://github.com/amanaks8055)
[![Profile Views](https://komarev.com/ghpvc/?username=amanaks8055&label=LEGENDARY%20VISITORS&color=blueviolet&style=for-the-badge)](https://github.com/amanaks8055)

</div>

---

<div align="center">

![Footer](https://capsule-render.vercel.app/api?type=waving&color=gradient&customColorList=30,31,32,33,34,35&height=200&section=footer&text=🌟+LEGENDARY+STATUS+ACHIEVED+🌟&fontSize=30&fontColor=fff&animation=twinkling&fontAlignY=65&desc=May+the+Purple+Lightning+Guide+Your+Code&descAlignY=85&descSize=16)

</div>

⚡ **Forged in the digital realm, powered by purple lightning, destined for greatness** ⚡

🔮 **From [AMAN SHARMA](https://github.com/amanaks8055) — The Legendary Code Architect** 🔮

💜 **"Those who master the code, master the future"** 💜`;

function copyReadme() {
  navigator.clipboard.writeText(README).then(() => {
    const btn = document.getElementById('copyBtn');
    btn.textContent = '✓ COPIED!';
    btn.classList.add('done');
    document.getElementById('st1').classList.add('done');
    document.getElementById('st1').querySelector('.step-icon').textContent = '✓';
    const toast = document.getElementById('toast');
    toast.classList.add('show');
    setTimeout(() => toast.classList.remove('show'), 3000);
    setTimeout(() => {
      btn.textContent = '⚡ COPY README.MD';
      btn.classList.remove('done');
    }, 4000);
  });
}

// Init
generateBuildings();
loop();
</script>
</body>
</html>
`;
