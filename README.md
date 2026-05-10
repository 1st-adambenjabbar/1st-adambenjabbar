<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8"/>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>TRON GRID — ADAM BENJABBAR</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Share+Tech+Mono&family=Orbitron:wght@400;700;900&display=swap');

  :root {
    --cyan: #00d4ff;
    --cyan2: #00b4d8;
    --cyan-dim: #0077b6;
    --dark: #00080f;
    --darker: #000508;
    --grid-color: rgba(0,180,216,0.12);
    --grid-bright: rgba(0,212,255,0.35);
    --orange: #ff6b00;
    --white: #caf0f8;
  }

  * { margin:0; padding:0; box-sizing:border-box; }

  body {
    background: var(--darker);
    overflow: hidden;
    font-family: 'Share Tech Mono', monospace;
    cursor: none;
  }

  /* ── CUSTOM CURSOR ── */
  #cursor {
    position: fixed;
    width: 20px; height: 20px;
    border: 1px solid var(--cyan);
    border-radius: 50%;
    pointer-events: none;
    z-index: 9999;
    transform: translate(-50%,-50%);
    transition: width .15s, height .15s, opacity .15s;
    box-shadow: 0 0 8px var(--cyan), inset 0 0 4px rgba(0,212,255,.3);
  }
  #cursor-dot {
    position: fixed;
    width: 4px; height: 4px;
    background: var(--cyan);
    border-radius: 50%;
    pointer-events: none;
    z-index: 9999;
    transform: translate(-50%,-50%);
    box-shadow: 0 0 6px var(--cyan);
  }

  /* ── MAIN CANVAS ── */
  #mainCanvas { position:fixed; top:0; left:0; z-index:1; }

  /* ── SCANLINES OVERLAY ── */
  #scanlines {
    position: fixed; inset: 0; z-index: 50; pointer-events: none;
    background: repeating-linear-gradient(
      0deg,
      transparent,
      transparent 2px,
      rgba(0,0,0,0.08) 2px,
      rgba(0,0,0,0.08) 4px
    );
    animation: scanMove 8s linear infinite;
  }
  @keyframes scanMove {
    0% { background-position: 0 0; }
    100% { background-position: 0 200px; }
  }

  /* ── VIGNETTE ── */
  #vignette {
    position: fixed; inset: 0; z-index: 49; pointer-events: none;
    background: radial-gradient(ellipse at center,
      transparent 40%,
      rgba(0,5,8,.7) 100%
    );
  }

  /* ── HUD OVERLAY ── */
  #hud {
    position: fixed; inset: 0; z-index: 60; pointer-events: none;
    display: flex; flex-direction: column;
    justify-content: space-between;
    padding: 28px 36px;
  }

  .hud-top {
    display: flex; justify-content: space-between; align-items: flex-start;
  }
  .hud-bottom {
    display: flex; justify-content: space-between; align-items: flex-end;
  }

  .hud-title {
    font-family: 'Orbitron', sans-serif;
    font-weight: 900;
    font-size: clamp(22px, 3vw, 42px);
    color: var(--cyan);
    letter-spacing: .15em;
    text-shadow: 0 0 20px var(--cyan), 0 0 60px rgba(0,212,255,.4);
    animation: titlePulse 3s ease-in-out infinite;
    line-height: 1.1;
  }
  .hud-subtitle {
    font-family: 'Share Tech Mono', monospace;
    font-size: clamp(9px, 1.1vw, 13px);
    color: var(--cyan-dim);
    letter-spacing: .25em;
    margin-top: 6px;
    text-shadow: 0 0 8px var(--cyan-dim);
  }
  @keyframes titlePulse {
    0%,100% { text-shadow: 0 0 20px var(--cyan), 0 0 60px rgba(0,212,255,.4); }
    50%      { text-shadow: 0 0 30px var(--cyan), 0 0 90px rgba(0,212,255,.7), 0 0 120px rgba(0,212,255,.2); }
  }

  .hud-stats {
    text-align: right;
    font-size: clamp(8px, 0.9vw, 11px);
    color: var(--cyan-dim);
    letter-spacing: .1em;
    line-height: 1.9;
  }
  .hud-stats span { color: var(--cyan); }

  .hud-corner {
    font-size: clamp(7px, 0.8vw, 10px);
    color: rgba(0,180,216,.45);
    letter-spacing: .12em;
    line-height: 2;
  }

  .status-bar {
    display: flex; align-items: center; gap: 18px;
    font-size: clamp(8px, 0.85vw, 11px);
    color: var(--cyan-dim);
    letter-spacing: .12em;
  }
  .status-dot {
    width: 6px; height: 6px;
    border-radius: 50%;
    background: var(--cyan);
    box-shadow: 0 0 8px var(--cyan);
    animation: blink 1.4s ease-in-out infinite;
  }
  @keyframes blink { 0%,100%{opacity:1} 50%{opacity:.2} }

  .hud-line {
    position: fixed; pointer-events: none; z-index: 55;
    background: var(--cyan);
    opacity: .25;
  }
  .hud-line.h { height: 1px; left: 0; right: 0; }
  .hud-line.v { width: 1px; top: 0; bottom: 0; }
  #hl-top    { top: 80px; }
  #hl-bot    { bottom: 70px; }
  #vl-left   { left: 0; }
  #vl-right  { right: 0; }

  /* corner brackets */
  .bracket {
    position: fixed; z-index: 56; pointer-events: none;
    width: 24px; height: 24px;
    border-color: var(--cyan);
    border-style: solid;
    opacity: .6;
  }
  .bracket.tl { top: 20px; left: 20px; border-width: 2px 0 0 2px; }
  .bracket.tr { top: 20px; right: 20px; border-width: 2px 2px 0 0; }
  .bracket.bl { bottom: 20px; left: 20px; border-width: 0 0 2px 2px; }
  .bracket.br { bottom: 20px; right: 20px; border-width: 0 2px 2px 0; }

  /* ── CLICK RIPPLE ── */
  .ripple {
    position: fixed;
    border: 1px solid var(--cyan);
    border-radius: 50%;
    pointer-events: none;
    z-index: 200;
    animation: rippleOut .8s ease-out forwards;
    transform: translate(-50%,-50%);
  }
  @keyframes rippleOut {
    0%   { width:0;    height:0;    opacity:.9; }
    100% { width:120px; height:120px; opacity:0; }
  }

  /* ── INFO PANEL ── */
  #info-panel {
    position: fixed; bottom: 90px; right: 36px;
    z-index: 70; pointer-events: none;
    text-align: right;
    font-size: clamp(7px,0.8vw,10px);
    color: rgba(0,180,216,.5);
    letter-spacing: .1em;
    line-height: 2;
  }
