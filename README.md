<!DOCTYPE html>

<html lang="ja">
<head>
<meta charset="UTF-8">
<title>IGNIS WORLD</title>
<style>
  @import url('https://fonts.googleapis.com/css2?family=Rajdhani:wght@300;400;600;700&family=Orbitron:wght@400;700;900&display=swap');
  *{margin:0;padding:0;box-sizing:border-box;}
  body{background:#000;overflow:hidden;width:100vw;height:100vh;font-family:'Rajdhani',sans-serif;color:#fff;touch-action:none;}
  #canvas{display:block;position:fixed;top:0;left:0;width:100vw!important;height:100vh!important;}
  #hud{position:fixed;top:0;left:0;right:0;bottom:0;pointer-events:none;z-index:10;}
  #top-bar{position:absolute;top:20px;left:50%;transform:translateX(-50%);text-align:center;}
  #logo{font-family:'Orbitron',monospace;font-size:20px;font-weight:900;letter-spacing:8px;background:linear-gradient(135deg,#ff3300,#ff6600,#ffaa00);-webkit-background-clip:text;-webkit-text-fill-color:transparent;filter:drop-shadow(0 0 20px rgba(255,80,0,0.8));}
  #world-label{font-family:'Orbitron',monospace;font-size:8px;letter-spacing:5px;color:rgba(255,150,50,0.5);}
  #member-card{position:absolute;bottom:16px;left:16px;background:rgba(0,0,0,0.6);border:1px solid rgba(255,80,0,0.3);border-radius:8px;padding:8px 14px;min-width:160px;backdrop-filter:blur(10px);}
  .rank{font-family:'Orbitron',monospace;font-size:8px;letter-spacing:4px;color:#ff6600;margin-bottom:3px;}
  .name{font-size:15px;font-weight:600;letter-spacing:2px;margin-bottom:6px;}
  .coins{display:flex;align-items:center;gap:7px;}
  .coin-icon{width:13px;height:13px;background:linear-gradient(135deg,#ffaa00,#ff6600);border-radius:50%;box-shadow:0 0 8px rgba(255,150,0,0.8);}
  .coin-amount{font-family:'Orbitron',monospace;font-size:13px;font-weight:700;color:#ffaa00;}
  #flight-info{position:absolute;bottom:90px;left:16px;display:flex;flex-direction:row;gap:20px;}
  .info-label{font-size:8px;letter-spacing:4px;color:rgba(255,100,0,0.5);text-transform:uppercase;}
  .info-value{font-family:'Orbitron',monospace;font-size:18px;font-weight:700;color:rgba(255,200,100,0.9);}
  .info-unit{font-size:9px;color:rgba(255,150,50,0.5);letter-spacing:2px;}
  #minimap{position:absolute;bottom:16px;right:16px;width:110px;height:110px;border:1px solid rgba(255,80,0,0.3);border-radius:4px;background:rgba(0,0,0,0.35);overflow:hidden;pointer-events:all;cursor:pointer;backdrop-filter:blur(4px);}
  #minimap-label{position:absolute;top:5px;left:7px;font-size:6px;letter-spacing:3px;color:rgba(255,100,0,0.5);z-index:1;}
  #notification{position:absolute;top:72px;left:50%;transform:translateX(-50%);background:rgba(255,80,0,0.15);border:1px solid rgba(255,80,0,0.5);border-radius:4px;padding:8px 20px;font-family:'Orbitron',monospace;font-size:10px;letter-spacing:4px;color:#ff8800;opacity:0;transition:opacity 0.5s;white-space:nowrap;}
  #notification.show{opacity:1;}
  #crosshair{position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);width:18px;height:18px;}
  #crosshair::before,#crosshair::after{content:'';position:absolute;background:rgba(255,100,0,0.45);}
  #crosshair::before{width:1px;height:100%;left:50%;top:0;}
  #crosshair::after{width:100%;height:1px;top:50%;left:0;}
  #kb-controls{position:absolute;bottom:30px;left:50%;transform:translateX(-50%);display:flex;gap:16px;opacity:0.3;}
  .ctrl{display:flex;flex-direction:column;align-items:center;gap:3px;}
  .ctrl-key{font-family:'Orbitron',monospace;font-size:8px;border:1px solid rgba(255,100,0,0.4);border-radius:3px;padding:2px 5px;color:rgba(255,150,50,0.8);}
  .ctrl-desc{font-size:7px;letter-spacing:2px;color:rgba(255,100,0,0.5);}
  #scanlines{position:fixed;top:0;left:0;right:0;bottom:0;background:repeating-linear-gradient(0deg,transparent,transparent 2px,rgba(0,0,0,0.03) 2px,rgba(0,0,0,0.03) 4px);pointer-events:none;z-index:5;}
  #vignette{display:none;}
  #mobile-controls{display:none;position:fixed;bottom:0;left:0;right:0;height:220px;pointer-events:none;z-index:20;}
  #jleft-wrap{position:absolute;bottom:30px;left:30px;pointer-events:all;}
  #jright-wrap{position:absolute;bottom:30px;right:130px;pointer-events:all;}
  .j-base{width:100px;height:100px;border-radius:50%;background:rgba(0,0,0,0.45);border:1.5px solid rgba(255,80,0,0.35);position:relative;}
  .j-knob{width:40px;height:40px;border-radius:50%;background:radial-gradient(circle,rgba(255,100,0,0.85),rgba(200,30,0,0.5));border:1px solid rgba(255,100,0,0.7);position:absolute;top:50%;left:50%;transform:translate(-50%,-50%);box-shadow:0 0 14px rgba(255,80,0,0.6);}
  .j-label{font-family:'Orbitron',monospace;font-size:7px;letter-spacing:3px;color:rgba(255,100,0,0.4);text-align:center;margin-top:6px;}
  #altitude-btns{position:absolute;bottom:30px;right:22px;display:flex;flex-direction:column;gap:10px;pointer-events:all;}
  .alt-btn{width:48px;height:48px;border-radius:50%;background:rgba(0,0,0,0.45);border:1.5px solid rgba(255,80,0,0.35);display:flex;align-items:center;justify-content:center;font-size:20px;color:rgba(255,120,0,0.8);cursor:pointer;user-select:none;}
  @media(max-width:768px){
    #mobile-controls{display:block;}
    #kb-controls{display:none;}
    #flight-info{bottom:160px;}
    #minimap{bottom:140px;}
  }
  #loading{position:fixed;top:0;left:0;right:0;bottom:0;background:#000;display:flex;flex-direction:column;align-items:center;justify-content:center;z-index:100;transition:opacity 1s;}
  #loading.fade{opacity:0;pointer-events:none;}
  #loading-logo{font-family:'Orbitron',monospace;font-size:clamp(32px,8vw,52px);font-weight:900;letter-spacing:clamp(8px,3vw,18px);background:linear-gradient(135deg,#ff3300,#ff6600,#ffaa00);-webkit-background-clip:text;-webkit-text-fill-color:transparent;filter:drop-shadow(0 0 40px rgba(255,80,0,0.8));margin-bottom:8px;}
  #loading-sub{font-size:10px;letter-spacing:7px;color:rgba(255,150,50,0.5);margin-bottom:44px;}
  #loading-bar-wrap{width:180px;height:1px;background:rgba(255,80,0,0.2);}
  #loading-bar{height:1px;background:linear-gradient(90deg,#ff3300,#ffaa00);width:0%;box-shadow:0 0 10px rgba(255,100,0,0.8);transition:width 0.1s;}
  #loading-text{font-family:'Orbitron',monospace;font-size:8px;letter-spacing:4px;color:rgba(255,100,0,0.4);margin-top:14px;}

#build-btn{
position:fixed;left:16px;top:50%;transform:translateY(-50%);
z-index:30;pointer-events:all;
background:rgba(0,0,0,0.75);border:1px solid rgba(255,80,0,0.5);border-radius:12px;
padding:10px 12px;font-family:‘Orbitron’,monospace;font-size:9px;letter-spacing:2px;
color:#ff8800;cursor:pointer;backdrop-filter:blur(10px);transition:all 0.2s;
}
#build-btn:active{background:rgba(255,80,0,0.2);}
#recovery-btn{
position:fixed;left:16px;top:calc(50% + 52px);
z-index:30;pointer-events:all;
background:rgba(0,0,0,0.75);border:1px solid rgba(255,60,60,0.5);border-radius:12px;
padding:10px 8px;font-family:‘Orbitron’,monospace;font-size:8px;letter-spacing:2px;
color:#ff6644;cursor:pointer;backdrop-filter:blur(10px);transition:all 0.2s;
}
#recovery-btn.active{background:rgba(255,60,0,0.3);border-color:rgba(255,80,0,0.9);color:#ff8800;}
#recovery-btn:active{background:rgba(255,40,0,0.3);}
@media(max-width:768px){
#recovery-btn{left:10px;top:calc(50% + 52px);}
}

#build-menu{
position:fixed;left:60px;top:50%;transform:translateY(-50%);
z-index:30;background:rgba(0,0,0,0.92);
border:1px solid rgba(255,80,0,0.4);border-radius:12px;
padding:12px;width:min(340px, calc(100vw - 80px));
max-height:min(90vh,85dvh);
overflow-y:auto;-webkit-overflow-scrolling:touch;
backdrop-filter:blur(16px);display:none;
box-shadow:0 0 30px rgba(255,80,0,0.2);
}
#build-menu.open{display:block;}
#build-menu h3{font-family:‘Orbitron’,monospace;font-size:9px;letter-spacing:4px;
color:rgba(255,120,0,0.7);margin-bottom:10px;text-align:center;}
.build-section{font-family:‘Orbitron’,monospace;font-size:7px;letter-spacing:3px;
color:rgba(255,100,0,0.4);margin:8px 0 4px;padding-bottom:2px;
border-bottom:1px solid rgba(255,80,0,0.15);}
.build-grid{display:grid;grid-template-columns:repeat(3,1fr);gap:6px;margin-bottom:4px;}
.build-item{background:rgba(255,80,0,0.07);border:1px solid rgba(255,80,0,0.2);
border-radius:8px;padding:8px 4px;cursor:pointer;transition:all 0.15s;text-align:center;}
.build-item:active,.build-item:hover{background:rgba(255,80,0,0.2);border-color:rgba(255,80,0,0.5);}
.bi-icon{font-size:18px;line-height:1.2;}
.bi-name{font-family:‘Orbitron’,monospace;font-size:7px;letter-spacing:1px;color:#ff8800;
display:block;margin-top:3px;white-space:nowrap;overflow:hidden;text-overflow:ellipsis;}
.bi-cost{font-size:8px;color:rgba(255,150,50,0.55);margin-top:1px;}
#build-close{display:block;text-align:center;margin-top:8px;padding:6px;
font-family:‘Orbitron’,monospace;font-size:9px;letter-spacing:3px;
color:rgba(255,80,0,0.5);cursor:pointer;border-top:1px solid rgba(255,80,0,0.15);}

@media (orientation:landscape) and (max-height:500px){
#build-menu{
left:60px;top:4px;transform:none;
max-height:calc(100dvh - 8px);width:min(320px,calc(100vw - 80px));
}
#build-btn{top:50%;transform:translateY(-50%);}
}
@media(max-width:768px){
#build-btn{left:10px;}
#build-menu{left:55px;}
}
</style>

</head>
<body>
<div id="loading">
  <div id="loading-logo">IGNIS</div>
  <div id="loading-sub">WORLD — ENTER THE CITY</div>
  <div id="loading-bar-wrap"><div id="loading-bar"></div></div>
  <div id="loading-text">INITIALIZING...</div>
</div>
<canvas id="canvas"></canvas>
<div id="scanlines"></div>
<div id="vignette"></div>
<div id="hud">
  <div id="top-bar"><div id="logo">IGNIS</div><div id="world-label">WORLD — IGNIS CITY</div></div>
  <div id="member-card">
    <div class="rank">RANK 4 — 虹</div>
    <div class="name">MEMBER</div>
    <div class="coins"><div class="coin-icon"></div><div class="coin-amount">2,480</div><div style="font-size:9px;color:rgba(255,150,50,0.5);letter-spacing:2px;">COINS</div></div>
  </div>
  <div id="crosshair"></div>
  <div id="flight-info">
    <div><div class="info-label">altitude</div><div class="info-value" id="alt-value">100</div><div class="info-unit">meters</div></div>
    <div><div class="info-label">speed</div><div class="info-value" id="spd-value">0</div><div class="info-unit">km/h</div></div>
  </div>
  <div id="kb-controls">
    <div class="ctrl"><div class="ctrl-key">W/S</div><div class="ctrl-desc">前後</div></div>
    <div class="ctrl"><div class="ctrl-key">A/D</div><div class="ctrl-desc">左右</div></div>
    <div class="ctrl"><div class="ctrl-key">SPACE</div><div class="ctrl-desc">上昇</div></div>
    <div class="ctrl"><div class="ctrl-key">SHIFT</div><div class="ctrl-desc">下降</div></div>
    <div class="ctrl"><div class="ctrl-key">DRAG</div><div class="ctrl-desc">視点</div></div>
  </div>
  <div id="minimap" onclick="toggleMinimap()" style="cursor:pointer;z-index:15;position:absolute;">
    <div id="minimap-label">IGNIS CITY</div>
    <canvas id="minimap-canvas" width="110" height="110"></canvas>
    <div style="position:absolute;bottom:4px;right:6px;font-size:7px;font-family:Orbitron,monospace;color:rgba(255,100,0,0.4);">TAP</div>
  </div>
  <div id="notification">IGNIS CITY — 全エリア解放済み</div>
