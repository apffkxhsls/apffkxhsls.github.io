# apffkxhsls.github.io

<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8" />
<title>DM-Man</title>
<style>
  body { background:#111; display:flex; justify-content:center; align-items:center; height:100vh; }
  canvas { background:#000; border:3px solid #fff; }
</style>
</head>

<body>
<canvas id="game" width="500" height="500"></canvas>

<script>
const canvas = document.getElementById("game");
const ctx = canvas.getContext("2d");

let player = { x:250, y:250, size:15, speed:3 };
let hearts = [];
let dms = [];
let score = 0;
let gameOver = false;

// Spawn heart items (good)
function spawnHeart() {
  hearts.push({
    x: Math.random()*470+15,
    y: Math.random()*470+15,
    size: 10
  });
}
setInterval(spawnHeart, 2000);

// Spawn DM enemies (bad)
function spawnDM() {
  dms.push({
    x: Math.random()<0.5 ? 0 : 500,
    y: Math.random()*500,
    size: 15,
    vx: (Math.random()*1.5+1) * (Math.random()<0.5?-1:1),
    vy: (Math.random()*1.5+1) * (Math.random()<0.5?-1:1)
  });
}
setInterval(spawnDM, 1500);

document.addEventListener("keydown", (e)=>{
  if(e.key==="ArrowUp") player.y -= player.speed;
  if(e.key==="ArrowDown") player.y += player.speed;
  if(e.key==="ArrowLeft") player.x -= player.speed;
  if(e.key==="ArrowRight") player.x += player.speed;
});

// Collision check
function hit(a,b){
  let dx=a.x-b.x, dy=a.y-b.y;
  return Math.hypot(dx,dy) < (a.size+b.size);
}

function draw(){
  if(gameOver){
    ctx.fillStyle="#fff";
    ctx.font="28px Arial";
    ctx.fillText("Game Over!",180,240);
    ctx.fillText("Score: "+score,200,280);
    return;
  }

  ctx.clearRect(0,0,500,500);

  // player
  ctx.fillStyle="yellow";
  ctx.beginPath();
  ctx.arc(player.x,player.y,player.size,0,Math.PI*2);
  ctx.fill();

  // hearts
  ctx.fillStyle="red";
  hearts.forEach((h,i)=>{
    ctx.beginPath();
    ctx.arc(h.x,h.y,h.size,0,Math.PI*2);
    ctx.fill();

    if(hit(player,h)){
      hearts.splice(i,1);
      score += 10;
    }
  });

  // DMs
  ctx.fillStyle="white";
  dms.forEach((d,i)=>{
    d.x += d.vx;
    d.y += d.vy;

    // bounce
    if(d.x<=0||d.x>=500) d.vx *= -1;
    if(d.y<=0||d.y>=500) d.vy *= -1;

    ctx.beginPath();
    ctx.arc(d.x,d.y,d.size,0,Math.PI*2);
    ctx.fill();
    ctx.fillStyle="#ff0";
    ctx.font="12px Arial";
    ctx.fillText("🔔", d.x-6, d.y+4);
    ctx.fillStyle="white";

    if(hit(player,d)){ gameOver=true; }
  });

  // Score UI
  ctx.fillStyle="#fff";
  ctx.font="18px Arial";
  ctx.fillText("❤️ "+score, 20, 30);

  requestAnimationFrame(draw);
}

draw();
</script>
</body>
</html>