</style>
</head>
<body>

<!-- CANVAS -->
<canvas id="mainCanvas"></canvas>

<!-- OVERLAYS -->
<div id="scanlines"></div>
<div id="vignette"></div>

<!-- CORNER BRACKETS -->
<div class="bracket tl"></div>
<div class="bracket tr"></div>
<div class="bracket bl"></div>
<div class="bracket br"></div>

<!-- HORIZONTAL / VERTICAL HUD LINES -->
<div class="hud-line h" id="hl-top"></div>
<div class="hud-line h" id="hl-bot"></div>

<!-- HUD -->
<div id="hud">
  <div class="hud-top">
    <div>
      <div class="hud-title">ADAM BENJABBAR</div>
      <div class="hud-subtitle">IA2R · POLYTECH NANCY · ROBOTICS ENGINEER</div>
    </div>
    <div class="hud-stats">
      <div>SECTOR <span>IA2R-07</span></div>
      <div>STATUS <span>ONLINE</span></div>
      <div>GRID <span id="gridHz">60Hz</span></div>
      <div>CYCLE <span id="cycleCount">0000</span></div>
    </div>
  </div>
  <div class="hud-bottom">
    <div class="status-bar">
      <div class="status-dot"></div>
      <span>AUTONOMOUS SYSTEMS ACTIVE</span>
    </div>
    <div class="hud-corner">
      <div>ROS2 · STM32 · SLAM</div>
      <div>COUPE DE FRANCE 2025</div>
    </div>
  </div>
</div>

<!-- INFO PANEL -->
<div id="info-panel">
  <div>ENTITIES: <span id="entityCount">0</span></div>
  <div>LIGHT TRAILS: <span id="trailCount">0</span></div>
  <div>PARTICLES: <span id="particleCount">0</span></div>
</div>

<!-- CURSOR -->
<div id="cursor"></div>
<div id="cursor-dot"></div>

<script>
// ════════════════════════════════════════════
//  TRON GRID SCENE — Full Canvas Engine
// ════════════════════════════════════════════

const canvas = document.getElementById('mainCanvas');
const ctx    = canvas.getContext('2d');