</div>

<div id="build-btn" onclick="toggleBuildMenu()">BUILD</div>
<div id="recovery-btn" onclick="toggleRecoveryMode()">UNDO</div>
<div id="build-menu">
  <h3>⚙ BUILD MENU</h3>
  <div class="build-section">NATURE</div>
  <div class="build-grid">
    <div class="build-item" onclick="buildItem('grass')"><div class="bi-icon">🌿</div><span class="bi-name">GRASS</span><div class="bi-cost">5c</div></div>
    <div class="build-item" onclick="buildItem('tree')"><div class="bi-icon">🌳</div><span class="bi-name">TREE</span><div class="bi-cost">10c</div></div>
    <div class="build-item" onclick="buildItem('mountain')"><div class="bi-icon">⛰️</div><span class="bi-name">MOUNTAIN</span><div class="bi-cost">150c</div></div>
    <div class="build-item" onclick="buildItem('river')"><div class="bi-icon">🌊</div><span class="bi-name">RIVER</span><div class="bi-cost">80c</div></div>
    <div class="build-item" onclick="buildItem('lake')"><div class="bi-icon">💧</div><span class="bi-name">LAKE</span><div class="bi-cost">120c</div></div>
    <div class="build-item" onclick="buildItem('fountain')"><div class="bi-icon">⛲</div><span class="bi-name">FOUNTAIN</span><div class="bi-cost">60c</div></div>
  </div>
  <div class="build-section">ROAD & TRANSPORT</div>
  <div class="build-grid">
    <div class="build-item" onclick="buildItem('road')"><div class="bi-icon">&#x1F6E3;</div><span class="bi-name">ROAD</span><div class="bi-cost">20c</div></div>
    <div class="build-item" onclick="buildItem('roadcurveR')"><div class="bi-icon">&#x27A1;</div><span class="bi-name">CURVE R</span><div class="bi-cost">20c</div></div>
    <div class="build-item" onclick="buildItem('roadcurveL')"><div class="bi-icon">&#x2B05;</div><span class="bi-name">CURVE L</span><div class="bi-cost">20c</div></div>
    <div class="build-item" onclick="buildItem('streetlight')"><div class="bi-icon">&#x1F4A1;</div><span class="bi-name">LIGHT</span><div class="bi-cost">15c</div></div>
    <div class="build-item" onclick="buildItem('bridge')"><div class="bi-icon">&#x1F309;</div><span class="bi-name">BRIDGE</span><div class="bi-cost">100c</div></div>
    <div class="build-item" onclick="buildItem('footbridge')"><div class="bi-icon">&#x1F6B6;</div><span class="bi-name">WALKWAY</span><div class="bi-cost">60c</div></div>
    <div class="build-item" onclick="buildItem('car')"><div class="bi-icon">&#x1F697;</div><span class="bi-name">CAR</span><div class="bi-cost">30c</div></div>
    <div class="build-item" onclick="buildItem('bike')"><div class="bi-icon">&#x1F3CD;</div><span class="bi-name">BIKE</span><div class="bi-cost">20c</div></div>
    <div class="build-item" onclick="buildItem('person')"><div class="bi-icon">&#x1F9CD;</div><span class="bi-name">PERSON</span><div class="bi-cost">5c</div></div>
  </div>
  <div class="build-section">BUILDINGS</div>
  <div class="build-grid">
    <div class="build-item" onclick="buildItem('house')"><div class="bi-icon">🏠</div><span class="bi-name">HOUSE</span><div class="bi-cost">80c</div></div>
    <div class="build-item" onclick="buildItem('building')"><div class="bi-icon">🏢</div><span class="bi-name">BUILDING</span><div class="bi-cost">50c</div></div>
    <div class="build-item" onclick="buildItem('school')"><div class="bi-icon">🏫</div><span class="bi-name">SCHOOL</span><div class="bi-cost">200c</div></div>
    <div class="build-item" onclick="buildItem('gym')"><div class="bi-icon">🏋️</div><span class="bi-name">GYM</span><div class="bi-cost">150c</div></div>
  </div>
  <div class="build-section">SPORTS</div>
  <div class="build-grid">
    <div class="build-item" onclick="buildItem('schoolyard')"><div class="bi-icon">⬜</div><span class="bi-name">SCHOOLYARD</span><div class="bi-cost">100c</div></div>
    <div class="build-item" onclick="buildItem('tennis')"><div class="bi-icon">🎾</div><span class="bi-name">TENNIS</span><div class="bi-cost">120c</div></div>
    <div class="build-item" onclick="buildItem('baseball')"><div class="bi-icon">⚾</div><span class="bi-name">BASEBALL</span><div class="bi-cost">250c</div></div>
  </div>
  <div id="build-close" onclick="toggleBuildMenu()">✕ CLOSE</div>
</div>

<div id="mobile-controls">
  <div id="jleft-wrap"><div class="j-base" id="jleft-base"><div class="j-knob" id="jleft-knob"></div></div><div class="j-label">MOVE</div></div>
  <div id="jright-wrap"><div class="j-base" id="jright-base"><div class="j-knob" id="jright-knob"></div></div><div class="j-label">LOOK</div></div>
  <!-- FIX #2: btn-down HTML was broken (▲ icon, missing closing div) - fixed below -->
  <div id="altitude-btns">
    <div class="alt-btn" id="btn-up">▲</div>
    <div class="alt-btn" id="btn-down">▼</div>
  </div>
</div>

<script>
// FIX #1: Added 'three-ready' event listener so ignis_startGame() actually gets called
(function(){
  var loadBar = document.getElementById('loading-bar');
  var loadText = document.getElementById('loading-text');
  var progress = 0;
  var progressInterval = setInterval(function(){
    progress = Math.min(progress + Math.random() * 8, 85);
    if(loadBar) loadBar.style.width = progress + '%';
  }, 150);

  var cdns=[
    "https://cdn.jsdelivr.net/npm/three@0.128.0/build/three.min.js",
    "https://cdnjs.cloudflare.com/ajax/libs/three.js/r128/three.min.js",
    "https://unpkg.com/three@0.128.0/build/three.min.js"
  ];
  var idx=0;
  function tryLoad(){
    if(idx>=cdns.length){
      clearInterval(progressInterval);
      if(loadText) loadText.textContent='ERROR: Network unavailable';
      return;
    }
    if(loadText) loadText.textContent='LOADING THREE.JS (' + (idx+1) + '/' + cdns.length + ')...';
    var s=document.createElement('script');
    s.src=cdns[idx];
    s.onload=function(){
      if(typeof THREE!=='undefined'){
        clearInterval(progressInterval);
        if(loadBar) loadBar.style.width='100%';
        if(loadText) loadText.textContent='LAUNCHING IGNIS WORLD...';
        setTimeout(function(){
          document.dispatchEvent(new Event('three-ready'));
        }, 300);
      } else {
        idx++; tryLoad();
      }
    };
    s.onerror=function(){ idx++; tryLoad(); };
    document.head.appendChild(s);
  }
  tryLoad();
})();
</script>

<script>
window.onerror=function(msg,src,line,col,err){
  if(msg==='Script error.' && line===0) return false;
  var d=document.getElementById('err-dbg');
  if(!d){
    d=document.createElement('div');
    d.id='err-dbg';
    d.style.cssText='position:fixed;top:10%;left:5%;right:5%;background:rgba(0,0,0,0.95);color:#ff4444;font-size:12px;padding:12px;z-index:99999;border:2px solid #ff4444;border-radius:6px;word-break:break-all;font-family:monospace;max-height:60vh;overflow-y:auto;';
    document.body.appendChild(d);
  }
  d.innerHTML+='<div>ERR: '+msg+'<br>L'+line+':'+col+'</div><hr>';
  return false;
};

// FIX #1: Listen for three-ready event to start game
document.addEventListener('three-ready', function(){
  try {
    initGame();
    ignis_startGame();
  } catch(e) {
    var loadText = document.getElementById('loading-text');
    var loadEl = document.getElementById('loading');
    if(loadText){ loadText.textContent='ERR: '+(e.message||'unknown'); loadText.style.color='#f44'; }
    if(loadEl){ loadEl.style.opacity='1'; loadEl.style.display='flex'; }
    console.error('Game init error:', e);
  }
});

var placedObjects = [];
var growRegistry = {};
var playerCoins = 5000;
var recoveryMode = false;
var t = 0;
var _lastT = performance.now();

var TILE=12;
var movingObjects=[];
var riverMeshes=[];
var fishMeshes=[];
var roadTiles=new Set();

var canvas=document.getElementById('canvas');
var mmCanvas=document.getElementById('minimap-canvas');
var mmCtx=mmCanvas?mmCanvas.getContext('2d'):null;
var minimapExpanded=false;

// Scene, camera, renderer, lights declared in initGame
var scene, camera, renderer, ambientLight, sun, towerLight;
var windowMeshes=[];
var topGlow, starsMat, brightStarsMat, sunSphere, sunGlow, sunGlowMat, sunGlow2, sunGlow2Mat;
var moonSphere, moonGlow, moonGlowMat;
var timeLabelEl;
var yaw=Math.PI, pitch=-0.15, isDragging=false, lastX=0, lastY=0;
var vel;
var keys={};
var jlX=0,jlY=0,jrX=0,jrY=0;
var jlTid=null,jrTid=null;
var jlCx=0,jlCy=0,jrCx=0,jrCy=0;
var JOY_MAX=38;
var btnUp=false,btnDown=false;
var altEl,spdEl;

var buildingPalettes=[
  {base:0x4a5568,glass:0x2d4a6e,trim:0x718096},
  {base:0x5a4a3a,glass:0x3d5a6e,trim:0x8a7a6a},
  {base:0x3d4a5a,glass:0x1a3a5a,trim:0x607080},
  {base:0x6a6a5a,glass:0x2a4a3a,trim:0x8a8a7a},
  {base:0x5a3a3a,glass:0x3a2a4a,trim:0x7a5a5a},
  {base:0x4a4a6a,glass:0x2a3a6a,trim:0x6a6a8a},
];

var BUILD_COSTS={bridge:100,footbridge:60,roadcurveR:20,roadcurveL:20,streetlight:15,grass:5,tree:10,mountain:150,river:80,lake:120,fountain:60,road:20,car:30,bike:20,person:5,house:80,building:50,school:200,gym:150,schoolyard:100,tennis:120,baseball:250};

var TIME_CYCLE = 120;
var timeColors = [
  {sky:0xff6622,fog:0xcc4411,amb:0x331100,sun:0xff8844,si:0.7,label:'DAWN'},
  {sky:0x88ccee,fog:0x66aacc,amb:0x223344,sun:0xffffff,si:0.8,label:'MORNING'},
  {sky:0xd0e8f8,fog:0xaaccdd,amb:0x334455,sun:0xffffff,si:1.0,label:'DAY'},
  {sky:0xff9944,fog:0xdd6622,amb:0x331100,sun:0xffbb44,si:0.9,label:'SUNSET'},
  {sky:0x000102,fog:0x000000,amb:0x010102,sun:0x050a1a,si:0.02,label:'NIGHT'},
];

var bD2=[[20,20,130],[-20,20,100],[20,-20,115],[-20,-20,90],[35,35,150],[-35,35,75],[35,-35,110],[-35,-35,140],[80,80,65],[-80,80,50],[80,-80,60],[-80,-80,45],[110,0,38],[-110,0,32],[0,110,42],[0,-110,35],[50,10,55],[-50,10,45],[10,50,70],[-10,-50,52],[160,160,22],[-160,160,18],[160,-160,24],[-160,-160,16]];

function lerpColor(a,b,t){
  var ar=(a>>16)&0xff,ag=(a>>8)&0xff,ab=a&0xff;
  var br=(b>>16)&0xff,bg=(b>>8)&0xff,bb=b&0xff;
  return ((Math.round(ar+(br-ar)*t)<<16)|(Math.round(ag+(bg-ag)*t)<<8)|Math.round(ab+(bb-ab)*t));
}

function updateCoinDisplay(){
  var el=document.querySelector('.coin-amount');
  if(el) el.textContent=playerCoins.toLocaleString();
}

function showNotif(msg){
  var n=document.getElementById('notification');
  n.textContent=msg;n.classList.add('show');
  setTimeout(function(){n.classList.remove('show');},2500);
}

function trackPlaced(meshes, type, cost, x, z){
  placedObjects.push({meshes: meshes.slice(), type: type, cost: cost, x: x, z: z});
}

function growKey(x,z,type){ return x+'_'+z+'_'+type; }

function removeMeshGroup(meshes){
  if(!meshes) return;
  meshes.forEach(function(m){ scene.remove(m); });
}

function toggleMinimap(){
  minimapExpanded=!minimapExpanded;
  var mm=document.getElementById('minimap');
  if(mm){
    mm.style.width=minimapExpanded?'240px':'110px';
    mm.style.height=minimapExpanded?'240px':'110px';
    if(mmCanvas){mmCanvas.width=minimapExpanded?240:110;mmCanvas.height=minimapExpanded?240:110;}
  }
}

