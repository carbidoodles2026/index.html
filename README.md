<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1, viewport-fit=cover">
<title>Neon Words</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Baloo+2:wght@500;700;800&family=Chewy&display=swap" rel="stylesheet">
<script src="https://unpkg.com/peerjs@1.5.4/dist/peerjs.min.js"></script>
<style>
@property --ang{syntax:'<angle>';initial-value:0deg;inherits:false}
*{box-sizing:border-box;-webkit-tap-highlight-color:transparent}
html,body{margin:0}
body{min-height:100vh;color:#fff;font-family:'Baloo 2','Comic Sans MS',system-ui,sans-serif;
  background:radial-gradient(circle at 12% 8%,#47107f 0,transparent 45%),radial-gradient(circle at 88% 92%,#0b3f78 0,transparent 45%),#0b0620 fixed}
.hidden{display:none!important}
button{font-family:inherit;cursor:pointer}
.chew{font-family:'Chewy','Comic Sans MS',cursive}

/* setup */
#setup{display:flex;justify-content:center;padding:18px 12px 40px}
.card{width:min(580px,100%);background:rgba(20,10,50,.7);backdrop-filter:blur(8px);border:2px solid rgba(255,255,255,.2);border-radius:26px;padding:20px 18px;box-shadow:0 0 30px rgba(180,80,255,.35)}
.logo{font-family:'Chewy',cursive;text-align:center;margin:0;font-size:clamp(44px,12vw,76px);line-height:1;letter-spacing:2px}
.logo span{display:inline-block;color:#fff;animation:flick 3s infinite;text-shadow:0 0 4px #fff,0 0 12px hsl(var(--h) 100% 60%),0 0 28px hsl(var(--h) 100% 55%),0 4px 0 hsl(var(--h) 80% 30%)}
@keyframes flick{0%,92%,100%{opacity:1;transform:translateY(0)}94%{opacity:.5}96%{opacity:1;transform:translateY(-4px)}}
.sub{text-align:center;opacity:.85;margin:4px 0 14px}
.tabs{display:flex;gap:8px;margin-bottom:12px}
.tabs button{flex:1;padding:10px;border-radius:14px;border:2px solid rgba(255,255,255,.3);background:rgba(255,255,255,.08);color:#fff;font-weight:800;font-size:16px}
.tabs button.on{background:linear-gradient(135deg,#ff2e93,#7a5cff);border-color:#fff}
.count-row{display:flex;gap:8px;align-items:center;justify-content:center;margin-bottom:10px;font-weight:700}
.cbtn{width:44px;height:44px;border-radius:12px;border:2px solid rgba(255,255,255,.4);background:rgba(255,255,255,.1);color:#fff;font-size:20px;font-weight:800}
.cbtn.on{background:linear-gradient(135deg,#ffd400,#ff7a00);border-color:#fff}
.pcard{display:flex;gap:12px;align-items:center;background:rgba(0,0,0,.3);border-left:8px solid var(--pc);border-radius:16px;padding:8px 10px;margin-bottom:8px}
.avatar-pick{cursor:pointer;flex:none}
.avatar{width:64px;height:64px;border-radius:50%;border:4px solid var(--pc);background:#fff center/cover no-repeat;display:grid;place-items:center;font-size:26px;box-shadow:0 0 0 3px #fff,0 0 14px var(--pc)}
.avatar.empty{background:linear-gradient(135deg,#fff,#ddd);color:#333}
.pinfo{flex:1;min-width:0}
.pname,#roomCode{width:100%;font:inherit;font-weight:700;font-size:17px;padding:6px 10px;border-radius:10px;border:2px solid rgba(255,255,255,.35);background:#fff;color:#222}
.hint{display:block;font-size:12px;opacity:.8;margin-top:3px}
.linkbtn{background:none;border:none;color:#ff9aa8;font-size:12px;font-weight:700;padding:0;text-decoration:underline}
.two{display:grid;grid-template-columns:1fr 1fr;gap:10px;margin:10px 0}
@media(max-width:480px){.two{grid-template-columns:1fr}}
.box{background:rgba(255,255,255,.07);border:2px solid rgba(255,255,255,.18);border-radius:16px;padding:10px}
.box h3{margin:0 0 6px;font-size:17px}
.btn{padding:10px 14px;border-radius:12px;border:none;font-weight:800;font-size:16px;color:#fff;background:linear-gradient(135deg,#7a5cff,#ff2e93);width:100%;margin-top:6px}
.btn:disabled{opacity:.45;cursor:not-allowed}
.opts{display:flex;gap:12px;flex-wrap:wrap;align-items:center;justify-content:center;margin:12px 0 2px;font-weight:700;font-size:14px}
.opts select{font:inherit;padding:4px 8px;border-radius:8px}
.chip{font-size:12px;padding:2px 8px;border-radius:10px;background:rgba(255,255,255,.15)}
.bigbtn{display:block;width:100%;margin-top:12px;padding:14px;border-radius:18px;border:none;font-size:24px;font-weight:800;color:#fff;font-family:'Chewy',cursive;letter-spacing:1px;
  background:linear-gradient(135deg,#00e676,#00b0ff);box-shadow:0 6px 0 #00507a,0 0 24px rgba(0,230,255,.5)}
.bigbtn:active{transform:translateY(4px);box-shadow:0 2px 0 #00507a}
.code{font-family:'Chewy';font-size:44px;letter-spacing:8px;text-align:center;color:#ffd400;text-shadow:0 0 14px #ff9a00}
.lobbyrow{display:flex;align-items:center;gap:8px;padding:4px 0}
.lobbyrow .pav{width:34px;height:34px;font-size:15px}

/* game layout */
#game{padding:8px}
.gridwrap{display:grid;grid-template-columns:minmax(0,auto) 330px;gap:16px;justify-content:center;align-items:start}
@media(max-width:1000px){.gridwrap{grid-template-columns:minmax(0,1fr);justify-items:center}}
.main{display:flex;flex-direction:column;align-items:center;gap:8px}
aside{width:min(330px,100%);display:flex;flex-direction:column;gap:10px}
.turnbar{display:flex;align-items:center;gap:10px;width:100%;max-width:720px;background:rgba(255,255,255,.08);border:2px solid rgba(255,255,255,.18);border-radius:18px;padding:4px 12px}
.hg{width:46px;height:70px;flex:none;filter:drop-shadow(0 0 6px #8cf)}
.hg.flipping{animation:hgflip .8s cubic-bezier(.5,0,.3,1)}
@keyframes hgflip{to{transform:rotate(180deg)}}
.hg.urgent{animation:shake .3s infinite}
@keyframes shake{25%{transform:rotate(-6deg)}75%{transform:rotate(6deg)}}
.stream{animation:dash .35s linear infinite}
@keyframes dash{to{stroke-dashoffset:-6}}
#timeTxt{font-family:'Chewy';font-size:34px;min-width:78px;text-shadow:0 0 12px #ffd400}
#timeTxt.urgent{color:#ff4d6d;text-shadow:0 0 14px #f00}
.tinfo{flex:1;min-width:0;line-height:1.1}
.tinfo b{font-size:18px}
.tinfo small{opacity:.8}

/* moving vibrant border */
.frame{--bw:clamp(270px,min(100vw - 44px,100vh - 300px),700px);--cell:calc(var(--bw)/15);position:relative;padding:11px;border-radius:24px;isolation:isolate}
.frame::before,.frame::after{content:'';position:absolute;inset:0;border-radius:24px;z-index:-1;
  background:conic-gradient(from var(--ang),#ff0080,#ff8c00,#ffe600,#00ff6a,#00e5ff,#7a5cff,#ff0080);animation:spin 4s linear infinite,hue 9s linear infinite}
.frame::after{filter:blur(20px);opacity:.85}
@keyframes spin{to{--ang:360deg}}
@keyframes hue{to{filter:hue-rotate(360deg)}}
.frame::after{animation:spin 4s linear infinite}
.board{width:var(--bw);height:var(--bw);display:grid;grid-template:repeat(15,1fr)/repeat(15,1fr);gap:2px;background:#0d0626;padding:3px;border-radius:15px}
.cell{position:relative;border-radius:5px;background:rgba(255,255,255,.07);display:grid;place-items:center;--ts:var(--cell);
  font-size:calc(var(--cell)*.3);font-weight:800;text-shadow:0 1px 2px rgba(0,0,0,.6)}
.cell.hot{outline:3px solid #fff;outline-offset:-3px}
.p-TW{background:linear-gradient(145deg,#ff3b5c,#b8002a);box-shadow:inset 0 0 8px #ff9aa8}
.p-DW{background:linear-gradient(145deg,#ff7ad9,#b81c98);box-shadow:inset 0 0 8px #ffc2f0}
.p-TL{background:linear-gradient(145deg,#4d8dff,#1237c9);box-shadow:inset 0 0 8px #aac8ff}
.p-DL{background:linear-gradient(145deg,#3ee6ff,#0a8fb8);box-shadow:inset 0 0 8px #b5f5ff}
.cell.star{font-size:calc(var(--cell)*.6);animation:twinkle 1.6s infinite alternate}
@keyframes twinkle{to{filter:brightness(1.5)}}

/* tiles */
.tile{--ts:var(--cell);position:relative;border-radius:22%;display:grid;place-items:center;user-select:none;
  background:linear-gradient(150deg,hsl(var(--h) 100% 64%),hsl(calc(var(--h) + 30) 100% 46%));border:2px solid hsl(var(--h) 100% 82%);
  box-shadow:0 0 8px hsl(var(--h) 100% 60%/.9),0 0 18px hsl(var(--h) 100% 55%/.5),inset 0 2px 3px rgba(255,255,255,.6),inset 0 -3px 4px rgba(0,0,0,.25)}
.cell .tile{position:absolute;inset:5%}
.tile .ch{font-family:'Chewy','Comic Sans MS',cursive;font-size:calc(var(--ts)*.66);line-height:1;color:#fff;
  text-shadow:0 0 4px #fff,0 0 10px hsl(var(--h) 100% 72%),2px 2px 0 hsl(var(--h) 85% 28%),-1px -1px 0 hsl(var(--h) 85% 28%)}
.tile .pv{position:absolute;right:calc(var(--ts)*.08);bottom:calc(var(--ts)*.02);font-family:'Baloo 2';font-weight:800;font-size:calc(var(--ts)*.25);color:#fff;text-shadow:0 1px 2px #000}
.tile.blank{background:linear-gradient(150deg,#2b1b5e,#150b3a);border:2px dashed #fff}
.tile.blank .ch{text-shadow:0 0 8px hsl(var(--h) 100% 65%),0 0 16px hsl(var(--h) 100% 55%)}
.tile.placed::after{content:'';position:absolute;left:8%;top:8%;width:17%;height:17%;border-radius:50%;background:var(--pc);box-shadow:0 0 6px var(--pc)}
.tile.last{animation:lastp 1s ease-in-out infinite alternate}
@keyframes lastp{to{box-shadow:0 0 14px #fff,0 0 26px #fff}}
.tile.pend{outline:2px solid #fff;touch-action:none;cursor:grab;z-index:3}
.tile.ghost{opacity:.8;outline:3px dashed var(--pc);animation:ghost .7s infinite alternate}
@keyframes ghost{to{transform:translateY(-4px) scale(1.06)}}
.tile.pop{animation:pop .5s cubic-bezier(.2,1.8,.4,1)}
@keyframes pop{from{transform:scale(.2) rotate(-90deg)}}
.tile.dragging{position:fixed;z-index:1000;pointer-events:none;transform:translate(-50%,-62%) scale(1.15) rotate(-5deg);box-shadow:0 14px 24px rgba(0,0,0,.5)}
.tile.fly{position:fixed;z-index:1200;pointer-events:none;margin:0}

/* rack */
.rackwrap{width:100%;max-width:720px;display:flex;flex-direction:column;gap:6px;align-items:center}
.rack{--rt:clamp(38px,10.8vw,64px);display:flex;gap:6px;justify-content:center;padding:9px;border-radius:18px;background:rgba(255,255,255,.08);border:2px solid rgba(255,255,255,.18);width:100%}
.rslot{width:var(--rt);height:var(--rt);border-radius:22%;flex:none}
.rslot.empty{border:2px dashed rgba(255,255,255,.25)}
.rslot .tile{--ts:var(--rt);width:100%;height:100%;touch-action:none;cursor:grab}
.rslot .tile.sel{transform:translateY(-8px) scale(1.08);outline:3px solid #fff}
.rslot .tile.deal{animation:deal .55s cubic-bezier(.2,1.5,.4,1) backwards;animation-delay:calc(var(--i)*70ms)}
@keyframes deal{from{transform:translateY(-160px) rotate(-220deg) scale(.3);opacity:0}}
.btns{display:flex;gap:6px;flex-wrap:wrap;justify-content:center;width:100%}
.btns button{flex:1;min-width:72px;padding:9px 6px;border-radius:12px;border:2px solid rgba(255,255,255,.35);background:rgba(255,255,255,.1);color:#fff;font-weight:800;font-size:14px}
.btns button.primary{flex:2;background:linear-gradient(135deg,#00e676,#00b0ff);font-size:17px;border-color:#fff}
.btns button:disabled{opacity:.4;cursor:not-allowed}
#preview{min-height:22px;font-weight:700;font-size:14px;text-align:center}
#preview.bad{color:#ff9aa8}
#preview.good{color:#8dffb0}

/* side */
.panel{background:rgba(255,255,255,.08);border:2px solid rgba(255,255,255,.18);border-radius:18px;padding:8px}
.pl{display:flex;align-items:center;gap:10px;padding:6px;border-radius:14px;border:3px solid transparent;transition:.2s}
.pl.active{background:rgba(255,255,255,.14);border-color:var(--pc);box-shadow:0 0 16px var(--pc)}
.pav{width:42px;height:42px;border-radius:50%;border:3px solid var(--pc);background:var(--pc) center/cover no-repeat;display:grid;place-items:center;font-weight:800;flex:none;box-shadow:0 0 0 2px #fff}
.pmeta{flex:1;min-width:0}.pn{font-weight:800;line-height:1}.pt{font-size:12px;opacity:.8}
.psc{font-family:'Chewy';font-size:30px;text-shadow:0 0 10px var(--pc)}
#log{max-height:150px;overflow:auto;font-size:13px}
#log div{padding:2px 4px;border-bottom:1px dashed rgba(255,255,255,.15)}

/* overlays */
.overlay{position:fixed;inset:0;background:rgba(5,2,20,.86);display:grid;place-items:center;z-index:300;padding:14px;overflow:auto}
.modal{background:linear-gradient(160deg,#3a1170,#10337a);border:3px solid #fff;border-radius:24px;padding:18px;max-width:min(440px,94vw);text-align:center;box-shadow:0 0 40px rgba(160,80,255,.6)}
.modal h2{margin:0 0 8px;font-family:'Chewy';font-size:30px}
.letters{display:grid;grid-template-columns:repeat(7,1fr);gap:6px;margin:8px 0}
.letters .tile{--ts:40px;width:40px;height:40px;cursor:pointer}
.xrack{display:flex;gap:6px;justify-content:center;flex-wrap:wrap;margin:10px 0}
.xrack .tile{--ts:46px;width:46px;height:46px;cursor:pointer}
.xrack .tile.sel{transform:translateY(-8px);outline:3px solid #fff}
.row2{display:flex;gap:8px}.row2 button{flex:1}
.rank{display:flex;align-items:center;gap:10px;margin:6px 0;text-align:left}
.rank .psc{margin-left:auto}
.toast{position:fixed;left:50%;bottom:24px;transform:translateX(-50%);background:rgba(0,0,0,.85);border:2px solid #fff;padding:10px 18px;border-radius:16px;font-weight:700;z-index:500;max-width:90vw;text-align:center}
.score-pop{position:fixed;z-index:600;font-family:'Chewy';font-size:40px;pointer-events:none;color:#fff;text-shadow:0 0 14px var(--pc),0 0 28px var(--pc);animation:rise 1.6s ease-out forwards}
@keyframes rise{to{transform:translateY(-90px) scale(1.4);opacity:0}}
.conf{position:fixed;top:-20px;width:10px;height:16px;animation:fall linear infinite;z-index:310;pointer-events:none}
@keyframes fall{to{transform:translateY(110vh) rotate(720deg)}}
</style>
</head>
<body>

<div id="setup">
 <div class="card">
  <h1 class="logo" id="logo"></h1>
  <p class="sub">A neon Scrabble-style word game · 2–4 players</p>
  <div class="tabs"><button data-tab="local" class="on">👥 Same device</button><button data-tab="online">🌐 Play online</button></div>

  <div id="tab-local">
    <div class="count-row">Players: <button class="cbtn" data-n="2">2</button><button class="cbtn" data-n="3">3</button><button class="cbtn" data-n="4">4</button></div>
    <div id="localCards"></div>
  </div>

  <div id="tab-online" class="hidden">
    <div id="onlineMe"></div>
    <div class="two" id="roomBtns">
      <div class="box"><h3>🏠 Host a room</h3><button class="btn" id="createRoom">Create room</button></div>
      <div class="box"><h3>🔗 Join a room</h3><input id="roomCode" maxlength="5" placeholder="CODE"><button class="btn" id="joinRoom">Join</button></div>
    </div>
    <div id="lobby" class="box hidden"></div>
  </div>

  <div class="opts" id="opts">
    <label>⏳ Turn timer <select id="optTimer"><option value="0">Off</option><option value="30">30s</option><option value="60">60s</option><option value="90" selected>90s</option><option value="120">2 min</option><option value="180">3 min</option></select></label>
    <label><input type="checkbox" id="optDict" checked> Check words</label>
    <span class="chip" id="dictChip">dictionary: loading…</span>
  </div>
  <button class="bigbtn" id="startBtn">▶ Start Game</button>
 </div>
</div>

<div id="game" class="hidden">
 <div class="gridwrap">
  <section class="main">
   <div class="turnbar">
     <div class="hg" id="hg"></div>
     <div id="timeTxt">1:30</div>
     <div class="tinfo"><b id="turnName"></b><br><small id="turnSub"></small></div>
   </div>
   <div class="frame"><div class="board" id="board"></div></div>
   <div class="rackwrap">
     <div id="preview"></div>
     <div class="rack" id="rack"></div>
     <div class="btns">
       <button id="bShuffle">🔀 Shuffle</button><button id="bRecall">↩ Recall</button>
       <button class="primary" id="bPlay">▶ Play</button>
       <button id="bSwap">♻ Swap</button><button id="bPass">⏭ Pass</button>
     </div>
   </div>
  </section>
  <aside>
   <div class="panel" id="players"></div>
   <div class="panel" id="log"></div>
  </aside>
 </div>
</div>

<div id="cover" class="overlay hidden"></div>
<script>