let W, H, cx, cy, frame = 0;

function resize() {
  W = canvas.width  = window.innerWidth;
  H = canvas.height = window.innerHeight;
  cx = W/2; cy = H/2;
}
resize();
window.addEventListener('resize', resize);

// ── PALETTE ──
const C = {
  bg:       '#000508',
  cyan:     '#00d4ff',
  cyan2:    '#00b4d8',
  cyanDim:  '#004466',
  cyanGlow: 'rgba(0,212,255,',
  orange:   '#ff6b00',
  orangeG:  'rgba(255,107,0,',
  white:    '#caf0f8',
  red:      '#ff2244',
};

// ── PERSPECTIVE GRID ──
const GRID = {
  cellSize: 80,
  horizonY: 0,
  fov: 600,
  speed: 0.8,
  offset: 0,
  cols: 30,
  rows: 30,
  draw(ctx) {
    this.offset = (this.offset + this.speed) % this.cellSize;
    const hy = H * 0.52;
    const fade = (y) => Math.max(0, Math.min(1, (y - hy) / (H - hy)));

    ctx.save();

    // Draw vertical lines (perspective)
    const numV = 24;
    for (let i = 0; i <= numV; i++) {
      const t = i / numV;
      const x = W * t;
      const alpha = 0.05 + 0.3 * Math.sin(t * Math.PI);
      const grd = ctx.createLinearGradient(x, hy, x, H);
      grd.addColorStop(0, `rgba(0,180,216,0)`);
      grd.addColorStop(0.3, `rgba(0,180,216,${alpha * 0.5})`);
      grd.addColorStop(1, `rgba(0,212,255,${alpha})`);
      ctx.strokeStyle = grd;
      ctx.lineWidth = i === 0 || i === numV ? 1.5 : 0.7;
      ctx.beginPath();
      ctx.moveTo(x, hy);
      ctx.lineTo(cx + (x - cx) * 3.5, H + 50);
      ctx.stroke();
    }

    // Horizontal lines with scroll
    const numH = 22;
    for (let i = 0; i <= numH; i++) {
      const rawT = i / numH;
      const perspT = rawT * rawT; // quadratic for depth
      const y = hy + (H - hy + 100) * perspT - this.offset * perspT * 0.5;
      if (y < hy) continue;
      const f = fade(y);
      const alpha = f * (0.08 + 0.25 * perspT);
      ctx.strokeStyle = `rgba(0,180,216,${alpha})`;
      ctx.lineWidth = perspT > 0.8 ? 1.2 : 0.5;
      ctx.beginPath();
      ctx.moveTo(0, y);
      ctx.lineTo(W, y);
      ctx.stroke();
    }

    // Horizon glow
    const hGrd = ctx.createLinearGradient(0, hy-30, 0, hy+60);
    hGrd.addColorStop(0, 'rgba(0,212,255,0)');
    hGrd.addColorStop(0.5, 'rgba(0,212,255,0.18)');
    hGrd.addColorStop(1, 'rgba(0,212,255,0)');
    ctx.fillStyle = hGrd;
    ctx.fillRect(0, hy-30, W, 90);

    ctx.restore();
  }
};

// ── TOP GRID (ceiling effect) ──
const TOPGRID = {
  draw(ctx) {
    const ty = H * 0.48;
    ctx.save();
    const numV = 24;
    for (let i = 0; i <= numV; i++) {
      const t = i / numV;
      const x = W * t;
      const alpha = 0.03 + 0.12 * Math.sin(t * Math.PI);
      ctx.strokeStyle = `rgba(0,150,200,${alpha * 0.4})`;
      ctx.lineWidth = 0.5;
      ctx.beginPath();
      ctx.moveTo(x, ty);
      ctx.lineTo(cx + (x - cx) * 3.5, -50);
      ctx.stroke();
    }
    ctx.restore();
  }
};