function mkB(x,z,w,d,h){
  var pal=buildingPalettes[Math.floor(Math.random()*buildingPalettes.length)];
  var bMat2=new THREE.MeshStandardMaterial({color:pal.base,roughness:0.4,metalness:0.6});
  var glassMat=new THREE.MeshStandardMaterial({color:pal.glass,roughness:0.05,metalness:0.8,transparent:true,opacity:0.7});
  var trimMat=new THREE.MeshStandardMaterial({color:pal.trim,roughness:0.3,metalness:0.7});
  var body2=new THREE.Mesh(new THREE.BoxGeometry(w,h,d),bMat2);
  body2.castShadow=true;body2.receiveShadow=true;
  body2.position.set(x,h/2,z);scene.add(body2);
  var gw=new THREE.Mesh(new THREE.BoxGeometry(w*0.96,h*0.95,0.5),glassMat);
  gw.position.set(x,h/2,z+d/2+0.1);scene.add(gw);
  var gb=new THREE.Mesh(new THREE.BoxGeometry(w*0.96,h*0.95,0.5),glassMat);
  gb.position.set(x,h/2,z-d/2-0.1);scene.add(gb);
  [w/2,-w/2].forEach(function(cx){
    [d/2,-d/2].forEach(function(cz){
      var trim2=new THREE.Mesh(new THREE.BoxGeometry(1.2,h,1.2),trimMat);
      trim2.position.set(x+cx,h/2,z+cz);scene.add(trim2);
    });
  });
  var floorH=Math.max(5, h/Math.round(h/8));
  var floors=Math.round(h/floorH);
  for(var fi=1;fi<floors;fi++){
    var slab=new THREE.Mesh(new THREE.BoxGeometry(w+0.8,0.5,d+0.8),trimMat);
    slab.position.set(x,fi*floorH,z);scene.add(slab);
  }
  var roofMat=new THREE.MeshStandardMaterial({color:0x555555,roughness:0.7,metalness:0.4});
  var roofBox=new THREE.Mesh(new THREE.BoxGeometry(w*0.3,Math.random()*4+3,d*0.3),roofMat);
  roofBox.position.set(x+(Math.random()-0.5)*w*0.3,h+roofBox.geometry.parameters.height/2,z+(Math.random()-0.5)*d*0.3);
  scene.add(roofBox);
  var antenna=new THREE.Mesh(new THREE.CylinderGeometry(0.1,0.1,6+Math.random()*4,4),trimMat);
  antenna.position.set(x+(Math.random()-0.5)*w*0.4,h+antenna.geometry.parameters.height/2,z+(Math.random()-0.5)*d*0.4);
  scene.add(antenna);
}

function mkR(x,z,w,l,v){
  var r=new THREE.Mesh(new THREE.PlaneGeometry(v?w:l,v?l:w),new THREE.MeshStandardMaterial({color:0x555555,roughness:0.9,metalness:0.1}));r.rotation.x=-Math.PI/2;r.position.set(x,0.05,z);scene.add(r);
  var ln=new THREE.Mesh(new THREE.PlaneGeometry(v?0.4:l*0.85,v?l*0.85:0.4),new THREE.MeshBasicMaterial({color:0xffffff,transparent:true,opacity:0.25}));ln.rotation.x=-Math.PI/2;ln.position.set(x,0.1,z);scene.add(ln);
}

function drawMM(){
  if(!mmCanvas||!mmCtx) return;
  var W=mmCanvas.width;
  var mapRange=minimapExpanded?3000:700;
  var sc=W/mapRange/2, cx=W/2, cy=W/2;
  var dot=minimapExpanded?4:2;
  mmCtx.fillStyle='#060608';mmCtx.fillRect(0,0,W,W);
  mmCtx.strokeStyle='rgba(255,60,0,0.15)';mmCtx.lineWidth=1;
  for(var g=-3;g<=3;g++){
    var gx=cx+g*(100*sc*2),gy=cy+g*(100*sc*2);
    mmCtx.beginPath();mmCtx.moveTo(gx,0);mmCtx.lineTo(gx,W);mmCtx.stroke();
    mmCtx.beginPath();mmCtx.moveTo(0,gy);mmCtx.lineTo(W,gy);mmCtx.stroke();
  }
  bD2.forEach(function(_a){var bx=_a[0],bz=_a[1],h=_a[2];
    var px=cx+bx*sc,pz=cy+bz*sc;
    var br=Math.min(255,60+(h/150)*195), alpha=0.4+(h/150)*0.5;
    mmCtx.fillStyle='rgba(255,'+br+',0,'+alpha+')';
    mmCtx.fillRect(px-dot,pz-dot,dot*2,dot*2);
  });
  mmCtx.fillStyle='rgba(255,80,0,0.9)';
  mmCtx.beginPath();mmCtx.arc(cx,cy,dot*1.5,0,Math.PI*2);mmCtx.fill();
  if(minimapExpanded){
    mmCtx.font='12px Orbitron,monospace';
    mmCtx.fillStyle='rgba(255,100,0,0.5)';
    mmCtx.textAlign='center';
    mmCtx.fillText('N',cx,16);mmCtx.fillText('S',cx,W-6);
    mmCtx.fillText('W',12,cy+5);mmCtx.fillText('E',W-12,cy+5);
  }
  var px=Math.max(dot+2,Math.min(W-dot-2,cx+camera.position.x*sc));
  var pz=Math.max(dot+2,Math.min(W-dot-2,cy+camera.position.z*sc));
  var arrowLen=minimapExpanded?20:10;
  mmCtx.strokeStyle='rgba(255,255,255,0.6)';mmCtx.lineWidth=1.5;
  mmCtx.beginPath();mmCtx.moveTo(px,pz);
  mmCtx.lineTo(
    Math.max(dot+2,Math.min(W-dot-2,px+Math.sin(yaw)*arrowLen)),
    Math.max(dot+2,Math.min(W-dot-2,pz+Math.cos(yaw)*arrowLen))
  );mmCtx.stroke();
  mmCtx.fillStyle='#ffffff';
  mmCtx.beginPath();mmCtx.arc(px,pz,dot+1,0,Math.PI*2);mmCtx.fill();
  if(minimapExpanded){
    mmCtx.strokeStyle='rgba(255,255,255,0.15)';mmCtx.lineWidth=1;
    mmCtx.beginPath();
    mmCtx.moveTo(px,pz);
    mmCtx.arc(px,pz,40,yaw-0.6+Math.PI/2,yaw+0.6+Math.PI/2);
    mmCtx.closePath();mmCtx.stroke();
    mmCtx.fillStyle='rgba(255,255,255,0.04)';mmCtx.fill();
  }
}

function toggleBuildMenu(){
  var m=document.getElementById('build-menu');
  m.classList.toggle('open');
}

function placePos(){
  var sinY=Math.sin(yaw), cosY=Math.cos(yaw);
  var sinP=Math.sin(pitch), cosP=Math.cos(pitch);
  var camH=Math.max(camera.position.y, 2);
  var groundDist = cosP > 0.05 ? camH / cosP : TILE * 4;
  var clampedDist = Math.min(groundDist, TILE * 6);
  var px = camera.position.x - sinY * cosP * clampedDist;
  var pz = camera.position.z - cosY * cosP * clampedDist;
  return {
    x: Math.round(px / TILE) * TILE,
    z: Math.round(pz / TILE) * TILE
  };
}

function buildItem(type){
  try{
    var cost=BUILD_COSTS[type]||0;
    if(playerCoins<cost){ showNotif('コインが足りません ('+cost+'c必要)'); return; }
    playerCoins-=cost;
    updateCoinDisplay();
    toggleBuildMenu();
    var pos=placePos();
    var x=pos.x, z=pos.z;

    if(type==='grass'){ var b0=scene.children.length; buildGrass(x,z); placedObjects.push({meshes:scene.children.slice(b0),type:type,cost:cost,x:x,z:z}); showNotif('草むらを配置！'); }
    else if(type==='tree'){ var b1=scene.children.length; buildTree(x,z); placedObjects.push({meshes:scene.children.slice(b1),type:type,cost:cost,x:x,z:z}); showNotif('木を植えました！'); }
    else if(type==='river'){ var b2=scene.children.length; buildRiver(x,z); placedObjects.push({meshes:scene.children.slice(b2),type:type,cost:cost,x:x,z:z}); showNotif('川を配置！'); }
    else if(type==='lake'){ var b3=scene.children.length; buildLake(x,z); placedObjects.push({meshes:scene.children.slice(b3),type:type,cost:cost,x:x,z:z}); showNotif('湖を作成！'); }
    else if(type==='fountain'){ var b4=scene.children.length; buildFountain(x,z); placedObjects.push({meshes:scene.children.slice(b4),type:type,cost:cost,x:x,z:z}); showNotif('噴水を設置！'); }
    else if(type==='gym'){ var b5=scene.children.length; buildGym(x,z); placedObjects.push({meshes:scene.children.slice(b5),type:type,cost:cost,x:x,z:z}); showNotif('体育館！'); }
    else if(type==='schoolyard'){ var b6=scene.children.length; buildSchoolyard(x,z); placedObjects.push({meshes:scene.children.slice(b6),type:type,cost:cost,x:x,z:z}); showNotif('校庭！'); }
    else if(type==='tennis'){ var b7=scene.children.length; buildTennisCourt(x,z); placedObjects.push({meshes:scene.children.slice(b7),type:type,cost:cost,x:x,z:z}); showNotif('テニスコート！'); }
    else if(type==='baseball'){ var b8=scene.children.length; buildBaseballField(x,z); placedObjects.push({meshes:scene.children.slice(b8),type:type,cost:cost,x:x,z:z}); showNotif('野球場！'); }
    else if(type==='road'){
      var rm=new THREE.Mesh(new THREE.PlaneGeometry(TILE,TILE),new THREE.MeshStandardMaterial({color:0x2a2a2a,roughness:0.92}));
      rm.rotation.x=-Math.PI/2; rm.position.set(x,0.12,z); rm.receiveShadow=true; scene.add(rm);
      var lm2=new THREE.Mesh(new THREE.PlaneGeometry(TILE*0.06,TILE),new THREE.MeshBasicMaterial({color:0xffdd00,transparent:true,opacity:0.45}));
      lm2.rotation.x=-Math.PI/2; lm2.position.set(x,0.13,z); scene.add(lm2);
      var rk=x+'_'+z; roadTiles.add(rk);
      placedObjects.push({meshes:[rm,lm2],type:'road',cost:cost,x:x,z:z,roadKey:rk});
      showNotif('道路を敷設！');
    }
    else if(type==='roadcurveR'){ buildRoadCurve(x,z,'right'); showNotif('右カーブ！'); }
    else if(type==='roadcurveL'){ buildRoadCurve(x,z,'left'); showNotif('左カーブ！'); }
    else if(type==='streetlight'){ buildStreetlight(x,z); showNotif('街灯！'); }
    else if(type==='car'){ spawnCar(x,z); showNotif('車を配置！'); }
    else if(type==='bike'){ spawnBike(x,z); showNotif('バイクを配置！'); }
    else if(type==='person'){ spawnPerson(x,z); showNotif('人を配置！'); }
    else if(type==='bridge'){ var bm=buildBridge(x,z); placedObjects.push({meshes:bm,type:type,cost:cost,x:x,z:z}); showNotif('橋！'); }
    else if(type==='footbridge'){ var fm=buildFootbridge(x,z); placedObjects.push({meshes:fm,type:type,cost:cost,x:x,z:z}); showNotif('歩道橋！'); }
    else if(type==='mountain'){
      var mk=growKey(x,z,'mountain');
      if(!growRegistry[mk]) growRegistry[mk]={level:1,meshes:[]};
      else growRegistry[mk].level++;
      addGrowableMountain(x,z);
      placedObjects.push({meshes:growRegistry[mk].meshes.slice(),type:type,cost:cost,x:x,z:z,key:mk});
      showNotif('山 Lv'+growRegistry[mk].level+'！');
    }
    else if(type==='house'){
      var hk=growKey(x,z,'house');
      if(!growRegistry[hk]) growRegistry[hk]={level:1,meshes:[]};
      else growRegistry[hk].level=Math.min(growRegistry[hk].level+1,5);
      addGrowableHouse(x,z);
      placedObjects.push({meshes:growRegistry[hk].meshes.slice(),type:type,cost:cost,x:x,z:z,key:hk});
      showNotif('一軒家 Lv'+growRegistry[hk].level+'！');
    }
    else if(type==='building'){
      var bk=growKey(x,z,'building');
      if(!growRegistry[bk]) growRegistry[bk]={level:1,meshes:[]};
      else growRegistry[bk].level++;
      addGrowableBuilding(x,z,TILE*0.85,TILE*0.85,TILE);
      placedObjects.push({meshes:growRegistry[bk].meshes.slice(),type:type,cost:cost,x:x,z:z,key:bk});
      showNotif('ビル '+growRegistry[bk].level+'F！');
    }
    else if(type==='school'){
      var sk=growKey(x,z,'school');
      if(!growRegistry[sk]) growRegistry[sk]={level:1,meshes:[],maxLevel:5};
      else if(growRegistry[sk].level<5) growRegistry[sk].level++;
      else{ showNotif('学校は5階が最大！'); return; }
      addGrowableSchool(x,z);
      placedObjects.push({meshes:growRegistry[sk].meshes.slice(),type:type,cost:cost,x:x,z:z,key:sk});
      showNotif('学校 '+growRegistry[sk].level+'F！');
    }
    else{ showNotif('未対応: '+type); }
  }catch(err){
    var emsg=err&&err.message?err.message:'unknown';
    try{ showNotif('ERR:'+emsg.substring(0,25)); }catch(e2){}
    console.error('buildItem['+type+']:', emsg);
  }
}

