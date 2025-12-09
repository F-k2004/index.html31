<!DOCTYPE html>
<html lang="fa">
<head>
<meta charset="UTF-8">
<title>💎🌈 Rainbow Crystal Explosion</title>
<meta name="viewport" content="width=device-width, initial-scale=1">
<style>
  body {
    margin: 0;
    background: #000011;
    overflow: hidden;
  }
  canvas {
    isplay: block;
  }
</style>
</head>

<body>
<canvas id="c"></canvas>

<script>
const canvas = document.getElementById("c");
const ctx = canvas.getContext("2d");

let w, h;
function resize() {
  w = canvas.width = window.innerWidth;
  h = canvas.height = window.innerHeight;
}
resize();
window.addEventListener("resize", resize);

// -------------------------------------------
// Fragment (exploding crystal pieces)
// -------------------------------------------
class Fragment {
  constructor(angle, dist, hue) {
    this.angle = angle;
    this.dist = dist;
    this.size = 15 + Math.random() * 75;
    this.speed = 0.5 + Math.random() * 2;
    this.z = Math.random() * 2;
    this.hue = hue;
    this.light = 60 + Math.random()*30;
  }

  update() {
    this.dist += this.speed;
    this.z += 0.02;
  }

  draw() {
    const x = w/2 + Math.cos(this.angle) * this.dist * this.z;
    const y = h/2 + Math.sin(this.angle) * this.dist * this.z;
    const s = this.size * this.z;

    ctx.beginPath();
    ctx.fillStyle = `hsl(${this.hue}, 100%, ${this.light}%)`;
    ctx.globalAlpha = 1 - Math.min(1, this.z / 6);

    ctx.moveTo(x, y - s/2);
    ctx.lineTo(x + s/2, y + s/2);
    ctx.lineTo(x - s/2, y + s/2);
    ctx.closePath();
    ctx.fill();

    ctx.globalAlpha = 1;
  }
}

// -------------------------------------------
// Explosion generator
// -------------------------------------------
let fragments = [];
let explosionPower = 0;
let exploding = false;
let baseHue = 0;

function triggerExplosion() {
  fragments = [];
  baseHue = Math.random() * 360;

  for (let i = 0; i < 220; i++) {
    let hue = (baseHue + Math.random()*120) % 360;
    fragments.push(
      new Fragment(Math.random() * Math.PI * 2, 2 + Math.random() * 6, hue)
    );
  }

  exploding = true;
  explosionPower = 0;
}

setInterval(triggerExplosion, 3500);
triggerExplosion();

// -------------------------------------------
// Draw rotating rainbow crystal core
// -------------------------------------------
function drawCrystalCore(t) {
  const r = 100 + Math.sin(t*0.02)*15;

  ctx.save();
  ctx.translate(w/2, h/2);
  ctx.rotate(t * 0.002);

  for (let i = 0; i < 7; i++) {
    ctx.rotate(Math.PI/3.5);
    ctx.beginPath();
    ctx.fillStyle = `hsla(${(t + i*50)%360}, 100%, 60%, 0.55)`;

    ctx.moveTo(0, -r);
    ctx.lineTo(25, -r/3);
    ctx.lineTo(-25, -r/3);
    ctx.closePath();
    ctx.fill();
  }

  ctx.restore();
}

// -------------------------------------------
// Glow function
// -------------------------------------------
function glow(x, y, r, color) {
  const g = ctx.createRadialGradient(x,y,0, x,y,r);
  g.addColorStop(0, color);
  g.addColorStop(1, "transparent");
  ctx.fillStyle = g;

  ctx.beginPath();
  ctx.arc(x,y,r,0,Math.PI*2);
  ctx.fill();
}

// -------------------------------------------
// Main loop
// -------------------------------------------
let t = 0;
function loop() {
  ctx.clearRect(0,0,w,h);

  // Ambient rainbow glow
  glow(w/2, h/2, 250, `hsla(${(t*2)%360}, 100%, 60%, 0.25)`);

  // Crystal still visible before full explosion
  if (!exploding || explosionPower < 1) {
    drawCrystalCore(t);
  }

  // Explosion effect
  if (exploding) {
    explosionPower += 0.02;

    fragments.forEach(f => {
      f.update();
      f.draw();
    });

    if (explosionPower >= 6) exploding = false;
  }

  t++;
  requestAnimationFrame(loop);
}

loop();
</script>
</body>
</html>