// ── LIGHT TRAILS (light cycles) ──
class LightTrail {
  constructor() { this.reset(); }
  reset() {
    const side = Math.random() < 0.5;
    this.x  = side ? -60 : W + 60;
    this.y  = H * (0.6 + Math.random() * 0.38);
    this.vx = side ? (2 + Math.random()*4) : -(2 + Math.random()*4);
    this.vy = (Math.random() - 0.5) * 0.4;
    this.color = Math.random() < 0.75 ? C.cyan : C.orange;
    this.gcolor = Math.random() < 0.75 ? C.cyanGlow : C.orangeG;
    this.width = 1.5 + Math.random() * 1.5;
    this.trail = [];
    this.maxTrail = 90 + Math.floor(Math.random() * 100);
    this.alive = true;
    this.turnChance = 0.004;
    this.speed = 2.5 + Math.random() * 3;
  }
  update() {
    // Random right-angle turns (Tron style)
    if (Math.random() < this.turnChance) {
      const tmp = this.vx;
      this.vx = this.vy * (Math.random()<.5?1:-1) * this.speed;
      this.vy = tmp * (Math.random()<.5?1:-1) * this.speed * 0.3;
    }
    this.x += this.vx;
    this.y += this.vy;
    this.trail.push({x: this.x, y: this.y});
    if (this.trail.length > this.maxTrail) this.trail.shift();
    if (this.x < -300 || this.x > W+300 || this.y < H*0.5 || this.y > H+100) this.alive = false;
  }
  draw(ctx) {
    if (this.trail.length < 2) return;
    for (let i = 1; i < this.trail.length; i++) {
      const t = i / this.trail.length;
      const a = t * t;
      ctx.beginPath();
      ctx.moveTo(this.trail[i-1].x, this.trail[i-1].y);
      ctx.lineTo(this.trail[i].x, this.trail[i].y);
      ctx.strokeStyle = this.gcolor + a + ')';
      ctx.lineWidth = this.width * t;
      ctx.shadowBlur = 12 * t;
      ctx.shadowColor = this.color;
      ctx.stroke();
    }
    ctx.shadowBlur = 0;
    // Head glow
    ctx.beginPath();
    ctx.arc(this.x, this.y, 3, 0, Math.PI*2);
    ctx.fillStyle = this.color;
    ctx.shadowBlur = 20;
    ctx.shadowColor = this.color;
    ctx.fill();
    ctx.shadowBlur = 0;
  }
}

// ── ROBOT ENTITY ──
class Robot {
  constructor(x, y, scale, variant) {
    this.x = x; this.y = y;
    this.scale = scale || 1;
    this.variant = variant || 0; // 0=humanoid, 1=spider, 2=drone
    this.phase = Math.random() * Math.PI * 2;
    this.speed = 0.3 + Math.random() * 0.5;
    this.dir = Math.random() < 0.5 ? 1 : -1;
    this.alpha = 0.7 + Math.random() * 0.3;
    this.glowPhase = Math.random() * Math.PI * 2;
    this.alive = true;
    this.vx = this.dir * (0.4 + Math.random() * 0.8);
  }

  update() {
    this.x += this.vx;
    this.phase += 0.04 * this.speed;
    this.glowPhase += 0.03;
    if (this.x < -120 || this.x > W + 120) this.alive = false;
  }

  drawHumanoid(ctx, x, y, s, glow) {
    ctx.save();
    ctx.translate(x, y);
    ctx.scale(s * this.dir, s);

    const g = glow;
    // Body
    ctx.strokeStyle = C.cyan;
    ctx.lineWidth = 1.5 / s;
    ctx.shadowBlur = g * 12;
    ctx.shadowColor = C.cyan;

    // Head
    ctx.strokeRect(-8, -38, 16, 14);
    // Visor
    ctx.fillStyle = `rgba(0,212,255,${0.4 * g})`;
    ctx.fillRect(-6, -35, 12, 5);
    // Antenna
    ctx.beginPath(); ctx.moveTo(0,-38); ctx.lineTo(0,-46);
    ctx.moveTo(-3,-46); ctx.lineTo(3,-46); ctx.stroke();
    // Neck
    ctx.beginPath(); ctx.moveTo(0,-24); ctx.lineTo(0,-20); ctx.stroke();
    // Torso
    ctx.strokeRect(-12,-20,24,22);
    // Core light
    ctx.fillStyle = `rgba(0,212,255,${0.6*g})`;
    ctx.beginPath(); ctx.arc(0,-10,4,0,Math.PI*2); ctx.fill();
    // Arms
    const armSwing = Math.sin(this.phase) * 12;
    ctx.beginPath();
    ctx.moveTo(-12,-18); ctx.lineTo(-18,-10+armSwing); ctx.lineTo(-16,8+armSwing/2);
    ctx.moveTo(12,-18);  ctx.lineTo(18,-10-armSwing); ctx.lineTo(16,8-armSwing/2);
    ctx.stroke();
    // Legs
    const legSwing = Math.sin(this.phase) * 10;
    ctx.beginPath();
    ctx.moveTo(-6,2); ctx.lineTo(-8,14+legSwing); ctx.lineTo(-10,26+legSwing);
    ctx.moveTo(6,2);  ctx.lineTo(8,14-legSwing);  ctx.lineTo(10,26-legSwing);
    ctx.stroke();
    // Feet
    ctx.beginPath();
    ctx.moveTo(-10,26+legSwing); ctx.lineTo(-16,26+legSwing);
    ctx.moveTo(10,26-legSwing);  ctx.lineTo(16,26-legSwing);
    ctx.stroke();

    ctx.restore();
  }