function toggleRecoveryMode(){
  recoveryMode=!recoveryMode;
  var btn=document.getElementById('recovery-btn');
  if(recoveryMode){
    btn.classList.add('active');
    btn.textContent='CANCEL';
    showNotif('回収モード: 最後に置いたものを回収');
    recoverLast();
  } else {
    btn.classList.remove('active');
    btn.textContent='UNDO';
  }
}

function recoverLast(){
  var btn=document.getElementById('recovery-btn');
  btn.classList.remove('active');
  btn.textContent='UNDO';
  recoveryMode=false;
  if(placedObjects.length===0){ showNotif('回収できるものがありません'); return; }
  var last=placedObjects.pop();
  last.meshes.forEach(function(m){
    if(!m) return;
    scene.remove(m);
    for(var wi=windowMeshes.length-1;wi>=0;wi--){
      if(windowMeshes[wi].mesh===m) windowMeshes.splice(wi,1);
    }
  });
  if(last.movingRef){
    var mri=movingObjects.indexOf(last.movingRef);
    if(mri!==-1) movingObjects.splice(mri,1);
  }
  if(last.roadKey) roadTiles.delete(last.roadKey);
  if(last.key && growRegistry[last.key]){
    var reg=growRegistry[last.key];
    reg.meshes.forEach(function(m){ scene.remove(m); });
    reg.meshes=[];
    if(reg.level<=1){
      delete growRegistry[last.key];
    } else {
      reg.level--;
      var kp=last.key.split('_');
      var kx=parseFloat(kp[0]), kz=parseFloat(kp[1]), ktype=kp[2];
      if(ktype==='building') addGrowableBuilding(kx,kz,TILE*0.85,TILE*0.85,TILE);
      else if(ktype==='house') addGrowableHouse(kx,kz);
      else if(ktype==='school') addGrowableSchool(kx,kz);
      else if(ktype==='mountain') addGrowableMountain(kx,kz);
    }
  }
  playerCoins+=last.cost;
  updateCoinDisplay();
  showNotif('回収！ +'+last.cost+'コイン返還');
}

function spawnCar(sx,sz){
  var colors=[0xcc2200,0x1a3a8a,0x1a6a1a,0xccaa00,0x111111,0xf5f5f5,0x8833aa,0xff6600];
  var col=colors[Math.floor(Math.random()*colors.length)];
  var group=new THREE.Group();
  var bodyMat=new THREE.MeshStandardMaterial({color:col,roughness:0.15,metalness:0.9});
  var fgMat=new THREE.MeshStandardMaterial({color:0x88ccff,transparent:true,opacity:0.45,roughness:0.0,metalness:0.3});
  var lower=new THREE.Mesh(new THREE.BoxGeometry(4.0,0.9,7.6),bodyMat);
  lower.castShadow=true;lower.receiveShadow=true;lower.position.set(0,0.95,0); group.add(lower);
  var upper=new THREE.Mesh(new THREE.BoxGeometry(3.4,0.85,4.0),bodyMat);
  upper.position.set(0,1.83,-0.4); group.add(upper);
  var nose=new THREE.Mesh(new THREE.BoxGeometry(3.8,0.5,1.2),new THREE.MeshStandardMaterial({color:col,roughness:0.1,metalness:0.95}));
  nose.rotation.x=0.35; nose.position.set(0,1.1,3.6); group.add(nose);
  var fg=new THREE.Mesh(new THREE.BoxGeometry(3.2,0.8,0.12),fgMat);
  fg.rotation.x=0.45; fg.position.set(0,1.9,1.95); group.add(fg);
  var rg=new THREE.Mesh(new THREE.BoxGeometry(3.0,0.7,0.12),fgMat);
  rg.rotation.x=-0.35; rg.position.set(0,1.85,-2.35); group.add(rg);
  [1.72,-1.72].forEach(function(sw_x){
    var sw=new THREE.Mesh(new THREE.BoxGeometry(0.1,0.6,2.8),fgMat);
    sw.position.set(sw_x,1.88,-0.3); group.add(sw);
  });
  var wMat=new THREE.MeshStandardMaterial({color:0x111111,roughness:0.95});
  var rimMat=new THREE.MeshStandardMaterial({color:0xdddddd,roughness:0.1,metalness:1.0});
  [[1.95,2.6],[1.95,-2.6],[-1.95,2.6],[-1.95,-2.6]].forEach(function(wp){
    var wx=wp[0],wz=wp[1];
    var tire=new THREE.Mesh(new THREE.CylinderGeometry(0.75,0.75,0.55,20),wMat);
    tire.rotation.z=Math.PI/2; tire.position.set(wx,0.75,wz); group.add(tire);
    var rim=new THREE.Mesh(new THREE.CylinderGeometry(0.42,0.42,0.58,12),rimMat);
    rim.rotation.z=Math.PI/2; rim.position.set(wx,0.75,wz); group.add(rim);
    for(var sp=0;sp<5;sp++){
      var a=sp/5*Math.PI;
      var spoke=new THREE.Mesh(new THREE.BoxGeometry(0.55,0.06,0.06),rimMat);
      spoke.rotation.z=a; spoke.position.set(wx,0.75,wz); group.add(spoke);
    }
  });
  var hlMat=new THREE.MeshBasicMaterial({color:0xffffee});
  [[1.3,3.7],[-1.3,3.7]].forEach(function(lp){
    var hl=new THREE.Mesh(new THREE.BoxGeometry(0.55,0.28,0.12),hlMat);
    hl.position.set(lp[0],1.05,lp[1]); group.add(hl);
  });
  var tlMat=new THREE.MeshBasicMaterial({color:0xff2200});
  [[1.3,-3.7],[-1.3,-3.7]].forEach(function(lp){
    var tl=new THREE.Mesh(new THREE.BoxGeometry(0.55,0.24,0.1),tlMat);
    tl.position.set(lp[0],1.05,lp[1]); group.add(tl);
  });
  var grille=new THREE.Mesh(new THREE.BoxGeometry(2.5,0.4,0.15),new THREE.MeshStandardMaterial({color:0x222222,roughness:0.5,metalness:0.6}));
  grille.position.set(0,0.85,3.79); group.add(grille);
  group.position.set(sx||0, 0.75, sz||0);
  scene.add(group);
  var carAxis=Math.abs(Math.sin(yaw))>Math.abs(Math.cos(yaw))?'x':'z';
  var carDir=carAxis==='x'?(Math.sin(yaw)>0?1:-1):(Math.cos(yaw)>0?1:-1);
  var carObj={group:group,type:'car',speed:0.35+Math.random()*0.35,dir:carDir,axis:carAxis,limit:600,onRoadOnly:true};
  movingObjects.push(carObj);
  placedObjects.push({meshes:[group],type:'car',cost:BUILD_COSTS.car,x:sx||0,z:sz||0,movingRef:carObj});
}

function spawnBike(sx,sz){
  var cols=[0xcc2200,0x111111,0x1a3888,0xeeee00,0xff6600,0xffffff];
  var col=cols[Math.floor(Math.random()*cols.length)];
  var group=new THREE.Group();
  var bMat=new THREE.MeshStandardMaterial({color:col,roughness:0.1,metalness:0.95});
  var cMat=new THREE.MeshStandardMaterial({color:0x1a1a1a,roughness:0.4,metalness:0.7});
  var chromeMat=new THREE.MeshStandardMaterial({color:0xdddddd,roughness:0.05,metalness:1.0});
  var wMat=new THREE.MeshStandardMaterial({color:0x0d0d0d,roughness:0.95});
  var rimMat2=new THREE.MeshStandardMaterial({color:0xcccccc,roughness:0.05,metalness:1.0});
  var frame=new THREE.Mesh(new THREE.BoxGeometry(0.42,0.6,3.2),cMat);
  frame.position.set(0,1.1,0); group.add(frame);
  var tank=new THREE.Mesh(new THREE.BoxGeometry(0.52,0.42,1.3),bMat);
  tank.position.set(0,1.65,0.3); group.add(tank);
  var cowl=new THREE.Mesh(new THREE.BoxGeometry(0.48,0.65,0.7),bMat);
  cowl.position.set(0,1.42,1.85); group.add(cowl);
  var hl=new THREE.Mesh(new THREE.CylinderGeometry(0.18,0.18,0.1,12),new THREE.MeshBasicMaterial({color:0xffffcc}));
  hl.rotation.x=Math.PI/2; hl.position.set(0,1.45,2.22); group.add(hl);
  var seat=new THREE.Mesh(new THREE.BoxGeometry(0.44,0.14,1.1),cMat);
  seat.position.set(0,1.72,-0.6); group.add(seat);
  var handle=new THREE.Mesh(new THREE.BoxGeometry(1.15,0.08,0.08),chromeMat);
  handle.position.set(0,1.75,1.55); group.add(handle);
  var engine=new THREE.Mesh(new THREE.BoxGeometry(0.48,0.55,0.85),cMat);
  engine.position.set(0,0.82,0.15); group.add(engine);
  var muffler=new THREE.Mesh(new THREE.CylinderGeometry(0.08,0.11,1.55,10),chromeMat);
  muffler.rotation.z=Math.PI/2; muffler.position.set(0.38,0.7,-0.9); group.add(muffler);
  [[0,1.65],[0,-1.65]].forEach(function(wp){
    var wz2=wp[1];
    var tire=new THREE.Mesh(new THREE.CylinderGeometry(0.62,0.62,0.22,20),wMat);
    tire.rotation.z=Math.PI/2; tire.position.set(0,0.65,wz2); group.add(tire);
    var rim2=new THREE.Mesh(new THREE.CylinderGeometry(0.32,0.32,0.26,12),rimMat2);
    rim2.rotation.z=Math.PI/2; rim2.position.set(0,0.65,wz2); group.add(rim2);
    for(var sp=0;sp<8;sp++){
      var a=sp/8*Math.PI;
      var spoke2=new THREE.Mesh(new THREE.BoxGeometry(0.58,0.04,0.04),rimMat2);
      spoke2.rotation.z=a; spoke2.position.set(0,0.65,wz2); group.add(spoke2);
    }
  });
  group.position.set(sx||0, 0.65, sz||0);
  scene.add(group);
  var bikeAxis=Math.abs(Math.sin(yaw))>Math.abs(Math.cos(yaw))?'x':'z';
  var bikeDir=bikeAxis==='x'?(Math.sin(yaw)>0?1:-1):(Math.cos(yaw)>0?1:-1);
  var bikeObj={group:group,type:'bike',speed:0.55+Math.random()*0.5,dir:bikeDir,axis:bikeAxis,limit:600,onRoadOnly:true};
  movingObjects.push(bikeObj);
  placedObjects.push({meshes:[group],type:'bike',cost:BUILD_COSTS.bike,x:sx||0,z:sz||0,movingRef:bikeObj});
}

function buildRoadCurve(x,z,dir){
  var bm=[];
  var mat=new THREE.MeshStandardMaterial({color:0x555555,roughness:0.9,metalness:0.1});
  var angles=dir==='right'?[0,Math.PI/6,Math.PI/3]:[0,-Math.PI/6,-Math.PI/3];
  angles.forEach(function(a){
    var ox=Math.sin(a)*TILE*1.2, oz=Math.cos(a)*TILE*1.2;
    var rm=new THREE.Mesh(new THREE.PlaneGeometry(TILE*0.8,TILE*0.8),mat);
    rm.rotation.x=-Math.PI/2; rm.rotation.z=-a;
    rm.position.set(x+ox*0.5,0.15,z+oz*0.5-TILE*0.5);
    scene.add(rm); bm.push(rm);
    var rk=Math.round((x+ox*0.5)/TILE)*TILE+'_'+Math.round((z+oz*0.5-TILE*0.5)/TILE)*TILE;
    roadTiles.add(rk);
  });
  placedObjects.push({meshes:bm,type:'roadcurve',cost:BUILD_COSTS.roadcurveR,x:x,z:z});
}

function buildStreetlight(x,z){
  var bm=[];
  var poleMat=new THREE.MeshStandardMaterial({color:0x888888,roughness:0.3,metalness:0.8});
  var pole=new THREE.Mesh(new THREE.CylinderGeometry(0.15,0.2,10,8),poleMat);
  pole.position.set(x,5,z); scene.add(pole); bm.push(pole);
  var arm=new THREE.Mesh(new THREE.CylinderGeometry(0.1,0.1,3,6),poleMat);
  arm.rotation.z=Math.PI/2; arm.position.set(x+1.5,9.8,z); scene.add(arm); bm.push(arm);
  var lampMat=new THREE.MeshStandardMaterial({color:0xddddcc,roughness:0.3,metalness:0.5});
  var lamp=new THREE.Mesh(new THREE.CylinderGeometry(0.5,0.3,0.6,8),lampMat);
  lamp.position.set(x+3,9.5,z); scene.add(lamp); bm.push(lamp);
  var light=new THREE.PointLight(0xffffcc,2.0,40);
  light.position.set(x+3,9.2,z); scene.add(light); bm.push(light);
  var base=new THREE.Mesh(new THREE.CylinderGeometry(0.4,0.5,0.5,8),poleMat);
  base.position.set(x,0.25,z); scene.add(base); bm.push(base);
  placedObjects.push({meshes:bm,type:'streetlight',cost:BUILD_COSTS.streetlight,x:x,z:z});
}

