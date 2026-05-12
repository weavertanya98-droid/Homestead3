 <!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, user-scalable=no">
<title>Homestead Survival v4 - Dark Cutesy</title>
<style>
  body { 
    margin:0; 
    background:#0d1a0d; 
    color:#fff; 
    font-family: 'Courier New', monospace; 
    overflow:hidden; 
    touch-action:none; 
  }
  #game { 
    position:relative; 
    width:100vw; 
    height:100vh; 
    background:#1a2a1a;
    background-image: 
      radial-gradient(circle at 20% 30%, #1e331e 0%, transparent 50%),
      radial-gradient(circle at 80% 70%, #1e331e 0%, transparent 50%),
      repeating-linear-gradient(0deg, transparent, transparent 31px, #1f3a1f 32px),
      repeating-linear-gradient(90deg, transparent, transparent 31px, #1f3a1f 32px);
  }
  #player { 
    position:absolute; 
    font-size:28px; 
    z-index:10; 
    filter: drop-shadow(0 2px 2px #000);
    animation: bob 1.5s ease-in-out infinite;
  }
  .tree { 
    position:absolute; 
    font-size:32px; 
    filter: drop-shadow(0 3px 3px #000);
    animation: sway 3s ease-in-out infinite;
    z-index:5;
  }
  .animal { 
    position:absolute; 
    font-size:26px; 
    filter: drop-shadow(0 2px 2px #000);
    animation: bob 2s ease-in-out infinite;
    z-index:6;
  }
  #ui { 
    position:absolute; 
    top:10px; 
    left:10px; 
    font-size:20px; 
    z-index:20; 
    background:rgba(10,20,10,0.8); 
    padding:8px 12px; 
    border-radius:12px;
    border:2px solid #2a4a2a;
  }
  .ui-item {
    display:inline-block;
    margin-right:12px;
    background:rgba(0,0,0,0.3);
    padding:2px 6px;
    border-radius:6px;
  }
  #dpad { position:absolute; bottom:20px; right:20px; z-index:20; }
  #dpad button { 
    width:65px; height:65px; margin:3px; font-size:28px; 
    background:#2a3a2a; color:#fff; border:2px solid #3a5a3a; 
    border-radius:12px; box-shadow:0 3px 0 #1a2a1a;
  }
  #dpad button:active { transform:translateY(3px); box-shadow:none; }
  #action { 
    position:absolute; bottom:20px; left:20px; width:85px; height:85px; 
    font-size:24px; z-index:20; background:#4a2a2a; color:#fff; 
    border:2px solid #6a3a3a; border-radius:50%; 
    box-shadow:0 4px 0 #2a1a1a;
  }
  #action:active { transform:translateY(4px); box-shadow:none; }
  
  @keyframes bob {
    0%, 100% { transform:translateY(0); }
    50% { transform:translateY(-3px); }
  }
  @keyframes sway {
    0%, 100% { transform:rotate(-2deg); }
    50% { transform:rotate(2deg); }
  }
</style>
</head>
<body>
<div id="game">
  <div id="player">😠</div>
  <div id="ui">
    <span class="ui-item">🌳 0</span>
    <span class="ui-item">🧵 0</span>
    <span class="ui-item">🥫 0</span>
    <span class="ui-item">💧 0</span>
    <span class="ui-item">🪙 0</span>
  </div>
  <div id="dpad">
    <button ontouchstart="move('up')" onclick="move('up')">▲</button><br>
    <button ontouchstart="move('left')" onclick="move('left')">◄</button>
    <button ontouchstart="move('right')" onclick="move('right')">►</button><br>
    <button ontouchstart="move('down')" onclick="move('down')">▼</button>
  </div>
  <button id="action" ontouchstart="interact()" onclick="interact()">A</button>
</div>
<script>
let player = {x:200, y:200, wood:0, cloth:0, food:0, water:0, metal:0};
let trees = [], animals = [];
const game = document.getElementById('game');
const playerEl = document.getElementById('player');
const ui = document.getElementById('ui');

function updateUI(){
  ui.innerHTML = `
    <span class="ui-item">🌳 ${player.wood}</span>
    <span class="ui-item">🧵 ${player.cloth}</span>
    <span class="ui-item">🥫 ${player.food}</span>
    <span class="ui-item">💧 ${player.water}</span>
    <span class="ui-item">🪙 ${player.metal}</span>
  `;
}

function spawnTrees(){
  for(let i=0;i<8;i++){
    let t = document.createElement('div');
    t.className='tree';
    t.textContent='🌲';
    t.style.left=Math.random()*85+5+'%';
    t.style.top=Math.random()*85+5+'%';
    t.style.animationDelay=Math.random()*3+'s';
    game.appendChild(t);
    trees.push(t);
  }
}

function spawnAnimals(){
  for(let i=0;i<3;i++){
    let s = document.createElement('div');
    s.className='animal';
    s.textContent='🐑';
    s.style.left=Math.random()*85+5+'%';
    s.style.top=Math.random()*85+5+'%';
    s.dataset.type='sheep';
    s.style.animationDelay=Math.random()*2+'s';
    game.appendChild(s);
    animals.push(s);
    
    let d = document.createElement('div');
    d.className='animal';
    d.textContent='🦌';
    d.style.left=Math.random()*85+5+'%';
    d.style.top=Math.random()*85+5+'%';
    d.dataset.type='deer';
    d.style.animationDelay=Math.random()*2+'s';
    game.appendChild(d);
    animals.push(d);
  }
}

function move(dir){
  if(dir==='up'||dir==='w') player.y=Math.max(10,player.y-18);
  if(dir==='down'||dir==='s') player.y=Math.min(window.innerHeight-40,player.y+18);
  if(dir==='left'||dir==='a') player.x=Math.max(10,player.x-18);
  if(dir==='right'||dir==='d') player.x=Math.min(window.innerWidth-40,player.x+18);
  playerEl.style.left=player.x+'px';
  playerEl.style.top=player.y+'px';
}

function interact(){
  let px=player.x+14, py=player.y+14; // center of player
  trees.forEach((t,i)=>{
    let tx=parseInt(t.style.left)/100*window.innerWidth+16;
    let ty=parseInt(t.style.top)/100*window.innerHeight+16;
    if(Math.abs(px-tx)<35 && Math.abs(py-ty)<35){
      player.wood+=2;
      t.style.animation='none';
      t.style.transform='scale(0)';
      setTimeout(()=>t.remove(),200);
      trees.splice(i,1);
      updateUI();
    }
  });
  animals.forEach((a,i)=>{
    let ax=parseInt(a.style.left)/100*window.innerWidth+13;
    let ay=parseInt(a.style.top)/100*window.innerHeight+13;
    if(Math.abs(px-ax)<35 && Math.abs(py-ay)<35){
      if(a.dataset.type==='sheep'){ player.food+=1; player.cloth+=1; }
      if(a.dataset.type==='deer'){ player.food+=1; player.wood+=1; }
      a.style.animation='none';
      a.style.transform='scale(0)';
      setTimeout(()=>a.remove(),200);
      animals.splice(i,1);
      updateUI();
    }
  });
}

document.addEventListener('keydown',e=>{
  if(['w','a','s','d','ArrowUp','ArrowLeft','ArrowDown','ArrowRight'].includes(e.key)){
    e.preventDefault();
    let dir=e.key.replace('Arrow','').toLowerCase();
    move(dir);
  }
  if(e.key===' '||e.key==='Enter') interact();
});

playerEl.style.left=player.x+'px';
playerEl.style.top=player.y+'px';
spawnTrees();
spawnAnimals();
updateUI();
setInterval(()=>{ if(trees.length<8) spawnTrees(); },120000);

// Animals wander a bit
setInterval(()=>{
  animals.forEach(a=>{
    let x=parseFloat(a.style.left);
    let y=parseFloat(a.style.top);
    x += (Math.random()-0.5)*4;
    y += (Math.random()-0.5)*4;
    a.style.left=Math.max(5,Math.min(90,x))+'%';
    a.style.top=Math.max(5,Math.min(90,y))+'%';
  });
},3000);
</script>
</body>
</html>