  drawSpider(ctx, x, y, s, glow) {
    ctx.save();
    ctx.translate(x, y);
    ctx.scale(s, s);
    ctx.strokeStyle = C.orange;
    ctx.shadowBlur = glow * 14;
    ctx.shadowColor = C.orange;
    ctx.lineWidth = 1.2 / s;

    // Body
    ctx.beginPath();
    ctx.ellipse(0, 0, 14, 10, 0, 0, Math.PI*2);
    ctx.stroke();
    ctx.fillStyle = `rgba(255,107,0,${0.2 * glow})`;
    ctx.fill();

    // Head
    ctx.beginPath();
    ctx.arc(16, 0, 7, 0, Math.PI*2);
    ctx.stroke();
    // Eyes
    ctx.fillStyle = C.orange;
    ctx.beginPath(); ctx.arc(19, -2, 1.5, 0, Math.PI*2); ctx.fill();
    ctx.beginPath(); ctx.arc(19, 2, 1.5, 0, Math.PI*2); ctx.fill();

    // 8 legs with phase walk
    for (let i = 0; i < 4; i++) {
      const side = i < 2 ? -1 : 1;
      const li = i % 2;
      const swing = Math.sin(this.phase + li * Math.PI) * 8;
      const baseY = (li - 0.5) * 12;
      ctx.beginPath();
      ctx.moveTo(side * 12, baseY);
      ctx.lineTo(side * 22, baseY + swing - 8);
      ctx.lineTo(side * 30, baseY + swing);
      ctx.stroke();
    }
    ctx.restore();
  }

  drawDrone(ctx, x, y, s, glow) {
    ctx.save();
    ctx.translate(x, y);
    ctx.scale(s, s);
    ctx.strokeStyle = C.cyan;
    ctx.shadowBlur = glow * 16;
    ctx.shadowColor = C.cyan;
    ctx.lineWidth = 1.2 / s;

    // Hover bob
    const bob = Math.sin(this.phase * 2) * 3;
    ctx.translate(0, bob);

    // Body hexagon
    ctx.beginPath();
    for (let i = 0; i < 6; i++) {
      const a = i * Math.PI / 3 - Math.PI/6;
      i === 0 ? ctx.moveTo(Math.cos(a)*14, Math.sin(a)*14)
              : ctx.lineTo(Math.cos(a)*14, Math.sin(a)*14);
    }
    ctx.closePath(); ctx.stroke();
    ctx.fillStyle = `rgba(0,212,255,${0.15*glow})`; ctx.fill();

    // Core
    ctx.beginPath(); ctx.arc(0,0,5,0,Math.PI*2);
    ctx.fillStyle = `rgba(0,212,255,${0.8*glow})`; ctx.fill();

    // 4 rotors
    const rotorSpin = this.phase * 8;
    const arms = [[22,0],[-22,0],[0,22],[0,-22]];
    arms.forEach(([ax,ay]) => {
      ctx.beginPath(); ctx.moveTo(ax*0.5,ay*0.5); ctx.lineTo(ax,ay); ctx.stroke();
      ctx.save();
      ctx.translate(ax, ay);
      ctx.rotate(rotorSpin);
      ctx.beginPath();
      ctx.ellipse(0,0,7,2,0,0,Math.PI*2);
      ctx.strokeStyle = `rgba(0,212,255,${0.5*glow})`;
      ctx.stroke();
      ctx.restore();
    });

    // Downward beam
    const beamGrd = ctx.createLinearGradient(0,14,0,40);
    beamGrd.addColorStop(0, `rgba(0,212,255,${0.3*glow})`);
    beamGrd.addColorStop(1, 'rgba(0,212,255,0)');
    ctx.fillStyle = beamGrd;
    ctx.fillRect(-3, 14, 6, 26);

    ctx.restore();
  }