function buildBridge(x,z){
  var meshes=[];
  var len=TILE*3, w2=TILE*0.8;
  var isV=Math.random()>0.5;
  var roadMat=new THREE.MeshStandardMaterial({color:0x888888,roughness:0.7,metalness:0.3});
  var road=new THREE.Mesh(new THREE.BoxGeometry(isV?w2:len, 0.5, isV?len:w2),roadMat);
  road.position.set(x,4,z); scene.add(road); meshes.push(road);
  var railMat=new THREE.MeshStandardMaterial({color:0x999999,roughness:0.3,metalness:0.7});
  var sides=isV?[[-w2/2,0],[w2/2,0]]:[[0,-w2/2],[0,w2/2]];
  sides.forEach(function(s2){
    var rail=new THREE.Mesh(new THREE.BoxGeometry(isV?0.3:len, 1.2, isV?len:0.3),railMat);
    rail.position.set(x+s2[0],4.85,z+s2[1]); scene.add(rail); meshes.push(rail);
    var pillarCount=Math.floor(len/TILE)+1;
    for(var i=0;i<=pillarCount;i++){
      var pillar=new THREE.Mesh(new THREE.BoxGeometry(0.4,5,0.4),railMat);
      var poff=(i/pillarCount-0.5)*len;
      pillar.position.set(x+s2[0]+(isV?0:poff),2,z+s2[1]+(isV?poff:0));
      scene.add(pillar); meshes.push(pillar);
    }
  });
  var archMat=new THREE.MeshStandardMaterial({color:0x778899,roughness:0.4,metalness:0.6});
  var arch=new THREE.Mesh(new THREE.CylinderGeometry(len*0.55,len*0.55,0.5,16,1,false,0,Math.PI),archMat);
  arch.rotation.z=isV?Math.PI/2:0; arch.position.set(x,8,z); scene.add(arch); meshes.push(arch);
  return meshes;
}

function buildFootbridge(x,z){
  var meshes=[];
  var h=6, len=TILE*2;
  var isV=Math.random()>0.5;
  var matGray=new THREE.MeshStandardMaterial({color:0xaaaaaa,roughness:0.5,metalness:0.5});
  var path=new THREE.Mesh(new THREE.BoxGeometry(isV?2.5:len,0.3,isV?len:2.5),matGray);
  path.position.set(x,h,z); scene.add(path); meshes.push(path);
  [[isV?-1.2:0,isV?0:-1.2],[isV?1.2:0,isV?0:1.2]].forEach(function(s2){
    var handrail=new THREE.Mesh(new THREE.BoxGeometry(isV?0.1:len,1,isV?len:0.1),matGray);
    handrail.position.set(x+s2[0],h+0.65,z+s2[1]); scene.add(handrail); meshes.push(handrail);
  });
  var stairMat=new THREE.MeshStandardMaterial({color:0x999999,roughness:0.7});
  [-1,1].forEach(function(side){
    for(var i=0;i<5;i++){
      var step=new THREE.Mesh(new THREE.BoxGeometry(isV?2.5:2,0.3,isV?2:2.5),stairMat);
      var soff=side*(len/2-i*0.8);
      step.position.set(x+(isV?0:soff),h-i*0.9,z+(isV?soff:0));
      scene.add(step); meshes.push(step);
    }
  });
  return meshes;
}

function buildTree(x,z){
  var treeType=Math.floor(Math.random()*3);
  var trunkH=3+Math.random()*3;
  var trunkR=0.22+Math.random()*0.12;
  var trunkMat=new THREE.MeshStandardMaterial({color:0x5a3a1a,roughness:0.95});
  var trunk=new THREE.Mesh(new THREE.CylinderGeometry(trunkR*0.7,trunkR,trunkH,8),trunkMat);
  trunk.position.set(x,trunkH/2,z); trunk.castShadow=true; scene.add(trunk);
  var i,a,r2,sphere,layerR,layerH,yPos,cone;
  if(treeType===1){
    var coniferH=12+Math.random()*6;
    var coniferR=2.8+Math.random()*0.8;
    var darkGreens=[0x0d4a0d,0x0e5a0e,0x155515,0x0a3d0a,0x1a6a1a];
    for(i=0;i<5;i++){
      layerR=coniferR*(1-i/5*0.65)*(1+Math.random()*0.1);
      layerH=coniferH*(0.28-i*0.02);
      yPos=trunkH+i*(coniferH*0.16);
      cone=new THREE.Mesh(new THREE.ConeGeometry(layerR,layerH,10),
        new THREE.MeshStandardMaterial({color:darkGreens[i%5],roughness:0.85}));
      cone.position.set(x,yPos,z); cone.castShadow=true; scene.add(cone);
    }
  } else if(treeType===2){
    var leafR2=2.5+Math.random()*1.0;
    var pinkCols=[0xffb7c5,0xff99aa,0xffaacc,0xff88bb];
    for(i=0;i<6;i++){
      a=i/6*Math.PI*2;
      sphere=new THREE.Mesh(new THREE.SphereGeometry(leafR2*0.55+Math.random()*0.4,8,6),
        new THREE.MeshStandardMaterial({color:pinkCols[i%4],roughness:0.9,transparent:true,opacity:0.95}));
      sphere.position.set(x+Math.cos(a)*leafR2*0.5,trunkH+leafR2*0.6+Math.random()*0.8,z+Math.sin(a)*leafR2*0.5);
      sphere.castShadow=true; scene.add(sphere);
    }
  } else {
    var leafR3=2.2+Math.random()*1.2;
    var greens=[0x2d6a2d,0x3a7a2a,0x4a8a35,0x2a5a1a,0x228822];
    var lm=new THREE.MeshStandardMaterial({color:greens[Math.floor(Math.random()*5)],roughness:0.85});
    for(i=0;i<7;i++){
      a=i/7*Math.PI*2;
      sphere=new THREE.Mesh(new THREE.SphereGeometry(leafR3*0.5+Math.random()*0.4,8,5),lm);
      sphere.position.set(x+Math.cos(a)*leafR3*0.55,trunkH+leafR3*0.5+Math.random()*0.7,z+Math.sin(a)*leafR3*0.55);
      sphere.castShadow=true; scene.add(sphere);
    }
    var main2=new THREE.Mesh(new THREE.SphereGeometry(leafR3*0.75,9,7),lm);
    main2.position.set(x,trunkH+leafR3*0.75,z); main2.castShadow=true; scene.add(main2);
  }
}

function buildGrass(x,z){
  var dirtMat=new THREE.MeshStandardMaterial({color:0x6a7a3a,roughness:0.98});
  var base=new THREE.Mesh(new THREE.CircleGeometry(TILE*0.48,12),dirtMat);
  base.rotation.x=-Math.PI/2; base.position.set(x,0.02,z); scene.add(base);
  var greenCols=[0x2d7a1a,0x3a8a22,0x4a9a28,0x228a18,0x1e6e14];
  var weedCols=[0x5aaa28,0x3a9a1a,0x6aba30,0x88cc40];
  var i,a,r,h,blade,weed,tip;
  for(i=0;i<18;i++){
    a=Math.random()*Math.PI*2; r=Math.random()*TILE*0.42; h=0.5+Math.random()*0.9;
    blade=new THREE.Mesh(new THREE.ConeGeometry(0.06+Math.random()*0.05,h,4),
      new THREE.MeshStandardMaterial({color:greenCols[i%greenCols.length],roughness:0.9}));
    blade.position.set(x+Math.cos(a)*r,h/2,z+Math.sin(a)*r);
    blade.rotation.y=Math.random()*Math.PI; scene.add(blade);
  }
  for(i=0;i<10;i++){
    a=Math.random()*Math.PI*2; r=Math.random()*TILE*0.44; h=1.2+Math.random()*1.6;
    weed=new THREE.Mesh(new THREE.CylinderGeometry(0.015,0.04,h,4),
      new THREE.MeshStandardMaterial({color:weedCols[i%weedCols.length],roughness:0.8}));
    weed.position.set(x+Math.cos(a)*r,h/2,z+Math.sin(a)*r);
    weed.rotation.z=(Math.random()-0.5)*0.3; scene.add(weed);
    tip=new THREE.Mesh(new THREE.ConeGeometry(0.05,0.22,4),
      new THREE.MeshStandardMaterial({color:0xaacc66,roughness:0.8}));
    tip.position.set(x+Math.cos(a)*r,h+0.11,z+Math.sin(a)*r); scene.add(tip);
  }
}

function buildRiver(x,z){
  var isV=Math.random()>0.5;
  var rW=TILE*0.9, rL=TILE*2;
  var bedMat=new THREE.MeshStandardMaterial({color:0xb0a080,roughness:0.98});
  var bed=new THREE.Mesh(new THREE.PlaneGeometry(isV?rW*1.3:rL,isV?rL:rW*1.3),bedMat);
  bed.rotation.x=-Math.PI/2; bed.position.set(x,0.08,z); scene.add(bed);
  var rMat=new THREE.MeshStandardMaterial({color:0x2299dd,roughness:0.02,metalness:0.4,transparent:true,opacity:0.82});
  var river=new THREE.Mesh(new THREE.PlaneGeometry(isV?rW:rL,isV?rL:rW),rMat);
  river.rotation.x=-Math.PI/2; river.position.set(x,0.18,z);
  river.userData={isRiver:true,isV:isV,rx:x,rz:z}; scene.add(river); riverMeshes.push(river);
  var streamMat=new THREE.MeshBasicMaterial({color:0xaaddff,transparent:true,opacity:0.35});
  var numStreams=4;
  for(var i=0;i<numStreams;i++){
    var offset=(i/(numStreams-1)-0.5)*(isV?rW*0.6:rL*0.6);
    var streamW=isV?rW*0.06:rL*0.06;
    var streamL=isV?rL*0.85:rW*0.85;
    var stream=new THREE.Mesh(new THREE.PlaneGeometry(isV?streamW:streamL, isV?streamL:streamW),streamMat);
    stream.rotation.x=-Math.PI/2;
    stream.position.set(isV?x+offset:x,0.19,isV?z:z+offset);
    stream.userData={isStream:true,isV:isV,rx:x,rz:z,streamIndex:i,phase:i/numStreams*Math.PI*2};
    scene.add(stream); riverMeshes.push(stream);
  }
  var stoneMat=new THREE.MeshStandardMaterial({color:0x888878,roughness:0.95});
  var edgeOffset=isV?rW*0.55:rL*0.1;
  for(var si=0;si<8;si++){
    var sa=Math.random();
    var stone=new THREE.Mesh(new THREE.SphereGeometry(0.12+Math.random()*0.18,6,4),stoneMat);
    var stx=isV?x+(Math.random()>0.5?1:-1)*edgeOffset:x+(sa-0.5)*rL*0.9;
    var stz=isV?z+(sa-0.5)*rL*0.9:z+(Math.random()>0.5?1:-1)*edgeOffset;
    stone.position.set(stx,0.1,stz); scene.add(stone);
  }
}

function buildLake(x,z){
  var lakeR=TILE*2.0;
  var shoreMat=new THREE.MeshStandardMaterial({color:0xd4c49a,roughness:0.95});
  var shore=new THREE.Mesh(new THREE.CircleGeometry(lakeR+2.5,32),shoreMat);
  shore.rotation.x=-Math.PI/2; shore.position.set(x,0.04,z); scene.add(shore);
  var lakeMat=new THREE.MeshStandardMaterial({color:0x00bbee,roughness:0.01,metalness:0.5,transparent:true,opacity:0.88});
  var lake=new THREE.Mesh(new THREE.CircleGeometry(lakeR,32),lakeMat);
  lake.rotation.x=-Math.PI/2; lake.position.set(x,0.2,z); scene.add(lake);
  var deepMat=new THREE.MeshStandardMaterial({color:0x0099cc,roughness:0.01,metalness:0.4,transparent:true,opacity:0.55});
  var deep=new THREE.Mesh(new THREE.CircleGeometry(lakeR*0.6,24),deepMat);
  deep.rotation.x=-Math.PI/2; deep.position.set(x,0.22,z); scene.add(deep);
  var wl=new THREE.PointLight(0x00ccff,1.5,lakeR*3);
  wl.position.set(x,1,z); scene.add(wl);
  var fishCount=3+Math.floor(Math.random()*2);
  var fishCols=[0xff8844,0xffcc44,0xff4488,0x44aaff,0xff6622];
  for(var fi=0;fi<fishCount;fi++){
    var fc=fishCols[fi%fishCols.length];
    var fMat=new THREE.MeshStandardMaterial({color:fc,roughness:0.4});
    var body=new THREE.Mesh(new THREE.SphereGeometry(0.55,8,6),fMat);
    body.scale.set(1,0.5,1.8);
    var tail=new THREE.Mesh(new THREE.ConeGeometry(0.4,0.6,5),fMat);
    tail.rotation.x=Math.PI/2; tail.position.z=-0.95;
    var fa=fi/fishCount*Math.PI*2;
    var fr=lakeR*(0.3+Math.random()*0.35);
    var fGroup=new THREE.Group();
    fGroup.add(body); fGroup.add(tail);
    fGroup.position.set(x+Math.cos(fa)*fr, 0.35, z+Math.sin(fa)*fr);
    scene.add(fGroup);
    fishMeshes.push({group:fGroup,lx:x,lz:z,lakeR:lakeR,angle:fa,radius:fr,
      speed:(0.4+Math.random()*0.4)*(Math.random()>0.5?1:-1),phase:Math.random()*Math.PI*2,depth:0.3+Math.random()*0.3});
  }
  var stoneMat=new THREE.MeshStandardMaterial({color:0x998888,roughness:0.9});
  for(var si=0;si<8;si++){
    var sa=si/8*Math.PI*2;
    var s2=new THREE.Mesh(new THREE.SphereGeometry(0.2+Math.random()*0.2,6,4),stoneMat);
    s2.position.set(x+Math.cos(sa)*(lakeR+1+Math.random()),0.1,z+Math.sin(sa)*(lakeR+1+Math.random()));
    scene.add(s2);
  }
}

