<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Scrabble Neon Arcade</title>
  <script src="https://unpkg.com/peerjs@1.5.4/dist/peerjs.min.js"></script>
  <style>
    :root {
      --bg: #080b18;
      --panel: #111733;
      --cyan: #00f5ff;
      --pink: #ff2e93;
      --lime: #7cff2b;
      --yellow: #ffe600;
      --purple: #b54cff;
      --orange: #ff7b00;
    }

    * {
      box-sizing: border-box;
      user-select: none;
      -webkit-tap-highlight-color: transparent;
    }

    body {
      margin: 0;
      min-height: 100vh;
      font-family: "Comic Sans MS", "Chalkboard SE", "Comic Neue", cursive, sans-serif;
      background:
        radial-gradient(circle at 15% 15%, rgba(0, 245, 255, 0.15), transparent 36%),
        radial-gradient(circle at 85% 20%, rgba(255, 46, 147, 0.15), transparent 36%),
        var(--bg);
      color: var(--cyan);
      padding: 16px;
    }

    .neon-txt {
      paint-order: stroke fill;
      -webkit-text-stroke: 2px #000000;
      text-shadow:
        2px 2px 0 #000000,
        -1px -1px 0 #000000,
        1px -1px 0 #000000,
        -1px 1px 0 #000000,
        0 0 10px currentColor;
    }

    .neon-txt-sm {
      paint-order: stroke fill;
      -webkit-text-stroke: 1.3px #000000;
      text-shadow:
        1.5px 1.5px 0 #000000,
        -1px -1px 0 #000000,
        1px -1px 0 #000000,
        -1px 1px 0 #000000,
        0 0 7px currentColor;
    }

    .wrap {
      max-width: 1260px;
      margin: 0 auto;
    }

    .top-head {
      text-align: center;
      margin-bottom: 18px;
    }

    .pill-bar {
      display: inline-flex;
      flex-wrap: wrap;
      gap: 8px;
      justify-content: center;
      padding: 7px 16px;
      border-radius: 999px;
      background: #1d143d;
      border: 2px solid var(--purple);
      color: var(--pink);
      font-size: 0.88rem;
      font-weight: bold;
      margin-bottom: 8px;
    }

    .top-head h1 {
      margin: 0;
      font-size: clamp(2rem, 5vw, 3.3rem);
      color: var(--yellow);
    }

    .top-head p {
      margin: 6px 0 0;
      color: var(--cyan);
      font-size: 1rem;
    }

    .card {
      background: var(--panel);
      border: 2px solid rgba(0, 245, 255, 0.45);
      border-radius: 22px;
      padding: 18px;
      box-shadow: 0 0 24px rgba(0, 245, 255, 0.14);
    }

    .toolbar {
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
      justify-content: center;
      align-items: center;
      margin-bottom: 16px;
    }

    .btn {
      font-family: inherit;
      font-weight: bold;
      border-radius: 14px;
      border: 2px solid #000000;
      padding: 10px 18px;
      font-size: 0.98rem;
      cursor: pointer;
      transition: transform 0.12s ease;
    }

    .btn:hover { transform: translateY(-2px); }
    .btn:active { transform: translateY(1px); }

    .btn-cyan { background: linear-gradient(135deg, #00f5ff, #0088ff); color: var(--yellow); }
    .btn-pink { background: linear-gradient(135deg, #ff2e93, #b54cff); color: var(--yellow); }
    .btn-lime { background: linear-gradient(135deg, #7cff2b, #00c878); color: var(--yellow); }
    .btn-yellow { background: linear-gradient(135deg, #ffe600, #ff7b00); color: var(--cyan); }
    .btn-dark { background: #192147; color: var(--cyan); border-color: var(--cyan); }

    select, input[type="text"] {
      font-family: inherit;
      font-weight: bold;
      background: #0b0f24;
      color: var(--yellow);
      border: 2px solid var(--cyan);
      border-radius: 12px;
      padding: 8px 12px;
      outline: none;
    }

    .setup-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(210px, 1fr));
      gap: 12px;
    }

    .p-setup {
      background: #0b0f24;
      border: 2px solid var(--purple);
      border-radius: 18px;
      padding: 14px;
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 10px;
    }

    .avatar-pic {
      width: 68px;
      height: 68px;
      border-radius: 50%;
      border: 3px solid var(--yellow);
      background: #1a2352;
      display: flex;
      align-items: center;
      justify-content: center;
      overflow: hidden;
      font-size: 1.8rem;
    }

    .avatar-pic img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }

    .online-box {
      display: none;
      margin-top: 14px;
      padding: 14px;
      border-radius: 16px;
      background: #0b0f24;
      border: 2px dashed var(--lime);
    }

    .online-box.open { display: block; }

    .online-row {
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
      align-items: center;
      margin-top: 10px;
    }

    .room-tag {
      padding: 8px 14px;
      border-radius: 12px;
      background: #171f47;
      border: 2px solid var(--pink);
      color: var(--lime);
      font-weight: bold;
    }

    /* Game Layout */
    .play-grid {
      display: none;
      grid-template-columns: 285px minmax(320px, 1fr) 250px;
      gap: 16px;
      align-items: start;
    }

    .play-grid.active { display: grid; }

    @media (max-width: 1050px) {
      .play-grid.active { grid-template-columns: 1fr; }
    }

    /* Moving Rainbow Border */
    .rainbow-frame {
      position: relative;
      padding: 12px;
      border-radius: 28px;
      overflow: hidden;
      background: #060814;
      box-shadow: 0 0 26px rgba(0, 245, 255, 0.25);
    }

    .rainbow-frame::before {
      content: "";
      position: absolute;
      inset: -70%;
      background: conic-gradient(
        from 0deg,
        #ff2e93, #ffe600, #7cff2b, #00f5ff, #b54cff, #ff7b00, #ff2e93
      );
      animation: spinFrame 5s linear infinite;
      z-index: 0;
    }

    .rainbow-frame::after {
      content: "";
      position: absolute;
      inset: 6px;
      border-radius: 23px;
      background: #090d20;
      z-index: 1;
    }

    @keyframes spinFrame {
      to { transform: rotate(360deg); }
    }

    .bulbs {
      position: relative;
      z-index: 2;
      display: flex;
      justify-content: space-between;
      padding: 0 8px 6px;
    }

    .bulbs.bot { padding: 6px 8px 0; }

    .bulb {
      width: 10px;
      height: 10px;
      border-radius: 50%;
      border: 1.5px solid #000;
      animation: pulseBulb 1s infinite alternate;
    }

    .bulb:nth-child(odd) { background: var(--yellow); box-shadow: 0 0 8px var(--yellow); }
    .bulb:nth-child(even) { background: var(--pink); box-shadow: 0 0 8px var(--pink); animation-delay: 0.5s; }

    @keyframes pulseBulb {
      0% { transform: scale(0.8); }
      100% { transform: scale(1.25); }
    }

    .board-15 {
      position: relative;
      z-index: 2;
      display: grid;
      grid-template-columns: repeat(15, 1fr);
      gap: 3px;
      aspect-ratio: 1 / 1;
      background: #050712;
      padding: 6px;
      border-radius: 16px;
    }

    .tile-cell {
      border-radius: 6px;
      border: 1.5px solid rgba(255, 255, 255, 0.14);
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: clamp(0.45rem, 1vw, 0.74rem);
      font-weight: bold;
      cursor: pointer;
      overflow: hidden;
    }

    .c-base { background: #121836; color: var(--cyan); }
    .c-dl { background: #0d3a63; color: var(--cyan); }
    .c-tl { background: #134631; color: var(--lime); }
    .c-dw { background: #5c1638; color: var(--pink); }
    .c-tw { background: #5e2307; color: var(--yellow); }
    .c-star { background: #4a1769; color: var(--yellow); }

    .neon-piece {
      width: 100%;
      height: 100%;
      border-radius: 9px;
      border: 2px solid #000000;
      display: flex;
      align-items: center;
      justify-content: center;
      position: relative;
      font-weight: bold;
      cursor: pointer;
      box-shadow: 0 3px 0 #000000;
      animation: popPiece 0.25s ease-out;
    }

    @keyframes popPiece {
      0% { transform: scale(0.5) rotate(-10deg); }
      100% { transform: scale(1) rotate(0deg); }
    }

    .neon-piece.sel {
      outline: 3px solid var(--yellow);
      transform: translateY(-4px) scale(1.06);
      z-index: 4;
    }

    .piece-ch { font-size: clamp(0.85rem, 1.8vw, 1.3rem); line-height: 1; }
    .piece-pt {
      position: absolute;
      right: 3px;
      bottom: 2px;
      font-size: clamp(0.45rem, 0.7vw, 0.65rem);
      line-height: 1;
      color: #ffffff;
    }

    .fly-piece {
      position: fixed;
      z-index: 9999;
      pointer-events: none;
      border-radius: 9px;
      border: 2px solid #000;
      display: flex;
      align-items: center;
      justify-content: center;
      font-weight: bold;
      transition: left 0.36s ease, top 0.36s ease, transform 0.36s ease;
    }

    .rack-panel {
      margin-top: 14px;
      padding: 14px;
      border-radius: 20px;
      background: #101633;
      border: 2px solid var(--pink);
    }

    .rack-slots {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      gap: 8px;
      min-height: 58px;
      margin-bottom: 12px;
    }

    .r-slot { width: 52px; height: 56px; }

    .action-btns {
      display: flex;
      flex-wrap: wrap;
      gap: 8px;
      justify-content: center;
    }

    .hud-list { display: flex; flex-direction: column; gap: 10px; }

    .hud-row {
      display: flex;
      align-items: center;
      gap: 10px;
      padding: 10px;
      border-radius: 16px;
      background: #0b0f24;
      border: 2px solid rgba(255, 255, 255, 0.14);
    }

    .hud-row.active {
      border-color: var(--lime);
      box-shadow: 0 0 14px rgba(124, 255, 43, 0.35);
    }

    .hud-av {
      width: 42px;
      height: 42px;
      border-radius: 50%;
      border: 2px solid var(--cyan);
      overflow: hidden;
      display: flex;
      align-items: center;
      justify-content: center;
      background: #1a2352;
      flex-shrink: 0;
      font-size: 1.2rem;
    }

    .hud-av img { width: 100%; height: 100%; object-fit: cover; }

    .sand-box {
      margin-top: 14px;
      padding: 12px;
      border-radius: 16px;
      background: #0b0f24;
      border: 2px solid var(--yellow);
      text-align: center;
    }

    .sand-drip {
      stroke: var(--yellow);
      stroke-width: 3;
      stroke-dasharray: 4 3;
      animation: dripAnim 0.35s linear infinite;
    }

    @keyframes dripAnim { to { stroke-dashoffset: -7; } }

    .cam-box {
      display: none;
      margin-top: 14px;
      padding: 12px;
      border-radius: 16px;
      background: #0b0f24;
      border: 2px solid var(--cyan);
    }

    .cam-box.show { display: block; }

    .cam-grid {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(105px, 1fr));
      gap: 8px;
      margin-top: 10px;
    }

    .cam-card {
      position: relative;
      border-radius: 12px;
      overflow: hidden;
      border: 2px solid var(--purple);
      background: #050712;
      aspect-ratio: 4 / 3;
    }

    .cam-card video {
      width: 100%;
      height: 100%;
      object-fit: cover;
      transform: scaleX(-1);
    }

    .cam-lbl {
      position: absolute;
      bottom: 4px;
      left: 4px;
      font-size: 0.72rem;
      font-weight: bold;
      color: var(--lime);
      background: rgba(0, 0, 0, 0.7);
      padding: 2px 6px;
      border-radius: 6px;
    }

    .banner {
      margin-bottom: 12px;
      padding: 10px 14px;
      border-radius: 14px;
      background: #12193b;
      border: 2px solid var(--cyan);
      color: var(--yellow);
      font-weight: bold;
      text-align: center;
    }

    .feed {
      max-height: 220px;
      overflow-y: auto;
      display: flex;
      flex-direction: column;
      gap: 6px;
      font-size: 0.85rem;
    }

    .feed-msg {
      padding: 6px 8px;
      border-radius: 10px;
      background: #0b0f24;
      border-left: 3px solid var(--pink);
      color: var(--cyan);
    }
  </style>
</head>
<body>
  <div class="wrap">
    <div class="top-head">
      <div class="pill-bar neon-txt-sm">
        <span>Neon Tiles + Black Outline</span>
        <span>•</span>
        <span>Sand Timer</span>
        <span>•</span>
        <span>2–4 Players + Online Cam</span>
      </div>
      <h1 class="neon-txt">Scrabble Neon Arcade</h1>
      <p class="neon-txt-sm">Colorful neon tiles, moving border, gallery avatars & optional camera!</p>
    </div>

    <!-- SETUP -->
    <div id="setupScreen" class="card">
      <div class="toolbar">
        <button class="btn btn-cyan neon-txt-sm" onclick="setMode('local')">Local Pass & Play</button>
        <button class="btn btn-pink neon-txt-sm" onclick="setMode('online')">Play Online (2–4P)</button>
        <label class="neon-txt-sm" style="color: var(--lime);">
          Players:
          <select id="pCount" onchange="renderSetup()">
            <option value="2" selected>2 Players</option>
            <option value="3">3 Players</option>
            <option value="4">4 Players</option>
          </select>
        </label>
        <label class="neon-txt-sm" style="color: var(--yellow);">
          Sand Timer:
          <select id="sandOpt">
            <option value="60">60s</option>
            <option value="90" selected>90s</option>
            <option value="120">120s</option>
            <option value="0">Off</option>
          </select>
        </label>
      </div>

      <div id="setupCards" class="setup-grid"></div>

      <div id="onlineBox" class="online-box">
        <div class="neon-txt-sm" style="color: var(--lime);">Online Room (PeerJS)</div>
        <div class="online-row">
          <button class="btn btn-lime neon-txt-sm" onclick="createRoom()">Create Room</button>
          <input id="roomInput" type="text" placeholder="Room code..." style="max-width: 190px;" />
          <button class="btn btn-cyan neon-txt-sm" onclick="joinRoom()">Join Room</button>
          <span id="roomStatus" class="room-tag neon-txt-sm">Not connected</span>
        </div>
      </div>

      <div style="text-align: center; margin-top: 18px;">
        <button id="startBtn" class="btn btn-yellow neon-txt" style="font-size: 1.15rem; padding: 12px 28px;" onclick="startGame()">
          Start Neon Scrabble!
        </button>
      </div>
    </div>

    <!-- PLAY SCREEN -->
    <div id="playScreen" class="play-grid">
      <div class="card">
        <h3 class="neon-txt-sm" style="margin: 0 0 10px; color: var(--yellow);">Players</h3>
        <div id="playerHud" class="hud-list"></div>

        <div class="sand-box">
          <div class="neon-txt-sm" style="color: var(--cyan);">Sand Timer</div>
          <div style="display: flex; align-items: center; justify-content: center; gap: 12px; margin-top: 8px;">
            <svg width="48" height="66" viewBox="0 0 48 66">
              <rect x="6" y="4" width="36" height="5" rx="2" fill="#ffe600" stroke="#000" stroke-width="2" />
              <rect x="6" y="57" width="36" height="5" rx="2" fill="#ffe600" stroke="#000" stroke-width="2" />
              <path d="M11 9 L37 9 L24 33 L37 57 L11 57 L24 33 Z" fill="rgba(0,245,255,0.12)" stroke="#00f5ff" stroke-width="2.5" />
              <polygon id="sandTop" points="15,13 33,13 24,31" fill="#ff2e93" />
              <line id="sandStream" x1="24" y1="32" x2="24" y2="55" class="sand-drip" />
              <polygon id="sandBot" points="24,45 33,55 15,55" fill="#7cff2b" />
            </svg>
            <div>
              <div id="sandClock" class="neon-txt" style="font-size: 1.4rem; color: var(--pink);">90s</div>
              <div style="font-size: 0.78rem; color: var(--lime);">Turn Time</div>
            </div>
          </div>
        </div>

        <div id="camBox" class="cam-box">
          <div style="display: flex; justify-content: space-between; align-items: center;">
            <span class="neon-txt-sm" style="color: var(--pink);">Online Cam</span>
            <button id="camBtn" class="btn btn-dark neon-txt-sm" style="padding: 6px 12px; font-size: 0.82rem;" onclick="toggleCam()">
              Cam: OFF
            </button>
          </div>
          <div id="camGrid" class="cam-grid"></div>
        </div>
      </div>

      <div>
        <div id="statusBanner" class="banner neon-txt-sm">Game Ready!</div>

        <div class="rainbow-frame">
          <div class="bulbs">
            <span class="bulb"></span><span class="bulb"></span><span class="bulb"></span><span class="bulb"></span>
            <span class="bulb"></span><span class="bulb"></span><span class="bulb"></span><span class="bulb"></span>
          </div>
          <div id="boardGrid" class="board-15"></div>
          <div class="bulbs bot">
            <span class="bulb"></span><span class="bulb"></span><span class="bulb"></span><span class="bulb"></span>
            <span class="bulb"></span><span class="bulb"></span><span class="bulb"></span><span class="bulb"></span>
          </div>
        </div>

        <div class="rack-panel">
          <div style="display: flex; justify-content: space-between; margin-bottom: 8px;">
            <span id="rackLabel" class="neon-txt-sm" style="color: var(--yellow);">Your Tiles</span>
            <span id="bagLabel" class="neon-txt-sm" style="color: var(--lime);">Bag: 100</span>
          </div>
          <div id="rackSlots" class="rack-slots"></div>
          <div class="action-btns">
            <button class="btn btn-lime neon-txt-sm" onclick="submitWord()">Play Word</button>
            <button class="btn btn-yellow neon-txt-sm" onclick="recallAll()">Recall</button>
            <button class="btn btn-cyan neon-txt-sm" onclick="swapOne()">Swap</button>
            <button class="btn btn-pink neon-txt-sm" onclick="passMove()">Pass</button>
            <button class="btn btn-dark neon-txt-sm" onclick="goSetup()">Setup</button>
          </div>
        </div>
      </div>

      <div class="card">
        <h3 class="neon-txt-sm" style="margin: 0 0 10px; color: var(--pink);">Move Log</h3>
        <div id="moveFeed" class="feed"></div>
      </div>
    </div>
  </div>

  <script>
    const SCORES = {
      A:1, B:3, C:3, D:2, E:1, F:4, G:2, H:4, I:1, J:8, K:5, L:1, M:3,
      N:1, O:1, P:3, Q:10, R:1, S:1, T:1, U:1, V:4, W:4, X:8, Y:4, Z:10
    };
    const DIST = {
      A:9, B:2, C:2, D:4, E:12, F:2, G:3, H:2, I:9, J:1, K:1, L:4, M:2,
      N:6, O:8, P:2, Q:1, R:6, S:4, T:6, U:4, V:2, W:2, X:1, Y:2, Z:1
    };
    const PALETTES = [
      { bg: "linear-gradient(145deg, #00f5ff, #0077ff)", fg: "#ffe600" },
      { bg: "linear-gradient(145deg, #ff2e93, #ff6ec7)", fg: "#ffe600" },
      { bg: "linear-gradient(145deg, #7cff2b, #00c878)", fg: "#fff700" },
      { bg: "linear-gradient(145deg, #ffe600, #ff7b00)", fg: "#00f5ff" },
      { bg: "linear-gradient(145deg, #b54cff, #6528ff)", fg: "#7cff2b" }
    ];
    const BONUSES = {
      "0,0":"TW","0,7":"TW","0,14":"TW","7,0":"TW","7,14":"TW","14,0":"TW","14,7":"TW","14,14":"TW",
      "1,1":"DW","2,2":"DW","3,3":"DW","4,4":"DW","1,13":"DW","2,12":"DW","3,11":"DW","4,10":"DW",
      "13,1":"DW","12,2":"DW","11,3":"DW","10,4":"DW","13,13":"DW","12,12":"DW","11,11":"DW","10,10":"DW",
      "1,5":"TL","1,9":"TL","5,1":"TL","5,5":"TL","5,9":"TL","5,13":"TL",
      "9,1":"TL","9,5":"TL","9,9":"TL","9,13":"TL","13,5":"TL","13,9":"TL",
      "0,3":"DL","0,11":"DL","2,6":"DL","2,8":"DL","3,0":"DL","3,7":"DL","3,14":"DL",
      "6,2":"DL","6,6":"DL","6,8":"DL","6,12":"DL","7,3":"DL","7,11":"DL",
      "8,2":"DL","8,6":"DL","8,8":"DL","8,12":"DL","11,0":"DL","11,7":"DL","11,14":"DL",
      "12,6":"DL","12,8":"DL","14,3":"DL","14,11":"DL",
      "7,7":"STAR"
    };

    const g = {
      mode: "local",
      count: 2,
      players: [],
      turn: 0,
      board: Array.from({ length: 15 }, () => Array(15).fill(null)),
      staged: [],
      selIdx: null,
      bag: [],
      hasStar: false,
      sandSec: 90,
      sandLeft: 90,
      sandId: null,
      peer: null,
      conns: [],
      seat: 0,
      stream: null,
      camOn: false
    };

    function palFor(ch) {
      return PALETTES[Math.max(0, (ch.charCodeAt(0) - 65) % PALETTES.length)];
    }

    function setMode(m) {
      g.mode = m;
      document.getElementById("onlineBox").classList.toggle("open", m === "online");
      document.getElementById("camBox").classList.toggle("show", m === "online");
      renderSetup();
    }

    function renderSetup() {
      const n = parseInt(document.getElementById("pCount").value, 10);
      g.count = n;
      const box = document.getElementById("setupCards");
      box.innerHTML = "";
      for (let i = 0; i < n; i++) {
        if (!g.players[i]) g.players[i] = { name: `Player ${i + 1}`, score: 0, rack: [], photo: null };
        const p = g.players[i];
        const d = document.createElement("div");
        d.className = "p-setup";
        d.innerHTML = `
          <div class="avatar-pic" id="av_${i}">${p.photo ? `<img src="${p.photo}" alt="Avatar" />` : "🤩"}</div>
          <input type="text" id="pName_${i}" value="${esc(p.name)}" maxlength="16" />
          <label class="btn btn-dark neon-txt-sm" style="padding: 6px 12px; font-size: 0.82rem;">
            Gallery Photo
            <input type="file" accept="image/*" style="display:none;" onchange="loadPhoto(event, ${i})" />
          </label>
        `;
        box.appendChild(d);
      }
    }

    function loadPhoto(ev, idx) {
      const f = ev.target.files[0];
      if (!f) return;
      const r = new FileReader();
      r.onload = (e) => {
        const img = new Image();
        img.onload = () => {
          const cv = document.createElement("canvas");
          cv.width = 120;
          cv.height = 120;
          cv.getContext("2d").drawImage(img, 0, 0, 120, 120);
          const url = cv.toDataURL("image/jpeg", 0.72);
          g.players[idx].photo = url;
          document.getElementById(`av_${idx}`).innerHTML = `<img src="${url}" alt="Avatar" />`;
          sendState();
        };
        img.src = e.target.result;
      };
      r.readAsDataURL(f);
    }

    function makeBag() {
      const arr = [];
      let n = 1;
      for (const [ch, cnt] of Object.entries(DIST)) {
        for (let i = 0; i < cnt; i++) arr.push({ id: `${n++}_${ch}`, letter: ch, points: SCORES[ch] });
      }
      for (let i = arr.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [arr[i], arr[j]] = [arr[j], arr[i]];
      }
      return arr;
    }

    function drawN(n) {
      const out = [];
      while (out.length < n && g.bag.length > 0) out.push(g.bag.pop());
      return out;
    }

    function startGame() {
      for (let i = 0; i < g.count; i++) {
        const el = document.getElementById(`pName_${i}`);
        if (el && el.value.trim()) g.players[i].name = el.value.trim();
        g.players[i].score = 0;
        g.players[i].rack = [];
      }
      g.players = g.players.slice(0, g.count);
      g.board = Array.from({ length: 15 }, () => Array(15).fill(null));
      g.staged = [];
      g.selIdx = null;
      g.bag = makeBag();
      g.hasStar = false;
      g.turn = 0;
      g.sandSec = parseInt(document.getElementById("sandOpt").value, 10);
      for (let i = 0; i < g.count; i++) g.players[i].rack = drawN(7);

      document.getElementById("setupScreen").style.display = "none";
      document.getElementById("playScreen").classList.add("active");
      document.getElementById("camBox").classList.toggle("show", g.mode === "online");
      document.getElementById("moveFeed").innerHTML = "";
      logMsg("Game started! First word covers ★.");
      drawAll();
      startSand();
      sendState();
    }

    function goSetup() {
      clearInterval(g.sandId);
      document.getElementById("playScreen").classList.remove("active");
      document.getElementById("setupScreen").style.display = "block";
    }

    function myTurn() {
      return g.mode === "local" || g.turn === g.seat;
    }

    function pieceEl(tile, opts = {}) {
      const p = palFor(tile.letter);
      const el = document.createElement("div");
      el.className = "neon-piece" + (opts.sel ? " sel" : "");
      el.style.background = p.bg;
      el.style.color = p.fg;
      el.innerHTML = `<span class="piece-ch neon-txt-sm">${tile.letter}</span><span class="piece-pt neon-txt-sm">${tile.points}</span>`;
      if (opts.drag) {
        el.draggable = true;
        el.addEventListener("dragstart", (e) => {
          g.selIdx = opts.idx;
          e.dataTransfer.setData("text/plain", String(opts.idx));
          drawRack();
        });
      }
      return el;
    }

    function drawAll() {
      drawHud();
      drawBoard();
      drawRack();
      document.getElementById("bagLabel").textContent = `Bag: ${g.bag.length}`;
      const cur = g.players[g.turn];
      if (cur) document.getElementById("statusBanner").textContent = `Turn: ${cur.name}`;
    }

    function drawHud() {
      const hud = document.getElementById("playerHud");
      hud.innerHTML = "";
      g.players.forEach((p, i) => {
        const row = document.createElement("div");
        row.className = "hud-row" + (i === g.turn ? " active" : "");
        const tag = g.mode === "online" && i === g.seat ? " (You)" : "";
        row.innerHTML = `
          <div class="hud-av">${p.photo ? `<img src="${p.photo}" alt="${esc(p.name)}" />` : "🤩"}</div>
          <div style="min-width:0; flex:1;">
            <div class="neon-txt-sm" style="color:var(--yellow); white-space:nowrap; overflow:hidden; text-overflow:ellipsis;">${esc(p.name)}${tag}</div>
            <div class="neon-txt-sm" style="color:var(--lime); font-size:0.9rem;">${p.score} pts</div>
          </div>
        `;
        hud.appendChild(row);
      });
    }

    function drawBoard() {
      const bEl = document.getElementById("boardGrid");
      bEl.innerHTML = "";
      for (let r = 0; r < 15; r++) {
        for (let c = 0; c < 15; c++) {
          const b = BONUSES[`${r},${c}`] || "";
          const cell = document.createElement("div");
          cell.className = "tile-cell " + (
            b === "TW" ? "c-tw" : b === "DW" ? "c-dw" : b === "TL" ? "c-tl" :
            b === "DL" ? "c-dl" : b === "STAR" ? "c-star" : "c-base"
          );
          const stg = g.staged.find(x => x.r === r && x.c === c);
          const locked = g.board[r][c];
          if (locked) {
            cell.appendChild(pieceEl(locked));
          } else if (stg) {
            const pe = pieceEl(stg.tile, { sel: true });
            pe.onclick = (e) => { e.stopPropagation(); removeStaged(r, c); };
            cell.appendChild(pe);
          } else {
            cell.innerHTML = `<span class="neon-txt-sm">${b === "STAR" ? "★" : b}</span>`;
          }
          cell.addEventListener("dragover", e => e.preventDefault());
          cell.addEventListener("drop", e => {
            e.preventDefault();
            const idx = parseInt(e.dataTransfer.getData("text/plain"), 10);
            if (!Number.isNaN(idx)) putTile(idx, r, c);
          });
          cell.addEventListener("click", () => {
            if (!locked && !stg && g.selIdx !== null) putTile(g.selIdx, r, c);
          });
          bEl.appendChild(cell);
        }
      }
    }

    function drawRack() {
      const slots = document.getElementById("rackSlots");
      slots.innerHTML = "";
      const vp = g.mode === "online" ? g.players[g.seat] : g.players[g.turn];
      if (!vp) return;
      document.getElementById("rackLabel").textContent = `${vp.name}'s Tiles` + (myTurn() ? " • Your Turn" : "");
      const used = new Set(g.staged.map(s => s.idx));
      for (let i = 0; i < 7; i++) {
        const s = document.createElement("div");
        s.className = "r-slot";
        const t = vp.rack[i];
        if (t && !used.has(i)) {
          const pe = pieceEl(t, { drag: myTurn(), idx: i, sel: g.selIdx === i });
          pe.onclick = () => {
            if (!myTurn()) return;
            g.selIdx = g.selIdx === i ? null : i;
            drawRack();
          };
          s.appendChild(pe);
        }
        slots.appendChild(s);
      }
    }

    function flyAnim(tile, fromR, toR) {
      const p = palFor(tile.letter);
      const f = document.createElement("div");
      f.className = "fly-piece";
      f.style.background = p.bg;
      f.style.color = p.fg;
      f.style.width = `${Math.max(34, toR.width)}px`;
      f.style.height = `${Math.max(34, toR.height)}px`;
      f.style.left = `${fromR.left + fromR.width / 2 - 18}px`;
      f.style.top = `${fromR.top}px`;
      f.innerHTML = `<span class="neon-txt-sm">${tile.letter}</span>`;
      document.body.appendChild(f);
      requestAnimationFrame(() => {
        f.style.left = `${toR.left}px`;
        f.style.top = `${toR.top}px`;
        f.style.transform = "scale(1.06)";
      });
      setTimeout(() => f.remove(), 380);
    }

    function putTile(idx, r, c) {
      if (!myTurn()) return;
      const p = g.players[g.turn];
      const t = p.rack[idx];
      if (!t || g.board[r][c] || g.staged.some(s => s.r === r && s.c === c)) return;
      const cells = document.querySelectorAll("#boardGrid .tile-cell");
      const tgt = cells[r * 15 + c];
      if (tgt) flyAnim(t, document.getElementById("rackSlots").getBoundingClientRect(), tgt.getBoundingClientRect());
      g.staged.push({ r, c, tile: t, idx });
      g.selIdx = null;
      drawBoard();
      drawRack();
    }

    function removeStaged(r, c) {
      if (!myTurn()) return;
      g.staged = g.staged.filter(s => !(s.r === r && s.c === c));
      drawBoard();
      drawRack();
    }

    function recallAll() {
      if (!myTurn()) return;
      g.staged = [];
      g.selIdx = null;
      drawBoard();
      drawRack();
    }

    function submitWord() {
      if (!myTurn()) return;
      if (!g.staged.length) {
        document.getElementById("statusBanner").textContent = "Place tiles on the board first!";
        return;
      }
      const sameR = g.staged.every(s => s.r === g.staged[0].r);
      const sameC = g.staged.every(s => s.c === g.staged[0].c);
      if (!sameR && !sameC) {
        document.getElementById("statusBanner").textContent = "Tiles must be in one row or column!";
        return;
      }
      if (!g.hasStar && !g.staged.some(s => s.r === 7 && s.c === 7)) {
        document.getElementById("statusBanner").textContent = "First word must cover ★!";
        return;
      }
      const tmp = g.board.map(row => row.slice());
      const keys = new Set(g.staged.map(s => `${s.r},${s.c}`));
      for (const s of g.staged) tmp[s.r][s.c] = s.tile;

      if (g.hasStar) {
        const touch = g.staged.some(({ r, c }) =>
          [[-1,0],[1,0],[0,-1],[0,1]].some(([dr, dc]) => {
            const nr = r + dr, nc = c + dc;
            return nr >= 0 && nr < 15 && nc >= 0 && nc < 15 && g.board[nr][nc];
          })
        );
        if (!touch) {
          document.getElementById("statusBanner").textContent = "Connect to an existing tile!";
          return;
        }
      }

      const words = findWords(tmp, keys);
      if (!words.length) {
        document.getElementById("statusBanner").textContent = "Form a word of 2+ letters!";
        return;
      }

      let pts = 0;
      const labels = [];
      for (const w of words) {
        let base = 0, mult = 1;
        for (const cell of w.cells) {
          let lp = cell.tile.points;
          if (keys.has(`${cell.r},${cell.c}`)) {
            const b = BONUSES[`${cell.r},${cell.c}`];
            if (b === "DL") lp *= 2;
            else if (b === "TL") lp *= 3;
            else if (b === "DW" || b === "STAR") mult *= 2;
            else if (b === "TW") mult *= 3;
          }
          base += lp;
        }
        const tot = base * mult;
        pts += tot;
        labels.push(`${w.word} (+${tot})`);
      }
      if (g.staged.length === 7) { pts += 50; labels.push("Bingo (+50)"); }

      const p = g.players[g.turn];
      const used = new Set(g.staged.map(s => s.idx));
      for (const s of g.staged) g.board[s.r][s.c] = s.tile;
      p.rack = p.rack.filter((_, i) => !used.has(i));
      p.rack.push(...drawN(7 - p.rack.length));
      p.score += pts;
      g.staged = [];
      g.selIdx = null;
      g.hasStar = true;
      logMsg(`${p.name}: ${labels.join(", ")} = +${pts}`);
      advanceTurn();
    }

    function findWords(grid, keys) {
      const out = [], seen = new Set();
      function scan(r, c, dr, dc) {
        let sr = r, sc = c;
        while (sr - dr >= 0 && sr - dr < 15 && sc - dc >= 0 && sc - dc < 15 && grid[sr - dr][sc - dc]) {
          sr -= dr; sc -= dc;
        }
        const cells = [];
        let cr = sr, cc = sc;
        while (cr >= 0 && cr < 15 && cc >= 0 && cc < 15 && grid[cr][cc]) {
          cells.push({ r: cr, c: cc, tile: grid[cr][cc] });
          cr += dr; cc += dc;
        }
        if (cells.length >= 2 && cells.some(x => keys.has(`${x.r},${x.c}`))) {
          const k = `${dr},${dc}:${cells[0].r},${cells[0].c}-${cells[cells.length-1].r},${cells[cells.length-1].c}`;
          if (!seen.has(k)) { seen.add(k); out.push({ word: cells.map(x => x.tile.letter).join(""), cells }); }
        }
      }
      for (const s of g.staged) { scan(s.r, s.c, 0, 1); scan(s.r, s.c, 1, 0); }
      return out;
    }

    function swapOne() {
      if (!myTurn() || !g.bag.length || g.selIdx === null) return;
      const p = g.players[g.turn];
      const old = p.rack[g.selIdx];
      if (!old) return;
      g.staged = [];
      g.bag.push(old);
      g.bag.sort(() => Math.random() - 0.5);
      p.rack[g.selIdx] = drawN(1)[0] || null;
      p.rack = p.rack.filter(Boolean);
      g.selIdx = null;
      logMsg(`${p.name} swapped a tile.`);
      advanceTurn();
    }

    function passMove() {
      if (!myTurn()) return;
      const p = g.players[g.turn];
      g.staged = [];
      g.selIdx = null;
      logMsg(`${p.name} passed.`);
      advanceTurn();
    }

    function advanceTurn() {
      g.turn = (g.turn + 1) % g.count;
      drawAll();
      startSand();
      sendState();
    }

    function startSand() {
      clearInterval(g.sandId);
      if (g.sandSec <= 0) { document.getElementById("sandClock").textContent = "∞"; return; }
      g.sandLeft = g.sandSec;
      drawSand();
      g.sandId = setInterval(() => {
        g.sandLeft--;
        drawSand();
        if (g.sandLeft <= 0) {
          clearInterval(g.sandId);
          if (myTurn()) { logMsg(`Time up for ${g.players[g.turn].name}!`); advanceTurn(); }
        }
      }, 1000);
    }

    function drawSand() {
      document.getElementById("sandClock").textContent = `${g.sandLeft}s`;
      const r = Math.max(0, Math.min(1, g.sandLeft / Math.max(1, g.sandSec)));
      const h = 18 * r, y = 31 - h, w = Math.max(1, 9 * r);
      document.getElementById("sandTop").setAttribute("points", `${24 - w},${y} ${24 + w},${y} 24,31`);
      document.getElementById("sandStream").style.opacity = g.sandLeft > 0 ? "1" : "0";
    }

    function logMsg(t) {
      const f = document.getElementById("moveFeed");
      const m = document.createElement("div");
      m.className = "feed-msg neon-txt-sm";
      m.textContent = t;
      f.prepend(m);
    }

    function esc(s) {
      return String(s).replace(/[&<>"']/g, c => ({ "&":"&","<":"<",">":">",'"':""","'":"'" }[c]));
    }

    /* PeerJS Room + Optional Cam */
    function createRoom() {
      const code = "NEON-" + Math.random().toString(36).substring(2, 7).toUpperCase();
      g.seat = 0;
      startPeer(code, true, null);
    }

    function joinRoom() {
      const code = document.getElementById("roomInput").value.trim().toUpperCase();
      if (code) startPeer(null, false, code);
    }

    function startPeer(id, isHost, hostId) {
      if (g.peer) g.peer.destroy();
      const st = document.getElementById("roomStatus");
      st.textContent = "Connecting...";
      const peer = isHost ? new Peer(id) : new Peer();
      g.peer = peer;

      peer.on("open", (oid) => {
        if (isHost) {
          st.textContent = `Room: ${oid}`;
        } else {
          const c = peer.connect(hostId);
          bindConn(c);
          st.textContent = `Joined: ${hostId}`;
          if (g.camOn && g.stream) peer.call(hostId, g.stream);
        }
      });

      peer.on("connection", (c) => {
        bindConn(c);
        const s = g.conns.length;
        g.count = Math.min(4, Math.max(g.count, s + 1));
        document.getElementById("pCount").value = String(g.count);
        renderSetup();
        c.send({ type: "SEAT", seat: s });
        sendState();
      });

      peer.on("call", (call) => {
        call.answer(g.camOn && g.stream ? g.stream : undefined);
        call.on("stream", (rs) => addCamTile(call.peer, rs, "Friend"));
      });

      peer.on("error", () => { st.textContent = "Retry room code"; });
    }

    function bindConn(c) {
      g.conns.push(c);
      c.on("data", (msg) => {
        if (!msg || !msg.type) return;
        if (msg.type === "SEAT") {
          g.seat = msg.seat;
        } else if (msg.type === "STATE") {
          const myS = g.seat;
          g.board = msg.board; g.players = msg.players; g.count = msg.players.length;
          g.turn = msg.turn; g.bag = msg.bag; g.hasStar = msg.hasStar; g.staged = [];
          g.seat = myS;
          document.getElementById("setupScreen").style.display = "none";
          document.getElementById("playScreen").classList.add("active");
          document.getElementById("camBox").classList.toggle("show", true);
          drawAll();
          startSand();
        }
      });
    }

    function sendState() {
      if (g.mode !== "online" || !g.conns.length) return;
      const pay = { type: "STATE", board: g.board, players: g.players, turn: g.turn, bag: g.bag, hasStar: g.hasStar };
      g.conns.forEach(c => { if (c.open) c.send(pay); });
    }

    async function toggleCam() {
      const btn = document.getElementById("camBtn");
      if (!g.camOn) {
        try {
          const s = await navigator.mediaDevices.getUserMedia({ video: true, audio: false });
          g.stream = s; g.camOn = true; btn.textContent = "Cam: ON";
          addCamTile("me", s, "You");
          if (g.peer) {
            g.conns.forEach(c => {
              const call = g.peer.call(c.peer, s);
              if (call) call.on("stream", (rs) => addCamTile(c.peer, rs, "Friend"));
            });
          }
        } catch {
          document.getElementById("statusBanner").textContent = "Camera blocked or unavailable.";
        }
      } else {
        if (g.stream) g.stream.getTracks().forEach(t => t.stop());
        g.stream = null; g.camOn = false; btn.textContent = "Cam: OFF";
        const m = document.getElementById("cam_me");
        if (m) m.remove();
      }
    }

    function addCamTile(id, stream, lbl) {
      const grid = document.getElementById("camGrid");
      let card = document.getElementById(`cam_${id}`);
      if (!card) {
        card = document.createElement("div");
        card.id = `cam_${id}`;
        card.className = "cam-card";
        card.innerHTML = `<video autoplay playsinline muted></video><span class="cam-lbl neon-txt-sm">${lbl}</span>`;
        grid.appendChild(card);
      }
      card.querySelector("video").srcObject = stream;
    }

    renderSetup();
  </script>
</body>
</html>