  draw(ctx) {
    const glow = 0.6 + 0.4 * Math.sin(this.glowPhase);
    ctx.globalAlpha = this.alpha;
    if      (this.variant === 0) this.drawHumanoid(ctx, this.x, this.y, this.scale, glow);
    else if (this.variant === 1) this.drawSpider(ctx, this.x, this.y, this.scale, glow);
    else                         this.drawDrone(ctx, this.x, this.y, this.scale, glow);
    ctx.globalAlpha = 1;
  }
}

// ── PARTICLES ──
class Particle {
  constructor(x, y, color) {
    this.x = x || Math.random() * W;
    this.y = y || Math.random() * H;
    this.color = color || (Math.random()<.8 ? C.cyan : C.orange);
    this.size = 0.5 + Math.random() * 2;
    this.vx = (Math.random()-.5)*0.8;
    this.vy = -0.2 - Math.random()*0.6;
    this.life = 1;
    this.decay = 0.004 + Math.random()*0.008;
    this.alive = true;
  }
  update() {
    this.x += this.vx; this.y += this.vy;
    this.life -= this.decay;
    if (this.life <= 0) this.alive = false;
  }
  draw(ctx) {
    ctx.beginPath();
    ctx.arc(this.x, this.y, this.size * this.life, 0, Math.PI*2);
    ctx.fillStyle = this.color;
    ctx.globalAlpha = this.life * 0.7;
    ctx.shadowBlur = 6;
    ctx.shadowColor = this.color;
    ctx.fill();
    ctx.globalAlpha = 1;
    ctx.shadowBlur = 0;
  }
}

// ── ENERGY NODE ──
class EnergyNode {
  constructor(x, y) {
    this.x = x; this.y = y;
    this.phase = Math.random() * Math.PI * 2;
    this.radius = 8 + Math.random() * 12;
    this.pulseSpeed = 0.02 + Math.random() * 0.03;
  }
  draw(ctx) {
    this.phase += this.pulseSpeed;
    const pulse = 0.5 + 0.5 * Math.sin(this.phase);

    ctx.save();
    ctx.shadowBlur = 20 * pulse;
    ctx.shadowColor = C.cyan;

    // Outer ring
    ctx.beginPath();
    ctx.arc(this.x, this.y, this.radius + 4 * pulse, 0, Math.PI*2);
    ctx.strokeStyle = `rgba(0,212,255,${0.2 * pulse})`;
    ctx.lineWidth = 1;
    ctx.stroke();

    // Inner dot
    ctx.beginPath();
    ctx.arc(this.x, this.y, 3, 0, Math.PI*2);
    ctx.fillStyle = `rgba(0,212,255,${0.6 + 0.4 * pulse})`;
    ctx.fill();

    ctx.restore();
  }
}

// ── DATA STREAM (vertical falling chars) ──
const CHARS = '01アイウエオカキクSTM32ROS2SLAMIA2R'.split('');
class DataStream {
  constructor() { this.reset(); }
  reset() {
    this.x = Math.random() * W;
    this.y = Math.random() * H * 0.5;
    this.speed = 1.5 + Math.random() * 2.5;
    this.chars = Array.from({length: 8+Math.floor(Math.random()*10)}, ()=>
      CHARS[Math.floor(Math.random()*CHARS.length)]
    );
    this.alpha = 0.1 + Math.random() * 0.3;
    this.alive = true;
  }
  update() {
    this.y += this.speed;
    if (this.y > H + 200) this.alive = false;
    // randomly mutate chars
    if (Math.random() < 0.05) {
      const i = Math.floor(Math.random()*this.chars.length);
      this.chars[i] = CHARS[Math.floor(Math.random()*CHARS.length)];
    }
  }
  draw(ctx) {
    ctx.font = '10px "Share Tech Mono"';
    this.chars.forEach((c, i) => {
      const a = i === this.chars.length-1 ? this.alpha * 2.5 : this.alpha * (1 - i/this.chars.length * 0.6);
      ctx.fillStyle = i === this.chars.length-1
        ? `rgba(200,240,255,${Math.min(1,a)})`
        : `rgba(0,180,216,${a})`;
      ctx.fillText(c, this.x, this.y - i * 12);
    });
  }
}