function buildFountain(x,z){
  var FS=TILE*2;
  var baseMat=new THREE.MeshStandardMaterial({color:0x999999,roughness:0.3,metalness:0.7});
  var plazaMat=new THREE.MeshStandardMaterial({color:0xc8c0b0,roughness:0.7});
  var plaza=new THREE.Mesh(new THREE.CircleGeometry(FS*0.6,24),plazaMat);
  plaza.rotation.x=-Math.PI/2;plaza.position.set(x,0.1,z);scene.add(plaza);
  var base=new THREE.Mesh(new THREE.CylinderGeometry(FS*0.38,FS*0.4,1.5,20),baseMat);
  base.position.set(x,0.75,z);scene.add(base);
  var basin=new THREE.Mesh(new THREE.CylinderGeometry(FS*0.35,FS*0.3,0.8,20),
    new THREE.MeshStandardMaterial({color:0x88ccff,roughness:0.02,metalness:0.5,transparent:true,opacity:0.8}));
  basin.position.set(x,1.5,z);scene.add(basin);
  var pillar=new THREE.Mesh(new THREE.CylinderGeometry(0.3,0.4,4,10),baseMat);
  pillar.position.set(x,3,z);scene.add(pillar);
  var fl=new THREE.PointLight(0x44aaff,2.5,FS*3);fl.position.set(x,5,z);scene.add(fl);
  var dropMat=new THREE.MeshBasicMaterial({color:0x88ddff,transparent:true,opacity:0.75});
  for(var fi=0;fi<24;fi++){
    var a=fi/24*Math.PI*2;
    var drop=new THREE.Mesh(new THREE.SphereGeometry(0.12,4,4),dropMat);
    drop.position.set(x+Math.cos(a)*0.3,7.5,z+Math.sin(a)*0.3);
    drop.userData={isFountainDrop:true,fx:x,fz:z,angle:a,radius:0.4+Math.random()*1.2,
      speed:0.6+Math.random()*0.8,phase:Math.random()*Math.PI*2,height:3+Math.random()*3};
    scene.add(drop);riverMeshes.push(drop);
  }
}

function addGrowableMountain(x,z){
  var key=growKey(x,z,'mountain');
  if(!growRegistry[key]) growRegistry[key]={level:1,meshes:[]};
  var d=growRegistry[key];
  removeMeshGroup(d.meshes); d.meshes=[];
  var lv=d.level;
  var h=(6+lv*9)+(Math.random()*3);
  var r=(5+lv*5)+(Math.random()*2);
  var rockCols=[0x7a6a5a,0x8a7a68,0x6a5a4a];
  var rockMat=new THREE.MeshStandardMaterial({color:rockCols[Math.floor(Math.random()*rockCols.length)],roughness:0.92,flatShading:false});
  var snowMat=new THREE.MeshStandardMaterial({color:0xf2f2f0,roughness:0.6,flatShading:false});
  var grassMat2=new THREE.MeshStandardMaterial({color:0x4a7a3a,roughness:0.95,flatShading:false});
  var baseGeo=new THREE.ConeGeometry(r*1.18,h*0.5,32,4);
  var bPos=baseGeo.attributes.position;
  for(var i=0;i<bPos.count;i++){
    if(bPos.getY(i)<h*0.48){
      var noise=(Math.random()-0.5)*r*0.18;
      bPos.setX(i,bPos.getX(i)+noise);
      bPos.setZ(i,bPos.getZ(i)+noise);
    }
  }
  baseGeo.computeVertexNormals();
  var base=new THREE.Mesh(baseGeo,grassMat2);
  base.position.set(x,h*0.25,z); base.castShadow=true; scene.add(base); d.meshes.push(base);
  var midGeo=new THREE.ConeGeometry(r*0.72,h*0.55,28,3);
  var mPos=midGeo.attributes.position;
  for(var i=0;i<mPos.count;i++){
    if(mPos.getY(i)<h*0.5){
      var n2=(Math.random()-0.5)*r*0.12;
      mPos.setX(i,mPos.getX(i)+n2);
      mPos.setZ(i,mPos.getZ(i)+n2);
    }
  }
  midGeo.computeVertexNormals();
  var mid=new THREE.Mesh(midGeo,rockMat);
  mid.position.set(x,h*0.57,z); mid.castShadow=true; scene.add(mid); d.meshes.push(mid);
  var snowGeo=new THREE.ConeGeometry(r*0.28,h*0.28,20,2);
  snowGeo.computeVertexNormals();
  var snow=new THREE.Mesh(snowGeo,snowMat);
  snow.position.set(x,h*0.9,z); snow.castShadow=true; scene.add(snow); d.meshes.push(snow);
  if(lv>=2){
    for(var i=0;i<3;i++){
      var sa=i/3*Math.PI*2+(Math.random()*0.5);
      var sr=r*0.25;
      var snowPatch=new THREE.Mesh(new THREE.SphereGeometry(r*0.12+Math.random()*r*0.08,10,8),snowMat);
      snowPatch.position.set(x+Math.cos(sa)*sr,h*0.72,z+Math.sin(sa)*sr);
      scene.add(snowPatch); d.meshes.push(snowPatch);
    }
  }
}

function addGrowableHouse(x,z){
  var key=growKey(x,z,'house');
  if(!growRegistry[key]) growRegistry[key]={level:1,meshes:[]};
  var d=growRegistry[key];
  removeMeshGroup(d.meshes); d.meshes=[];
  var lv=d.level;
  var s=TILE*0.82;
  var flH=TILE*0.85;
  var h=flH*lv;
  var wallCols=[0xe8d8c0,0xd4c4b0,0xc8bca8,0xe0d0c0,0xd8ccbc];
  var wallMat=new THREE.MeshStandardMaterial({color:wallCols[Math.floor(Math.random()*wallCols.length)],roughness:0.85});
  var body=new THREE.Mesh(new THREE.BoxGeometry(s,h,s),wallMat);
  body.castShadow=true;body.position.set(x,h/2,z);scene.add(body);d.meshes.push(body);
  var roofCols=[0x8B4513,0x6B3410,0xcc4422,0x444444];
  var roofMat=new THREE.MeshStandardMaterial({color:roofCols[Math.floor(Math.random()*roofCols.length)],roughness:0.8});
  var roof=new THREE.Mesh(new THREE.ConeGeometry(s*0.78,s*0.55,4),roofMat);
  roof.rotation.y=Math.PI/4;roof.position.set(x,h+s*0.27,z);
  roof.castShadow=true;scene.add(roof);d.meshes.push(roof);
  var doorMat=new THREE.MeshStandardMaterial({color:0x8B5A2B,roughness:0.7});
  var door=new THREE.Mesh(new THREE.BoxGeometry(s*0.2,flH*0.55,0.1),doorMat);
  door.position.set(x,flH*0.27,z+s*0.42);scene.add(door);d.meshes.push(door);
  var winMat=new THREE.MeshBasicMaterial({color:0x88ccff,transparent:true,opacity:0.7});
  [[-s*0.25],[s*0.25]].forEach(function(wx){
    var w=new THREE.Mesh(new THREE.PlaneGeometry(s*0.18,s*0.18),winMat);
    w.position.set(x+wx[0],flH*0.55,z+s*0.422);scene.add(w);d.meshes.push(w);
    var wb=new THREE.Mesh(new THREE.PlaneGeometry(s*0.18,s*0.18),winMat);
    wb.rotation.y=Math.PI;wb.position.set(x+wx[0],flH*0.55,z-s*0.422);scene.add(wb);d.meshes.push(wb);
  });
}

function addGrowableBuilding(x,z,w,d2,baseH){
  var key=growKey(x,z,'building');
  if(!growRegistry[key]) growRegistry[key]={level:1,meshes:[]};
  var d=growRegistry[key];
  removeMeshGroup(d.meshes); d.meshes=[];
  var floorH=TILE;
  var h=floorH*d.level;
  var meshes=[];
  var pal=buildingPalettes[Math.floor(Math.random()*buildingPalettes.length)];
  var bMat=new THREE.MeshStandardMaterial({color:pal.base,roughness:0.35,metalness:0.65});
  var glassMat2=new THREE.MeshStandardMaterial({color:pal.glass,roughness:0.05,metalness:0.8,transparent:true,opacity:0.65});
  var body=new THREE.Mesh(new THREE.BoxGeometry(TILE*0.85,h,TILE*0.85),bMat);
  body.castShadow=true;body.receiveShadow=true;
  body.position.set(x,h/2,z);scene.add(body);meshes.push(body);
  var gw=new THREE.Mesh(new THREE.BoxGeometry(TILE*0.82,h*0.95,0.3),glassMat2);
  gw.position.set(x,h/2,z+TILE*0.43);scene.add(gw);meshes.push(gw);
  var gb=new THREE.Mesh(new THREE.BoxGeometry(TILE*0.82,h*0.95,0.3),glassMat2);
  gb.position.set(x,h/2,z-TILE*0.43);scene.add(gb);meshes.push(gb);
  var hw=TILE*0.425;
  var winW=TILE*0.18, winH=floorH*0.45;
  var offsets=[-TILE*0.18, TILE*0.18];
  var wm;
  for(var f=0;f<d.level;f++){
    var fy=f*floorH+floorH*0.5;
    [hw,-hw].forEach(function(fz,fi){
      offsets.forEach(function(ox){
        wm=new THREE.Mesh(new THREE.PlaneGeometry(winW,winH),
          new THREE.MeshBasicMaterial({color:0x99ddff,transparent:true,opacity:0.0}));
        if(fi===1) wm.rotation.y=Math.PI;
        wm.position.set(x+ox,fy,z+fz);
        scene.add(wm);meshes.push(wm);
        windowMeshes.push({mesh:wm,rand:Math.random()});
      });
    });
    [hw,-hw].forEach(function(fx,fi){
      offsets.forEach(function(oz){
        wm=new THREE.Mesh(new THREE.PlaneGeometry(winW,winH),
          new THREE.MeshBasicMaterial({color:0x99ddff,transparent:true,opacity:0.0}));
        wm.rotation.y=fi===0?Math.PI/2:-Math.PI/2;
        wm.position.set(x+fx,fy,z+oz);
        scene.add(wm);meshes.push(wm);
        windowMeshes.push({mesh:wm,rand:Math.random()});
      });
    });
  }
  d.meshes=meshes;
}

function addGrowableSchool(x,z){
  var key=growKey(x,z,'school');
  if(!growRegistry[key]) growRegistry[key]={level:1,meshes:[],maxLevel:5};
  var d=growRegistry[key];
  removeMeshGroup(d.meshes); d.meshes=[];
  var lv=d.level;
  var sw=TILE*1.8;
  var floorH=TILE*0.9;
  var h=floorH*lv;
  var meshes=[];
  var wMat=new THREE.MeshStandardMaterial({color:0xd4c8b0,roughness:0.8});
  var body=new THREE.Mesh(new THREE.BoxGeometry(sw,h,sw*0.6),wMat);
  body.castShadow=true;body.position.set(x,h/2,z);scene.add(body);meshes.push(body);
  var roofMat=new THREE.MeshStandardMaterial({color:0x7a8a6a,roughness:0.7});
  var roof=new THREE.Mesh(new THREE.BoxGeometry(sw+0.5,0.5,sw*0.6+0.5),roofMat);
  roof.position.set(x,h+0.25,z);scene.add(roof);meshes.push(roof);
  var winMat2=new THREE.MeshBasicMaterial({color:0x99ccff,transparent:true,opacity:0.0});
  for(var f=0;f<lv;f++){
    for(var wi=-2;wi<=2;wi++){
      if(wi===0) continue;
      var w2=new THREE.Mesh(new THREE.PlaneGeometry(sw*0.12,floorH*0.5),winMat2);
      w2.position.set(x+wi*(sw/5),f*floorH+floorH*0.55,z+sw*0.305);
      scene.add(w2);meshes.push(w2);
      windowMeshes.push({mesh:w2,rand:Math.random()});
    }
  }
  for(var f=0;f<lv;f++){
    for(var wi=-2;wi<=2;wi++){
      if(Math.abs(wi)<1) continue;
      var wb=new THREE.Mesh(new THREE.PlaneGeometry(sw*0.1,floorH*0.45),winMat2);
      wb.rotation.y=Math.PI;
      wb.position.set(x+wi*(sw/5),f*floorH+floorH*0.55,z-sw*0.305);
      scene.add(wb);meshes.push(wb);
      windowMeshes.push({mesh:wb,rand:Math.random()});
    }
  }
  var clockFace=new THREE.Mesh(new THREE.CircleGeometry(sw*0.065,16),new THREE.MeshBasicMaterial({color:0xffffff}));
  clockFace.position.set(x,h*0.82,z+sw*0.31);scene.add(clockFace);meshes.push(clockFace);
  if(lv>=2){
    var ent=new THREE.Mesh(new THREE.BoxGeometry(sw*0.22,h*0.65,sw*0.1),
      new THREE.MeshStandardMaterial({color:0x8a9a7a,roughness:0.6}));
    ent.position.set(x,h*0.32,z+sw*0.33);scene.add(ent);meshes.push(ent);
  }
  d.meshes=meshes;
}

