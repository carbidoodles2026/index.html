<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Scrabble Neon Arcade</title>
  <style>
    :root {
      --bg: #080b18;
      --panel: #111733;
      --cyan: #00f5ff;
      --pink: #ff2e93;
      --lime: #7cff2b;
      --yellow: #ffe600;
      --purple: #b54cff;
    }

    * { box-sizing: border-box; user-select: none; }

    body {
      margin: 0;
      min-height: 100vh;
      font-family: "Comic Sans MS", "Chalkboard SE", cursive, sans-serif;
      background: #080b18;
      color: var(--cyan);
      padding: 16px;
    }

    .neon-txt {
      paint-order: stroke fill;
      -webkit-text-stroke: 2px #000000;
      text-shadow: 2px 2px 0 #000, -1px -1px 0 #000, 1px -1px 0 #000, -1px 1px 0 #000, 0 0 10px currentColor;
    }

    .neon-txt-sm {
      paint-order: stroke fill;
      -webkit-text-stroke: 1.2px #000000;
      text-shadow: 1.5px 1.5px 0 #000, -1px -1px 0 #000, 1px -1px 0 #000, -1px 1px 0 #000, 0 0 7px currentColor;
    }

    .wrap { max-width: 1200px; margin: 0 auto; }
    .head { text-align: center; margin-bottom: 16px; }
    .head h1 { margin: 0; font-size: clamp(2rem, 5vw, 3.2rem); color: var(--yellow); }
    .head p { margin: 6px 0 0; color: var(--pink); }

    .card {
      background: var(--panel);
      border: 2px solid var(--cyan);
      border-radius: 20px;
      padding: 16px;
      margin-bottom: 14px;
    }

    .row-center {
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
      justify-content: center;
      align-items: center;
      margin-bottom: 14px;
    }

    .btn {
      font-family: inherit;
      font-weight: bold;
      border-radius: 12px;
      border: 2px solid #000;
      padding: 10px 16px;
      font-size: 0.95rem;
      cursor: pointer;
    }

    .btn-cyan { background: #00d2ff; color: var(--yellow); }
    .btn-pink { background: #ff2e93; color: var(--yellow); }
    .btn-lime { background: #5ce619; color: var(--yellow); }
    .btn-yellow { background: #ffd000; color: var(--cyan); }
    .btn-dark { background: #1a224d; color: var(--cyan); border-color: var(--cyan); }

    select, input[type="text"] {
      font-family: inherit;
      font-weight: bold;
      background: #0a0e22;
      color: var(--yellow);
      border: 2px solid var(--cyan);
      border-radius: 10px;
      padding: 8px 10px;
    }

    .setup-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(190px, 1fr));
      gap: 12px;
    }

    .p-box {
      background: #0a0e22;
      border: 2px solid var(--purple);
      border-radius: 16px;
      padding: 12px;
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 8px;
    }

    .av-circle {
      width: 64px;
      height: 64px;
      border-radius: 50%;
      border: 3px solid var(--yellow);
      overflow: hidden;
      display: flex;
      align-items: center;
      justify-content: center;
      background: #1a2352;
      font-size: 1.6rem;
    }

    .av-circle img { width: 100%; height: 100%; object-fit: cover; }

    #onlineBox {
      display: none;
      margin-top: 12px;
      padding: 12px;
      border-radius: 14px;
      background: #0a0e22;
      border: 2px dashed var(--lime);
    }

    #playArea {
      display: none;
      grid-template-columns: 260px 1fr 240px;
      gap: 14px;
      align-items: start;
    }

    @media (max-width: 980px) {
      #playArea { grid-template-columns: 1fr; }
    }

    /* Moving Border */
    .moving-border {
      position: relative;
      padding: 10px;
      border-radius: 24px;
      overflow: hidden;
      background: #060814;
    }

    .moving-border::before {
      content: "";
      position: absolute;
      inset: -70%;
      background: conic-gradient(#ff2e93, #ffe600, #7cff2b, #00f5ff, #b54cff, #ff2e93);
      animation: spinSlow 5s linear infinite;
    }

    .moving-border::after {
      content: "";
      position: absolute;
      inset: 5px;
      border-radius: 20px;
      background: #090d20;
    }

    @keyframes spinSlow { to { transform: rotate(360deg); } }

    .board {
      position: relative;
      z-index: 2;
      display: grid;
      grid-template-columns: repeat(15, 1fr);
      gap: 2px;
      aspect-ratio: 1 / 1;
      background: #050712;
      padding: 4px;
      border-radius: 14px;
    }

    .cell {
      border-radius: 5px;
      border: 1px solid rgba(255,255,255,0.14);
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: clamp(0.45rem, 1vw, 0.72rem);
      font-weight: bold;
      cursor: pointer;
      overflow: hidden;
    }

    .c-0 { background: #131a38; color: var(--cyan); }
    .c-DL { background: #0e3b66; color: var(--cyan); }
    .c-TL { background: #144731; color: var(--lime); }
    .c-DW { background: #5e173a; color: var(--pink); }
    .c-TW { background: #612408; color: var(--yellow); }
    .c-STAR { background: #4c186b; color: var(--yellow); }

    .tile {
      width: 100%;
      height: 100%;
      border-radius: 7px;
      border: 2px solid #000;
      display: flex;
      align-items: center;
      justify-content: center;
      position: relative;
      font-weight: bold;
      cursor: pointer;
      animation: popIn 0.2s ease-out;
    }

    @keyframes popIn {
      from { transform: scale(0.5); }
      to { transform: scale(1); }
    }

    .tile.picked { outline: 3px solid var(--yellow); transform: translateY(-3px); }
    .t-ch { font-size: clamp(0.8rem, 1.7vw, 1.2rem); }
    .t-pt { position: absolute; right: 2px; bottom: 1px; font-size: 0.55rem; color: #fff; }

    .rack-wrap {
      margin-top: 12px;
      padding: 12px;
      border-radius: 18px;
      background: #101633;
      border: 2px solid var(--pink);
    }

    .rack {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      gap: 8px;
      min-height: 56px;
      margin-bottom: 10px;
    }

    .slot { width: 50px; height: 54px; }

    .p-item {
      display: flex;
      align-items: center;
      gap: 8px;
      padding: 8px;
      border-radius: 12px;
      background: #0a0e22;
      border: 2px solid rgba(255,255,255,0.12);
      margin-bottom: 8px;
    }

    .p-item.now { border-color: var(--lime); }
    .p-av {
      width: 38px; height: 38px; border-radius: 50%; border: 2px solid var(--cyan);
      overflow: hidden; display: flex; align-items: center; justify-content: center; background: #1a2352;
    }
    .p-av img { width: 100%; height: 100%; object-fit: cover; }

    .status {
      margin-bottom: 10px;
      padding: 8px 12px;
      border-radius: 12px;
      background: #12193b;
      border: 2px solid var(--cyan);
      color: var(--yellow);
      font-weight: bold;
      text-align: center;
    }

    .log { max-height: 220px; overflow-y: auto; font-size: 0.84rem; }
    .log div { padding: 5px 8px; margin-bottom: 6px; border-radius: 8px; background: #0a0e22; border-left: 3px solid var(--pink); }

    #camBox { display: none; margin-top: 10px; }
    video { width: 100%; border-radius: 10px; border: 2px solid var(--cyan); transform: scaleX(-1); }
  </style>
</head>
<body>
  <div class="wrap">
    <div class="head">
      <h1 class="neon-txt">Scrabble Neon Arcade</h1>
      <p class="neon-txt-sm">Neon Tiles + Black Outline • Moving Border • Sand Timer • 2–4 Players</p>
    </div>

    <div id="setupScreen" class="card">
      <div class="row-center">
        <button type="button" class="btn btn-cyan neon-txt-sm" onclick="chooseMode('local')">Local Pass & Play</button>
        <button type="button" class="btn btn-pink neon-txt-sm" onclick="chooseMode('online')">Play Online (2–4P)</button>
        <label class="neon-txt-sm" style="color:var(--lime);">
          Players:
          <select id="pNum" onchange="makeCards()">
            <option value="2" selected>2</option>
            <option value="3">3</option>
            <option value="4">4</option>
          </select>
        </label>
        <label class="neon-txt-sm" style="color:var(--yellow);">
          Sand Timer:
          <select id="tSec">
            <option value="60">60s</option>
            <option value="90" selected>90s</option>
            <option value="120">120s</option>
            <option value="0">Off</option>
          </select>
        </label>
      </div>

      <div id="cards" class="setup-grid"></div>

      <div id="onlineBox">
        <div class="neon-txt-sm" style="color:var(--lime);">Online Room Code</div>
        <div class="row-center" style="justify-content:flex-start; margin-top:8px;">
          <button type="button" class="btn btn-lime neon-txt-sm" onclick="genCode()">Generate Code</button>
          <input id="codeBox" type="text" placeholder="Room code..." />
          <span id="codeShow" class="neon-txt-sm" style="color:var(--yellow);"></span>
        </div>
      </div>

      <div class="row-center" style="margin-top:16px;">
        <button type="button" class="btn btn-yellow neon-txt" style="font-size:1.1rem; padding:12px 26px;" onclick="startNow()">
          Start Neon Scrabble!
        </button>
      </div>
    </div>

    <div id="playArea">
      <div class="card">
        <h3 class="neon-txt-sm" style="margin:0 0 8px; color:var(--yellow);">Players</h3>
        <div id="hud"></div>
        <div style="text-align:center; margin-top:10px; padding:10px; border-radius:12px; background:#0a0e22; border:2px solid var(--yellow);">
          <div class="neon-txt-sm" style="color:var(--cyan);">Sand Timer</div>
          <div id="clockTxt" class="neon-txt" style="font-size:1.4rem; color:var(--pink);">90s</div>
        </div>
        <div id="camBox">
          <button type="button" class="btn btn-dark neon-txt-sm" style="width:100%; margin-bottom:8px;" onclick="toggleCam()">Toggle Camera</button>
          <video id="camVid" autoplay playsinline muted style="display:none;"></video>
        </div>
      </div>

      <div>
        <div id="status" class="status neon-txt-sm">Ready!</div>
        <div class="moving-border">
          <div id="board" class="board"></div>
        </div>
        <div class="rack-wrap">
          <div style="display:flex; justify-content:space-between; margin-bottom:8px;">
            <span id="rackOwner" class="neon-txt-sm" style="color:var(--yellow);">Your Tiles</span>
            <span id="bagTxt" class="neon-txt-sm" style="color:var(--lime);">Bag: 100</span>
          </div>
          <div id="rack" class="rack"></div>
          <div class="row-center" style="margin-bottom:0;">
            <button type="button" class="btn btn-lime neon-txt-sm" onclick="playTiles()">Play Word</button>
            <button type="button" class="btn btn-yellow neon-txt-sm" onclick="recallTiles()">Recall</button>
            <button type="button" class="btn btn-cyan neon-txt-sm" onclick="swapOne()">Swap</button>
            <button type="button" class="btn btn-pink neon-txt-sm" onclick="passNow()">Pass</button>
            <button type="button" class="btn btn-dark neon-txt-sm" onclick="backSetup()">Setup</button>
          </div>
        </div>
      </div>

      <div class="card">
        <h3 class="neon-txt-sm" style="margin:0 0 8px; color:var(--pink);">Moves</h3>
        <div id="log" class="log"></div>
      </div>
    </div>
  </div>

  <script>
    const PTS = {A:1,B:3,C:3,D:2,E:1,F:4,G:2,H:4,I:1,J:8,K:5,L:1,M:3,N:1,O:1,P:3,Q:10,R:1,S:1,T:1,U:1,V:4,W:4,X:8,Y:4,Z:10};
    const CNT = {A:9,B:2,C:2,D:4,E:12,F:2,G:3,H:2,I:9,J:1,K:1,L:4,M:2,N:6,O:8,P:2,Q:1,R:6,S:4,T:6,U:4,V:2,W:2,X:1,Y:2,Z:1};
    const COLORS = [
      ["#00f5ff","#0077ff","#ffe600"],
      ["#ff2e93","#b54cff","#ffe600"],
      ["#7cff2b","#00c878","#fff700"],
      ["#ffe600","#ff7b00","#00f5ff"],
      ["#b54cff","#5b2cff","#7cff2b"]
    ];
    const BONUS = {
      "0,0":"TW","0,7":"TW","0,14":"TW","7,0":"TW","7,14":"TW","14,0":"TW","14,7":"TW","14,14":"TW",
      "1,1":"DW","2,2":"DW","3,3":"DW","4,4":"DW","1,13":"DW","2,12":"DW","3,11":"DW","4,10":"DW",
      "13,1":"DW","12,2":"DW","11,3":"DW","10,4":"DW","13,13":"DW","12,12":"DW","11,11":"DW","10,10":"DW",
      "1,5":"TL","1,9":"TL","5,1":"TL","5,5":"TL","5,9":"TL","5,13":"TL",
      "9,1":"TL","9,5":"TL","9,9":"TL","9,13":"TL","13,5":"TL","13,9":"TL",
      "0,3":"DL","0,11":"DL","2,6":"DL","2,8":"DL","3,0":"DL","3,7":"DL","3,14":"DL",
      "6,2":"DL","6,6":"DL","6,8":"DL","6,12":"DL","7,3":"DL","7,11":"DL",
      "8,2":"DL","8,6":"DL","8,8":"DL","8,12":"DL","11,0":"DL","11,7":"DL","11,14":"DL",
      "12,6":"DL","12,8":"DL","14,3":"DL","14,11":"DL","7,7":"STAR"
    };

    const S = {
      mode: "local",
      nPlayers: 2,
      players: [],
      turn: 0,
      grid: [],
      placed: [],
      pick: null,
      bag: [],
      starUsed: false,
      sec: 90,
      left: 90,
      timer: null,
      stream: null
    };

    function chooseMode(m) {
      S.mode = m;
      document.getElementById("onlineBox").style.display = m === "online" ? "block" : "none";
      document.getElementById("camBox").style.display = m === "online" ? "block" : "none";
    }

    function genCode() {
      const c = "NEON-" + Math.random().toString(36).slice(2, 7).toUpperCase();
      document.getElementById("codeBox").value = c;
      document.getElementById("codeShow").textContent = "Code: " + c;
    }

    function makeCards() {
      S.nPlayers = parseInt(document.getElementById("pNum").value, 10);
      const wrap = document.getElementById("cards");
      wrap.innerHTML = "";
      for (let i = 0; i < S.nPlayers; i++) {
        if (!S.players[i]) S.players[i] = { name: "Player " + (i + 1), score: 0, rack: [], photo: "" };
        const d = document.createElement("div");
        d.className = "p-box";
        d.innerHTML = `
          <div class="av-circle" id="prev_${i}">${S.players[i].photo ? `<img src="${S.players[i].photo}">` : "😎"}</div>
          <input type="text" id="nm_${i}" value="${S.players[i].name}">
          <label class="btn btn-dark neon-txt-sm" style="padding:6px 10px; font-size:0.8rem;">
            Gallery Photo
            <input type="file" accept="image/*" style="display:none;" onchange="setPic(event,${i})">
          </label>
        `;
        wrap.appendChild(d);
      }
    }

    function setPic(ev, i) {
      const file = ev.target.files[0];
      if (!file) return;
      const fr = new FileReader();
      fr.onload = () => {
        S.players[i].photo = fr.result;
        document.getElementById("prev_" + i).innerHTML = `<img src="${fr.result}">`;
      };
      fr.readAsDataURL(file);
    }

    function newBag() {
      const b = [];
      for (const [k, v] of Object.entries(CNT)) {
        for (let i = 0; i < v; i++) b.push({ ch: k, pt: PTS[k] });
      }
      return b.sort(() => Math.random() - 0.5);
    }

    function drawFromBag(n) {
      const r = [];
      while (r.length < n && S.bag.length) r.push(S.bag.pop());
      return r;
    }

    function startNow() {
      S.nPlayers = parseInt(document.getElementById("pNum").value, 10);
      S.sec = parseInt(document.getElementById("tSec").value, 10);
      S.players = [];
      for (let i = 0; i < S.nPlayers; i++) {
        const inp = document.getElementById("nm_" + i);
        const prev = document.getElementById("prev_" + i)?.querySelector("img")?.src || "";
        S.players.push({
          name: (inp && inp.value.trim()) || ("Player " + (i + 1)),
          score: 0,
          rack: [],
          photo: prev
        });
      }
      S.grid = Array.from({ length: 15 }, () => Array(15).fill(null));
      S.placed = [];
      S.pick = null;
      S.bag = newBag();
      S.starUsed = false;
      S.turn = 0;
      for (let i = 0; i < S.nPlayers; i++) S.players[i].rack = drawFromBag(7);

      document.getElementById("setupScreen").style.display = "none";
      document.getElementById("playArea").style.display = "grid";
      document.getElementById("log").innerHTML = "";
      addLog("Game started! First word covers ★.");
      renderGame();
      startTimer();
    }

    function backSetup() {
      clearInterval(S.timer);
      document.getElementById("playArea").style.display = "none";
      document.getElementById("setupScreen").style.display = "block";
    }

    function tileNode(t, picked) {
      const c = COLORS[Math.max(0, (t.ch.charCodeAt(0) - 65) % COLORS.length)];
      const el = document.createElement("div");
      el.className = "tile" + (picked ? " picked" : "");
      el.style.background = `linear-gradient(145deg, ${c[0]}, ${c[1]})`;
      el.style.color = c[2];
      el.innerHTML = `<span class="t-ch neon-txt-sm">${t.ch}</span><span class="t-pt neon-txt-sm">${t.pt}</span>`;
      return el;
    }

    function renderGame() {
      const p = S.players[S.turn];
      document.getElementById("status").textContent = "Turn: " + p.name;
      document.getElementById("rackOwner").textContent = p.name + "'s Tiles";
      document.getElementById("bagTxt").textContent = "Bag: " + S.bag.length;

      const hud = document.getElementById("hud");
      hud.innerHTML = "";
      S.players.forEach((pl, idx) => {
        const div = document.createElement("div");
        div.className = "p-item" + (idx === S.turn ? " now" : "");
        div.innerHTML = `
          <div class="p-av">${pl.photo ? `<img src="${pl.photo}">` : "😎"}</div>
          <div>
            <div class="neon-txt-sm" style="color:var(--yellow);">${pl.name}</div>
            <div class="neon-txt-sm" style="color:var(--lime);">${pl.score} pts</div>
          </div>
        `;
        hud.appendChild(div);
      });

      const bEl = document.getElementById("board");
      bEl.innerHTML = "";
      for (let r = 0; r < 15; r++) {
        for (let c = 0; c < 15; c++) {
          const b = BONUS[r + "," + c] || "";
          const cell = document.createElement("div");
          cell.className = "cell " + (b ? "c-" + b : "c-0");
          const pl = S.placed.find(x => x.r === r && x.c === c);
          const lk = S.grid[r][c];
          if (lk) cell.appendChild(tileNode(lk, false));
          else if (pl) {
            const tn = tileNode(pl.tile, true);
            tn.onclick = (e) => { e.stopPropagation(); S.placed = S.placed.filter(x => !(x.r === r && x.c === c)); renderGame(); };
            cell.appendChild(tn);
          } else cell.innerHTML = `<span class="neon-txt-sm">${b === "STAR" ? "★" : b}</span>`;

          cell.onclick = () => {
            if (!lk && !pl && S.pick !== null) {
              const t = p.rack[S.pick];
              if (t) {
                S.placed.push({ r, c, tile: t, idx: S.pick });
                S.pick = null;
                renderGame();
              }
            }
          };
          bEl.appendChild(cell);
        }
      }

      const rk = document.getElementById("rack");
      rk.innerHTML = "";
      const used = new Set(S.placed.map(x => x.idx));
      for (let i = 0; i < 7; i++) {
        const slot = document.createElement("div");
        slot.className = "slot";
        const t = p.rack[i];
        if (t && !used.has(i)) {
          const tn = tileNode(t, S.pick === i);
          tn.onclick = () => { S.pick = S.pick === i ? null : i; renderGame(); };
          slot.appendChild(tn);
        }
        rk.appendChild(slot);
      }
    }

    function recallTiles() { S.placed = []; S.pick = null; renderGame(); }

    function playTiles() {
      if (!S.placed.length) return;
      if (!S.starUsed && !S.placed.some(x => x.r === 7 && x.c === 7)) {
        document.getElementById("status").textContent = "First word must touch ★!";
        return;
      }
      const p = S.players[S.turn];
      let pts = 0;
      const used = new Set(S.placed.map(x => x.idx));
      const word = S.placed.map(x => {
        let lp = x.tile.pt;
        const b = BONUS[x.r + "," + x.c];
        if (b === "DL") lp *= 2;
        if (b === "TL") lp *= 3;
        pts += lp;
        S.grid[x.r][x.c] = x.tile;
        return x.tile.ch;
      }).join("");
      p.score += pts;
      p.rack = p.rack.filter((_, i) => !used.has(i));
      p.rack.push(...drawFromBag(7 - p.rack.length));
      S.placed = [];
      S.pick = null;
      S.starUsed = true;
      addLog(`${p.name} played ${word} (+${pts})`);
      nextTurn();
    }

    function swapOne() {
      if (!S.bag.length || S.pick === null) return;
      const p = S.players[S.turn];
      const old = p.rack[S.pick];
      if (!old) return;
      S.placed = [];
      S.bag.push(old);
      S.bag.sort(() => Math.random() - 0.5);
      p.rack[S.pick] = drawFromBag(1)[0] || null;
      p.rack = p.rack.filter(Boolean);
      S.pick = null;
      addLog(`${p.name} swapped a tile.`);
      nextTurn();
    }

    function passNow() {
      S.placed = [];
      S.pick = null;
      addLog(`${S.players[S.turn].name} passed.`);
      nextTurn();
    }

    function nextTurn() {
      S.turn = (S.turn + 1) % S.nPlayers;
      renderGame();
      startTimer();
    }

    function startTimer() {
      clearInterval(S.timer);
      if (S.sec <= 0) { document.getElementById("clockTxt").textContent = "∞"; return; }
      S.left = S.sec;
      document.getElementById("clockTxt").textContent = S.left + "s";
      S.timer = setInterval(() => {
        S.left--;
        document.getElementById("clockTxt").textContent = S.left + "s";
        if (S.left <= 0) {
          clearInterval(S.timer);
          nextTurn();
        }
      }, 1000);
    }

    async function toggleCam() {
      const vid = document.getElementById("camVid");
      if (!S.stream) {
        try {
          S.stream = await navigator.mediaDevices.getUserMedia({ video: true, audio: false });
          vid.srcObject = S.stream;
          vid.style.display = "block";
        } catch {
          document.getElementById("status").textContent = "Camera unavailable.";
        }
      } else {
        S.stream.getTracks().forEach(t => t.stop());
        S.stream = null;
        vid.style.display = "none";
      }
    }

    function addLog(msg) {
      const l = document.getElementById("log");
      const d = document.createElement("div");
      d.className = "neon-txt-sm";
      d.textContent = msg;
      l.prepend(d);
    }

    makeCards();
  </script>
</body>
</html>