// ── HORIZON CITY SILHOUETTE ──
function drawCity(ctx) {
  const hy = H * 0.52;
  ctx.save();

  // Buildings
  const buildings = [
    {x:0.03,w:0.04,h:60}, {x:0.07,w:0.025,h:90}, {x:0.10,w:0.05,h:50},
    {x:0.15,w:0.03,h:110},{x:0.18,w:0.04,h:70},  {x:0.22,w:0.025,h:130},
    {x:0.25,w:0.06,h:80}, {x:0.31,w:0.03,h:95},  {x:0.34,w:0.05,h:55},
    {x:0.39,w:0.04,h:140},{x:0.43,w:0.025,h:75}, {x:0.46,w:0.07,h:100},
    {x:0.53,w:0.04,h:140},{x:0.57,w:0.025,h:75}, {x:0.60,w:0.07,h:65},
    {x:0.67,w:0.05,h:130},{x:0.72,w:0.03,h:85},  {x:0.75,w:0.04,h:50},
    {x:0.79,w:0.025,h:110},{x:0.82,w:0.05,h:70}, {x:0.87,w:0.03,h:90},
    {x:0.90,w:0.04,h:60}, {x:0.94,w:0.025,h:100},{x:0.97,w:0.03,h:45},
  ];

  buildings.forEach(b => {
    const bx = b.x * W, bw = b.w * W, bh = b.h;
    // Building fill
    ctx.fillStyle = 'rgba(0,10,18,0.95)';
    ctx.fillRect(bx, hy - bh, bw, bh);
    // Edge glow
    ctx.strokeStyle = `rgba(0,180,216,0.25)`;
    ctx.lineWidth = 1;
    ctx.strokeRect(bx, hy - bh, bw, bh);
    // Windows (random pattern)
    ctx.fillStyle = `rgba(0,212,255,0.4)`;
    for (let wy = hy - bh + 6; wy < hy - 4; wy += 8) {
      for (let wx = bx + 3; wx < bx + bw - 3; wx += 6) {
        if (Math.sin(wx * wy * 0.001 + frame*0.002) > 0.3) {
          ctx.fillRect(wx, wy, 3, 4);
        }
      }
    }
    // Antenna on tall buildings
    if (bh > 100) {
      ctx.strokeStyle = `rgba(0,212,255,0.5)`;
      ctx.beginPath();
      ctx.moveTo(bx + bw/2, hy - bh);
      ctx.lineTo(bx + bw/2, hy - bh - 20);
      ctx.stroke();
      // Blink
      if (Math.sin(frame * 0.05 + b.x * 10) > 0.7) {
        ctx.beginPath();
        ctx.arc(bx + bw/2, hy - bh - 20, 2.5, 0, Math.PI*2);
        ctx.fillStyle = C.red;
        ctx.shadowBlur = 10; ctx.shadowColor = C.red;
        ctx.fill(); ctx.shadowBlur = 0;
      }
    }
  });

  // Ground glow beneath city
  const cityGrd = ctx.createLinearGradient(0, hy-15, 0, hy+10);
  cityGrd.addColorStop(0, 'rgba(0,212,255,0.15)');
  cityGrd.addColorStop(1, 'rgba(0,212,255,0)');
  ctx.fillStyle = cityGrd;
  ctx.fillRect(0, hy-15, W, 25);

  ctx.restore();
}

// ── BACKGROUND ATMOSPHERE ──
function drawAtmosphere(ctx) {
  // Deep space gradient
  const bgGrd = ctx.createLinearGradient(0, 0, 0, H);
  bgGrd.addColorStop(0,   '#000508');
  bgGrd.addColorStop(0.4, '#000d14');
  bgGrd.addColorStop(0.52,'#001a28');
  bgGrd.addColorStop(0.55,'#000d14');
  bgGrd.addColorStop(1,   '#000508');
  ctx.fillStyle = bgGrd;
  ctx.fillRect(0, 0, W, H);

  // Subtle nebula / ambient light
  const t = frame * 0.003;
  const nebCx = cx + Math.sin(t) * W * 0.1;
  const nebCy = H * 0.3 + Math.cos(t * 0.7) * H * 0.05;
  const neb = ctx.createRadialGradient(nebCx, nebCy, 0, nebCx, nebCy, W * 0.5);
  neb.addColorStop(0, 'rgba(0,50,80,0.12)');
  neb.addColorStop(1, 'rgba(0,0,0,0)');
  ctx.fillStyle = neb;
  ctx.fillRect(0, 0, W, H);
}

// ── STATE ──
let trails    = [];
let robots    = [];
let particles = [];
let streams   = [];
let nodes     = [];