function buildGym(x,z){
  var s=TILE*1.5;
  var mat=new THREE.MeshStandardMaterial({color:0xd8d0c8,roughness:0.85});
  var roofMat=new THREE.MeshStandardMaterial({color:0x446688,roughness:0.6,metalness:0.4});
  var wallH=s*0.45;
  var archR=s*0.42;
  var body=new THREE.Mesh(new THREE.BoxGeometry(archR*2,wallH,s),mat);
  body.position.set(x,wallH/2,z);scene.add(body);
  [1,-1].forEach(function(side){
    for(var i=0;i<8;i++){
      var a=i/8*Math.PI;
      var y=wallH+Math.sin(a)*archR;
      var hw2=Math.cos(a)*archR;
      var panel=new THREE.Mesh(new THREE.BoxGeometry(hw2*2+0.1,0.5,0.2),mat);
      panel.position.set(x,y,z+side*s*0.5);scene.add(panel);
    }
  });
  var arch=new THREE.Mesh(new THREE.CylinderGeometry(archR,archR,s,24,1,false,0,Math.PI),roofMat);
  arch.rotation.z=Math.PI/2;arch.position.set(x,wallH,z);scene.add(arch);
}

function buildSchoolyard(x,z){
  var sy=TILE*2;
  var groundMat=new THREE.MeshStandardMaterial({color:0xd4c89a,roughness:0.95});
  var ground=new THREE.Mesh(new THREE.PlaneGeometry(sy,sy),groundMat);
  ground.rotation.x=-Math.PI/2;ground.position.set(x,0.12,z);scene.add(ground);
  var lineMat=new THREE.MeshBasicMaterial({color:0xffffff,transparent:true,opacity:0.5});
  var line=new THREE.Mesh(new THREE.PlaneGeometry(sy,0.3),lineMat);
  line.rotation.x=-Math.PI/2;line.position.set(x,0.13,z);scene.add(line);
}

function buildTennisCourt(x,z){
  var cMat=new THREE.MeshStandardMaterial({color:0x2266aa,roughness:0.7});
  var court=new THREE.Mesh(new THREE.PlaneGeometry(TILE*1.2,TILE*2),cMat);
  court.rotation.x=-Math.PI/2;court.position.set(x,0.13,z);scene.add(court);
  var lMat=new THREE.MeshBasicMaterial({color:0xffffff,transparent:true,opacity:0.8});
  [[0,0,TILE*1.2,0.25],[TILE*0.55,0,0.25,TILE*2],[-TILE*0.55,0,0.25,TILE*2],
   [0,TILE*0.5,TILE*1.2,0.25],[0,-TILE*0.5,TILE*1.2,0.25]].forEach(function(_a){
    var lx=_a[0],lz=_a[1],lw=_a[2],ll=_a[3];
    var l=new THREE.Mesh(new THREE.PlaneGeometry(lw,ll),lMat);
    l.rotation.x=-Math.PI/2;l.position.set(x+lx,0.14,z+lz);scene.add(l);
  });
  var netMat=new THREE.MeshStandardMaterial({color:0xdddddd,roughness:0.5,transparent:true,opacity:0.6});
  var net=new THREE.Mesh(new THREE.BoxGeometry(TILE*1.25,0.9,0.1),netMat);
  net.position.set(x,0.5,z);scene.add(net);
}

function buildBaseballField(x,z){
  var f=TILE*2.5;
  var grassMat=new THREE.MeshStandardMaterial({color:0x3a8a3a,roughness:0.9});
  var dirtMat=new THREE.MeshStandardMaterial({color:0xc4a87a,roughness:0.95});
  var field=new THREE.Mesh(new THREE.CircleGeometry(f*0.48,24),grassMat);
  field.rotation.x=-Math.PI/2;field.position.set(x,0.12,z);scene.add(field);
  var infield=new THREE.Mesh(new THREE.CircleGeometry(f*0.22,16),dirtMat);
  infield.rotation.x=-Math.PI/2;infield.position.set(x,0.13,z);scene.add(infield);
}

function spawnPerson(startX,startZ){
  var group=new THREE.Group();
  var skinMat=new THREE.MeshStandardMaterial({color:0xffcc99,roughness:0.8});
  var bodyMat=new THREE.MeshStandardMaterial({
    color:[0x334488,0x883344,0x448833,0x333333,0x884422][Math.floor(Math.random()*5)],roughness:0.8});
  var legMat=new THREE.MeshStandardMaterial({color:0x222244,roughness:0.8});
  var head=new THREE.Mesh(new THREE.SphereGeometry(0.35,8,6),skinMat);
  head.position.set(0,2.35,0);group.add(head);
  var torso=new THREE.Mesh(new THREE.BoxGeometry(0.55,0.9,0.32),bodyMat);
  torso.position.set(0,1.6,0);group.add(torso);
  [[-0.42,0],[0.42,0]].forEach(function(ap){
    var arm=new THREE.Mesh(new THREE.BoxGeometry(0.2,0.7,0.2),bodyMat);
    arm.position.set(ap[0],1.55,0);group.add(arm);
  });
  var lLeg=new THREE.Mesh(new THREE.BoxGeometry(0.22,0.75,0.22),legMat);
  lLeg.position.set(-0.18,0.7,0);group.add(lLeg);
  var rLeg=new THREE.Mesh(new THREE.BoxGeometry(0.22,0.75,0.22),legMat);
  rLeg.position.set(0.18,0.7,0);group.add(rLeg);
  group.position.set(startX,0.42,startZ);
  scene.add(group);
  var personObj={group:group,type:'person',speed:0.12+Math.random()*0.1,
    dir:Math.random()>0.5?1:-1,axis:Math.random()>0.5?'x':'z',
    limit:300,walkPhase:0,lLeg:lLeg,rLeg:rLeg};
  movingObjects.push(personObj);
  placedObjects.push({meshes:[group],type:'person',cost:BUILD_COSTS.person,x:startX,z:startZ,movingRef:personObj});
}

function initGame(){
  vel = new THREE.Vector3();
  renderer=new THREE.WebGLRenderer({canvas:canvas,antialias:true,powerPreference:'high-performance'});
  renderer.setSize(window.innerWidth,window.innerHeight);
  renderer.setPixelRatio(Math.min(window.devicePixelRatio,2));
  renderer.shadowMap.enabled=false;
  renderer.toneMapping=THREE.ACESFilmicToneMapping;
  renderer.toneMappingExposure=1.0;

  window.addEventListener('resize',function(){
    var w=window.innerWidth, h=window.innerHeight;
    renderer.setSize(w,h);
    renderer.setPixelRatio(Math.min(window.devicePixelRatio,2));
    camera.aspect=w/h;
    camera.updateProjectionMatrix();
  });
  window.addEventListener('orientationchange',function(){
    setTimeout(function(){
      var w=window.innerWidth, h=window.innerHeight;
      renderer.setSize(w,h);
      renderer.setPixelRatio(Math.min(window.devicePixelRatio,2));
      camera.aspect=w/h;
      camera.updateProjectionMatrix();
    },300);
  });

  scene=new THREE.Scene();
  scene.background=new THREE.Color(0x050100);
  scene.fog=new THREE.FogExp2(0xc8e0f0,0.002);
  camera=new THREE.PerspectiveCamera(75,window.innerWidth/window.innerHeight,0.1,2000);
  camera.position.set(0,100,160);
  ambientLight=new THREE.AmbientLight(0x1a0800,0.6);scene.add(ambientLight);
  sun=new THREE.DirectionalLight(0xffffff,2.5);sun.position.set(200,400,200);scene.add(sun);
  towerLight=new THREE.PointLight(0xff4400,5,500);towerLight.position.set(0,200,0);scene.add(towerLight);
  [[0,5,0,0xff4400,2,300],[80,10,80,0xff2200,1.5,200],[-60,8,-60,0xffaa00,1.5,200]].forEach(function(_a){
    var l=new THREE.PointLight(_a[3],_a[4],_a[5]);l.position.set(_a[0],_a[1],_a[2]);scene.add(l);
  });

  var gnd=new THREE.Mesh(
    new THREE.PlaneGeometry(8000,8000,1,1),
    new THREE.MeshStandardMaterial({color:0xf0ede8,roughness:0.98,metalness:0.0})
  );
  gnd.rotation.x=-Math.PI/2;gnd.receiveShadow=true;scene.add(gnd);
  var grid1=new THREE.GridHelper(3000,100,0xbbbbbb,0xcccccc);
  grid1.position.y=0.05;scene.add(grid1);
  var grid2=new THREE.GridHelper(3000,300,0xcccccc,0xdddddd);
  grid2.position.y=0.06;scene.add(grid2);

  altEl=document.getElementById('alt-value');
  spdEl=document.getElementById('spd-value');

  timeLabelEl=document.createElement('div');
  timeLabelEl.style.cssText='position:absolute;top:20px;left:16px;font-family:Orbitron,monospace;font-size:9px;letter-spacing:4px;color:rgba(255,120,0,0.6);';
  document.getElementById('hud').appendChild(timeLabelEl);

  // Initial roads
  [[0,0,10,400,true],[0,0,400,10,false]].forEach(function(a){mkR.apply(null,a);});
  (function(){
    for(var rx=-200;rx<=200;rx+=TILE){ roadTiles.add(rx+'_0'); }
    for(var rz=-200;rz<=200;rz+=TILE){ roadTiles.add('0_'+rz); }
  })();
  [[28,0,22,22,80],[-30,10,18,18,65],[12,-28,20,20,72]].forEach(function(b){mkB.apply(null,b);});

  // Tower
  var tower=new THREE.Mesh(new THREE.CylinderGeometry(5,8,210,8),new THREE.MeshStandardMaterial({color:0x0f0500,emissive:0x770000,emissiveIntensity:0.3,roughness:0.4,metalness:0.9}));
  tower.position.set(0,105,0);scene.add(tower);
  for(var i=0;i<4;i++){
    var ring=new THREE.Mesh(new THREE.TorusGeometry(7-i*0.5,0.3,8,32),new THREE.MeshBasicMaterial({color:0xff4400,transparent:true,opacity:0.6}));
    ring.position.set(0,30+i*50,0);scene.add(ring);
  }
  topGlow=new THREE.Mesh(new THREE.SphereGeometry(4,16,16),new THREE.MeshBasicMaterial({color:0xff4400}));
  topGlow.position.set(0,212,0);scene.add(topGlow);

  // Stars
  var sp=new Float32Array(4000*3);
  for(var i=0;i<4000;i++){sp[i*3]=(Math.random()-0.5)*2000;sp[i*3+1]=Math.random()*600+50;sp[i*3+2]=(Math.random()-0.5)*2000;}
  var sg=new THREE.BufferGeometry();sg.setAttribute('position',new THREE.BufferAttribute(sp,3));
  starsMat=new THREE.PointsMaterial({color:0xccddff,size:0.8,transparent:true,opacity:0.0,sizeAttenuation:true});
  var starsObj=new THREE.Points(sg,starsMat);scene.add(starsObj);
  var brightSp=new Float32Array(200*3);
  for(var i=0;i<200;i++){
    var theta=Math.random()*Math.PI*2, phi=Math.random()*Math.PI*0.6, r2=900;
    brightSp[i*3]=Math.sin(phi)*Math.cos(theta)*r2;
    brightSp[i*3+1]=Math.abs(Math.cos(phi))*r2+50;
    brightSp[i*3+2]=Math.sin(phi)*Math.sin(theta)*r2;
  }
  var brightSg=new THREE.BufferGeometry();brightSg.setAttribute('position',new THREE.BufferAttribute(brightSp,3));
  brightStarsMat=new THREE.PointsMaterial({color:0xffffff,size:2.5,transparent:true,opacity:0.0,sizeAttenuation:true});
  var brightStars=new THREE.Points(brightSg,brightStarsMat);scene.add(brightStars);

  // Sun & Moon
  sunSphere=new THREE.Mesh(new THREE.SphereGeometry(28,32,32),new THREE.MeshBasicMaterial({color:0xfffde0,transparent:true,opacity:1.0}));
  scene.add(sunSphere);
  sunGlowMat=new THREE.MeshBasicMaterial({color:0xffdd55,transparent:true,opacity:0.35,side:THREE.BackSide});
  sunGlow=new THREE.Mesh(new THREE.SphereGeometry(48,32,32),sunGlowMat);scene.add(sunGlow);
  sunGlow2Mat=new THREE.MeshBasicMaterial({color:0xff9922,transparent:true,opacity:0.12,side:THREE.BackSide});
  sunGlow2=new THREE.Mesh(new THREE.SphereGeometry(80,32,32),sunGlow2Mat);scene.add(sunGlow2);
  moonSphere=new THREE.Mesh(new THREE.SphereGeometry(20,32,32),new THREE.MeshBasicMaterial({color:0xeef4ff,transparent:true,opacity:0.0}));
  scene.add(moonSphere);
  moonGlowMat=new THREE.MeshBasicMaterial({color:0x8899dd,transparent:true,opacity:0.0,side:THREE.BackSide});
  moonGlow=new THREE.Mesh(new THREE.SphereGeometry(34,32,32),moonGlowMat);scene.add(moonGlow);

  // Controls
  document.addEventListener('keydown',function(e){keys[e.code]=true;if(['Space','ArrowUp','ArrowDown'].includes(e.code))e.preventDefault();});
  document.addEventListener('keyup',function(e){keys[e.code]=false;});
  canvas.addEventListener('mousedown',function(e){isDragging=true;lastX=e.clientX;lastY=e.clientY;});
  window.addEventListener('mouseup',function(){isDragging=false;});
  window.addEventListener('mousemove',function(e){
    if(!isDragging)return;
    yaw-=(e.clientX-lastX)*0.003;
    pitch=Math.max(-1.1,Math.min(1.1,pitch-(e.clientY-lastY)*0.003));
    lastX=e.clientX;lastY=e.clientY;
  });

  // Joystick setup
  var jlBase=document.getElementById('jleft-base'),jlKnob=document.getElementById('jleft-knob');
  var jrBase=document.getElementById('jright-base'),jrKnob=document.getElementById('jright-knob');
  if(jlBase) jlBase.addEventListener('touchstart',function(e){
    e.preventDefault();
    var touch=e.changedTouches[0]; jlTid=touch.identifier;
    var r=jlBase.getBoundingClientRect(); jlCx=r.left+r.width/2; jlCy=r.top+r.height/2;
  },{passive:false});
  if(jrBase) jrBase.addEventListener('touchstart',function(e){
    e.preventDefault();
    var touch=e.changedTouches[0]; jrTid=touch.identifier;
    var r=jrBase.getBoundingClientRect(); jrCx=r.left+r.width/2; jrCy=r.top+r.height/2;
  },{passive:false});
  window.addEventListener('touchmove',function(e){
    for(var i=0;i<e.changedTouches.length;i++){
      var touch=e.changedTouches[i];
      if(touch.identifier===jlTid && jlKnob){
        var ox=touch.clientX-jlCx, oy=touch.clientY-jlCy;
        var dist=Math.sqrt(ox*ox+oy*oy);
        if(dist>JOY_MAX){ox=ox/dist*JOY_MAX;oy=oy/dist*JOY_MAX;}
        jlKnob.style.transform='translate(calc(-50% + '+ox+'px),calc(-50% + '+oy+'px))';
        jlX=ox/JOY_MAX; jlY=oy/JOY_MAX;
      }
      if(touch.identifier===jrTid && jrKnob){
        var ox=touch.clientX-jrCx, oy=touch.clientY-jrCy;
        var dist=Math.sqrt(ox*ox+oy*oy);
        if(dist>JOY_MAX){ox=ox/dist*JOY_MAX;oy=oy/dist*JOY_MAX;}
        jrKnob.style.transform='translate(calc(-50% + '+ox+'px),calc(-50% + '+oy+'px))';
        jrX=ox/JOY_MAX; jrY=oy/JOY_MAX;
      }
    }
  },{passive:true});
  window.addEventListener('touchend',function(e){
    for(var i=0;i<e.changedTouches.length;i++){
      var touch=e.changedTouches[i];
      if(touch.identifier===jlTid){jlTid=null;jlX=0;jlY=0;if(jlKnob)jlKnob.style.transform='translate(-50%,-50%)';}
      if(touch.identifier===jrTid){jrTid=null;jrX=0;jrY=0;if(jrKnob)jrKnob.style.transform='translate(-50%,-50%)';}
    }
  });

  // FIX #3: btn-up/btn-down safe null-checked
  var bU=document.getElementById('btn-up');
  var bD=document.getElementById('btn-down'); // was null due to broken HTML
  if(bU){
    bU.addEventListener('touchstart',function(e){e.preventDefault();btnUp=true;},{passive:false});
    bU.addEventListener('touchend',function(){btnUp=false;});
  }
  if(bD){
    bD.addEventListener('touchstart',function(e){e.preventDefault();btnDown=true;},{passive:false});
    bD.addEventListener('touchend',function(){btnDown=false;});
  }

  updateCoinDisplay();
  setTimeout(function(){ showNotif('IGNIS CITY — 全エリア解放済み'); }, 500);
}