// Init energy nodes at grid intersections area
function initNodes() {
  nodes = [];
  const hy = H * 0.52;
  for (let i = 0; i < 12; i++) {
    nodes.push(new EnergyNode(
      W * (0.1 + Math.random() * 0.8),
      hy + Math.random() * (H - hy) * 0.5
    ));
  }
}
initNodes();
window.addEventListener('resize', initNodes);

// Spawn helpers
function spawnTrail() {
  if (trails.length < 12) trails.push(new LightTrail());
}
function spawnRobot() {
  if (robots.length < 8) {
    const hy = H * 0.52;
    const variant = Math.floor(Math.random() * 3);
    const yRange = variant === 2
      ? hy + Math.random() * (H - hy) * 0.5   // drones float anywhere
      : hy + 20 + Math.random() * 30;           // walkers near ground
    const scale = 0.6 + Math.random() * 0.8;
    const side = Math.random() < 0.5;
    robots.push(new Robot(side ? -80 : W+80, yRange, scale, variant));
  }
}
function spawnStream() {
  if (streams.length < 20) streams.push(new DataStream());
}

// Spawn intervals
setInterval(spawnTrail, 1200);
setInterval(spawnRobot, 2500);
setInterval(spawnStream, 600);
// Initial spawns
for (let i = 0; i < 6; i++) trails.push(new LightTrail());
for (let i = 0; i < 4; i++) robots.push(new Robot(
  Math.random() * W,
  H * 0.55 + Math.random() * H * 0.3,
  0.6 + Math.random() * 0.7,
  Math.floor(Math.random()*3)
));
for (let i = 0; i < 12; i++) streams.push(new DataStream());

// ── MOUSE / TOUCH ──
let mouseX = cx, mouseY = cy;
const cursor    = document.getElementById('cursor');
const cursorDot = document.getElementById('cursor-dot');

document.addEventListener('mousemove', e => {
  mouseX = e.clientX; mouseY = e.clientY;
  cursor.style.left    = mouseX + 'px';
  cursor.style.top     = mouseY + 'px';
  cursorDot.style.left = mouseX + 'px';
  cursorDot.style.top  = mouseY + 'px';
  // Spawn particles along path
  if (Math.random() < 0.25) {
    particles.push(new Particle(mouseX + (Math.random()-.5)*10, mouseY + (Math.random()-.5)*10, C.cyan));
  }
});

document.addEventListener('click', e => {
  // Ripple
  const r = document.createElement('div');
  r.className = 'ripple';
  r.style.left = e.clientX + 'px';
  r.style.top  = e.clientY + 'px';
  document.body.appendChild(r);
  setTimeout(() => r.remove(), 800);
  // Burst particles
  for (let i = 0; i < 20; i++) {
    particles.push(new Particle(e.clientX, e.clientY, Math.random()<.6?C.cyan:C.orange));
  }
  // New trail from click
  const t = new LightTrail();
  t.x = e.clientX; t.y = e.clientY;
  trails.push(t);
});

// HUD counters
const cycleEl  = document.getElementById('cycleCount');
const entityEl = document.getElementById('entityCount');
const trailEl  = document.getElementById('trailCount');
const partEl   = document.getElementById('particleCount');

// ── MAIN LOOP ──
function loop() {
  frame++;
  ctx.clearRect(0, 0, W, H);

  // Background
  drawAtmosphere(ctx);

  // Data streams (behind everything)
  streams = streams.filter(s => s.alive);
  streams.forEach(s => { s.update(); s.draw(ctx); });

  // Top grid (ceiling)
  TOPGRID.draw(ctx);

  // Perspective floor grid
  GRID.draw(ctx);

  // City skyline at horizon
  drawCity(ctx);

  // Energy nodes
  nodes.forEach(n => n.draw(ctx));

  // Robots
  robots = robots.filter(r => r.alive);
  robots.forEach(r => { r.update(); r.draw(ctx); });

  // Light trails
  trails = trails.filter(t => t.alive);
  trails.forEach(t => { t.update(); t.draw(ctx); });

  // Particles
  particles = particles.filter(p => p.alive);
  particles.forEach(p => { p.update(); p.draw(ctx); });

  // HUD update (every 30 frames)
  if (frame % 30 === 0) {
    cycleEl.textContent  = String(frame).padStart(6,'0');
    entityEl.textContent = robots.length;
    trailEl.textContent  = trails.length;
    partEl.textContent   = particles.length;
  }

  requestAnimationFrame(loop);
}

loop();
</script>
</body>
</html>
EOF