function ignis_startGame(){
  ignisLoop();
}

function ignisLoop(){
  requestAnimationFrame(function(){ ignisLoop(); });
  var _now=performance.now(); var dt=(_now-_lastT)/1000; _lastT=_now; t+=dt;

  var cycle=(t%TIME_CYCLE)/TIME_CYCLE;
  var totalSteps=timeColors.length;
  var pos=cycle*totalSteps;
  var idx=Math.floor(pos)%totalSteps;
  var nextIdx=(idx+1)%totalSteps;
  var frac=pos-Math.floor(pos);
  var tc=timeColors[idx], tn=timeColors[nextIdx];
  var skyCol=lerpColor(tc.sky,tn.sky,frac);
  scene.background.setHex(skyCol);
  scene.fog.color.setHex(skyCol);
  var fogDensities=[0.002,0.0008,0.0008,0.0008,0.005];
  scene.fog.density=fogDensities[idx]+(fogDensities[nextIdx]-fogDensities[idx])*frac;
  ambientLight.color.setHex(lerpColor(tc.amb,tn.amb,frac));
  ambientLight.intensity=0.4+(tc.si+(tn.si-tc.si)*frac)*0.8;
  sun.color.setHex(lerpColor(tc.sun,tn.sun,frac));
  sun.intensity=tc.si+(tn.si-tc.si)*frac;
  var sunAngle=(cycle*Math.PI*2)-Math.PI*0.5;
  sun.position.set(Math.cos(sunAngle)*300,Math.abs(Math.sin(sunAngle))*300+50,Math.sin(sunAngle)*200);
  timeLabelEl.textContent=tc.label;

  var SPD=0.18, DAMP=0.82, MAX_SPD=1.0;
  yaw-=jrX*0.035;
  pitch=Math.max(-1.0,Math.min(1.0,pitch-jrY*0.035));
  var flatFwd=new THREE.Vector3(-Math.sin(yaw),0,-Math.cos(yaw));
  var right=new THREE.Vector3(Math.cos(yaw),0,-Math.sin(yaw));
  if(keys['KeyW'])vel.addScaledVector(flatFwd,SPD);
  if(keys['KeyS'])vel.addScaledVector(flatFwd,-SPD*0.7);
  if(keys['KeyA'])vel.addScaledVector(right,-SPD);
  if(keys['KeyD'])vel.addScaledVector(right,SPD);
  if(keys['Space'])vel.y+=SPD;
  if(keys['ShiftLeft']||keys['ShiftRight'])vel.y-=SPD;
  if(jlX!==0||jlY!==0){vel.addScaledVector(right,jlX*SPD);vel.addScaledVector(flatFwd,-jlY*SPD);}
  if(btnUp)vel.y+=SPD; if(btnDown)vel.y-=SPD;
  var hv=new THREE.Vector3(vel.x,0,vel.z);
  if(hv.length()>MAX_SPD){hv.normalize().multiplyScalar(MAX_SPD);vel.x=hv.x;vel.z=hv.z;}
  vel.y=Math.max(-MAX_SPD,Math.min(MAX_SPD,vel.y));
  vel.multiplyScalar(DAMP);
  camera.position.add(vel);
  camera.position.y=Math.max(3,Math.min(800,camera.position.y));
  camera.position.x=Math.max(-3000,Math.min(3000,camera.position.x));
  camera.position.z=Math.max(-3000,Math.min(3000,camera.position.z));
  camera.quaternion.setFromEuler(new THREE.Euler(pitch,yaw,0,'YXZ'));

  var SUN_RADIUS=600;
  var sunA=(cycle*Math.PI*2)-Math.PI*0.5;
  sunSphere.position.set(Math.cos(sunA)*SUN_RADIUS,Math.sin(sunA)*SUN_RADIUS,Math.sin(sunA*0.3)*200);
  sunGlow.position.copy(sunSphere.position);
  sunGlow2.position.copy(sunSphere.position);
  var moonA=sunA+Math.PI;
  moonSphere.position.set(Math.cos(moonA)*SUN_RADIUS,Math.sin(moonA)*SUN_RADIUS,Math.sin(moonA*0.3)*200);
  moonGlow.position.copy(moonSphere.position);
  var sunVis=Math.max(0,Math.min(1,(sunSphere.position.y+30)/100));
  var moonVis=Math.max(0,Math.min(1,(moonSphere.position.y+80)/160));
  sunSphere.material.opacity=Math.min(1.0,sunVis*1.2);
  sunGlowMat.opacity=sunVis*0.5;
  sunGlow2Mat.opacity=sunVis*0.18;
  moonSphere.material.opacity=Math.min(1.0,moonVis*1.1);
  moonGlowMat.opacity=moonVis*0.35;
  var nightness=Math.max(0,1.0-sunVis*3);
  starsMat.opacity=nightness*0.85;
  brightStarsMat.opacity=nightness*1.0;

  if(windowMeshes.length>0){
    var isDawn=(cycle<0.15||cycle>0.92);
    var isEvening=(cycle>0.55&&cycle<0.75);
    var isNight=(cycle>0.75||cycle<0.08);
    windowMeshes.forEach(function(w){
      var targetOp=0, targetCol=0xffffaa;
      if(sunVis>0.5){ targetOp=0.08+w.rand*0.08; targetCol=0xaaddff; }
      else if(isEvening){ if(w.rand<0.4){targetOp=0.5+w.rand*0.5;targetCol=0xffdd88;}else{targetOp=0.0;} }
      else if(isNight){ if(w.rand<0.25){targetOp=0.3+w.rand*0.6+Math.sin(t*3+w.rand*10)*0.05;targetCol=0xffcc66;}else{targetOp=0.0;} }
      else { targetOp=nightness*(0.4+w.rand*0.6); targetCol=0xffee99; }
      w.mesh.material.opacity+=(targetOp-w.mesh.material.opacity)*0.04;
      if(w.mesh.material.color) w.mesh.material.color.setHex(targetCol);
    });
  }

  topGlow.material.opacity=0.6+Math.sin(t*3)*0.3;
  towerLight.intensity=4+Math.sin(t*2)*2;
  if(altEl) altEl.textContent=Math.round(camera.position.y);
  if(spdEl) spdEl.textContent=Math.round(vel.length()*55);

  movingObjects.forEach(function(obj){
    var g=obj.group;
    var nx=g.position.x, nz=g.position.z;
    if(obj.axis==='x'){ nx+=obj.speed*obj.dir; g.rotation.y=obj.dir>0?Math.PI/2:-Math.PI/2; }
    else { nz+=obj.speed*obj.dir; g.rotation.y=obj.dir>0?0:Math.PI; }
    if(obj.onRoadOnly){
      var tk=Math.round(nx/TILE)*TILE+'_'+Math.round(nz/TILE)*TILE;
      if(roadTiles.has(tk)){ g.position.x=nx; g.position.z=nz; }
      else {
        obj.dir*=-1;
        if(obj.axis==='x') g.rotation.y=obj.dir>0?Math.PI/2:-Math.PI/2;
        else g.rotation.y=obj.dir>0?0:Math.PI;
      }
    } else { g.position.x=nx; g.position.z=nz; }
    if(obj.axis==='x' && Math.abs(g.position.x)>obj.limit) obj.dir*=-1;
    if(obj.axis==='z' && Math.abs(g.position.z)>obj.limit) obj.dir*=-1;
    if(obj.type==='person' && obj.lLeg){
      obj.walkPhase+=0.12;
      obj.lLeg.rotation.x=Math.sin(obj.walkPhase)*0.5;
      obj.rLeg.rotation.x=Math.sin(obj.walkPhase+Math.PI)*0.5;
    }
    if(obj.type==='bike') g.rotation.z=obj.dir*0.06;
  });

  riverMeshes.forEach(function(m){
    if(m.userData.isFountainDrop){
      var d=m.userData; d.phase+=dt*2.5;
      var up=Math.sin(d.phase)*d.height;
      var spread=Math.abs(Math.sin(d.phase))*d.radius;
      m.position.set(d.fx+Math.cos(d.angle)*spread,7.5+up,d.fz+Math.sin(d.angle)*spread);
      m.material.opacity=Math.max(0,Math.sin(d.phase)*0.85);
      return;
    }
    if(m.userData.isStream){
      var ph=t*0.02*60+m.userData.phase;
      var scroll=((ph%1)+1)%1;
      m.material.opacity=0.15+Math.sin(t*2+m.userData.streamIndex)*0.15+scroll*0.1;
      if(m.userData.isV){ m.position.z=m.userData.rz+(scroll-0.5)*TILE*1.8; }
      else { m.position.x=m.userData.rx+(scroll-0.5)*TILE*1.8; }
    }
  });

  fishMeshes.forEach(function(fish){
    if(fish.group){
      fish.angle+=fish.speed*0.008;
      var r=fish.radius*(0.85+Math.sin(t*0.3+fish.phase)*0.15);
      fish.group.position.x=fish.lx+Math.cos(fish.angle)*r;
      fish.group.position.z=fish.lz+Math.sin(fish.angle)*r;
      fish.group.position.y=fish.depth+Math.sin(t*1.5+fish.phase)*0.12;
      fish.group.rotation.y=-fish.angle+Math.PI/2;
      fish.group.rotation.z=Math.sin(t*4+fish.phase)*0.06;
    }
  });

  drawMM();
  renderer.render(scene,camera);
}
</script>

</body>
</html>
