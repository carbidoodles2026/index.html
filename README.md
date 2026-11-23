<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>Neon Scrabble Pop</title>
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Fredoka:wght@600;700&display=swap" rel="stylesheet" />
  <script src="https://unpkg.com/peerjs@1.5.4/dist/peerjs.min.js"></script>
  <style>
    :root {
      --bg-night: #080a16;
      --card-bg: #111631;
      --neon-cyan: #00f5ff;
      --neon-pink: #ff2e93;
      --neon-lime: #7cff2b;
      --neon-yellow: #ffe600;
      --neon-purple: #b54cff;
      --neon-orange: #ff7b00;
    }

    * {
      box-sizing: border-box;
      user-select: none;
      -webkit-tap-highlight-color: transparent;
    }

    body {
      margin: 0;
      min-height: 100vh;
      font-family: "Fredoka", "Comic Sans MS", "Chalkboard SE", cursive, sans-serif;
      background:
        radial-gradient(circle at 12% 15%, rgba(0, 245, 255, 0.16), transparent 36%),
        radial-gradient(circle at 88% 18%, rgba(255, 46, 147, 0.16), transparent 36%),
        radial-gradient(circle at 50% 90%, rgba(124, 255, 43, 0.12), transparent 42%),
        var(--bg-night);
      color: var(--neon-cyan);
      padding: 16px;
    }

    .toon-text {
      paint-order: stroke fill;
      -webkit-text-stroke: 2px #000000;
      text-shadow:
        2px 2px 0px #000000,
        -1px -1px 0px #000000,
        1px -1px 0px #000000,
        -1px 1px 0px #000000,
        0 0 12px currentColor;
    }

    .toon-text-sm {
      paint-order: stroke fill;
      -webkit-text-stroke: 1.4px #000000;
      text-shadow:
        1.5px 1.5px 0px #000000,
        -1px -1px 0px #000000,
        1px -1px 0px #000000,
        -1px 1px 0px #000000,
        0 0 8px currentColor;
    }

    .container {
      max-width: 1280px;
      margin: 0 auto;
    }

    .header {
      text-align: center;
      margin-bottom: 18px;
    }

    .header h1 {
      margin: 0;
      font-size: clamp(2rem, 5vw, 3.4rem);
      color: var(--neon-yellow);
    }

    .header p {
      margin: 6px 0 0;
      color: var(--neon-pink);
      font-size: 1rem;
    }

    .box {
      background: rgba(17, 22, 49, 0.94);
      border: 2px solid rgba(0, 245, 255, 0.45);
      border-radius: 22px;
      padding: 18px;
      box-shadow: 0 0 24px rgba(0, 245, 255, 0.14);
    }

    .controls-top {
      display: flex;
      flex-wrap: wrap;
      gap: 12px;
      justify-content: center;
      align-items: center;
      margin-bottom: 16px;
    }

    .btn {
      font-family: inherit;
      font-weight: 700;
      border-radius: 14px;
      border: 2px solid #000000;
      padding: 10px 18px;
      font-size: 0.98rem;
      cursor: pointer;
      transition: transform 0.12s ease, filter 0.12s ease;
    }

    .btn:hover:not(:disabled) {
      transform: translateY(-2px);
      filter: brightness(1.08);
    }

    .btn:active:not(:disabled) {
      transform: translateY(1px);
    }

    .btn:disabled {
      opacity: 0.45;
      cursor: not-allowed;
    }

    .btn-cyan { background: linear-gradient(135deg, #00f5ff, #008cff); color: var(--neon-yellow); }
    .btn-pink { background: linear-gradient(135deg, #ff2e93, #b54cff); color: var(--neon-yellow); }
    .btn-lime { background: linear-gradient(135deg, #7cff2b, #00cf7c); color: var(--neon-yellow); }
    .btn-yellow { background: linear-gradient(135deg, #ffe600, #ff7b00); color: var(--neon-cyan); }
    .btn-dark { background: #182047; color: var(--neon-cyan); border-color: var(--neon-cyan); }

    select, input[type="text"] {
      font-family: inherit;
      font-weight: 700;
      background: #0b0f24;
      color: var(--neon-yellow);
      border: 2px solid var(--neon-cyan);
      border-radius: 12px;
      padding: 8px 12px;
      font-size: 0.95rem;
      outline: none;
    }

    .players-setup {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(210px, 1fr));
      gap: 14px;
      margin-top: 14px;
    }

    .setup-card {
      background: #0b0f24;
      border: 2px solid var(--neon-purple);
      border-radius: 18px;
      padding: 14px;
      display: flex;
      flex-direction: column;
      align-items: center;
      gap: 10px;
    }

    .avatar-ring {
      width: 68px;
      height: 68px;
      border-radius: 50%;
      border: 3px solid var(--neon-yellow);
      background: #1a224d;
      display: flex;
      align-items: center;
      justify-content: center;
      overflow: hidden;
      font-size: 1.8rem;
      box-shadow: 0 0 12px rgba(255, 230, 0, 0.45);
    }

    .avatar-ring img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }

    .online-panel {
      display: none;
      margin-top: 16px;
      padding: 14px;
      border-radius: 16px;
      background: #0b0f24;
      border: 2px dashed var(--neon-lime);
    }

    .online-panel.show {
      display: block;
    }

    .online-row {
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
      align-items: center;
      margin-top: 10px;
    }

    .room-badge {
      padding: 8px 14px;
      border-radius: 12px;
      background: #171e42;
      border: 2px solid var(--neon-pink);
      color: var(--neon-lime);
      font-weight: 700;
    }

    /* Game Stage */
    .stage {
      display: none;
      grid-template-columns: 290px minmax(320px, 1fr) 260px;
      gap: 18px;
      align-items: start;
    }

    .stage.active {
      display: grid;
    }

    @media (max-width: 1060px) {
      .stage.active {
        grid-template-columns: 1fr;
      }
    }

    /* Moving Vibrant Border */
    .moving-border-wrap {
      position: relative;
      padding: 12px;
      border-radius: 28px;
      overflow: hidden;
      background: #060813;
      box-shadow: 0 0 28px rgba(255, 46, 147, 0.25);
    }

    .moving-border-wrap::before {
      content: "";
      position: absolute;
      inset: -70%;
      background: conic-gradient(
        from 0deg,
        #ff2e93,
        #ffe600,
        #7cff2b,
        #00f5ff,
        #b54cff,
        #ff7b00,
        #ff2e93
      );
      animation: rotateNeon 5s linear infinite;
      z-index: 0;
    }

    .moving-border-wrap::after {
      content: "";
      position: absolute;
      inset: 6px;
      border-radius: 23px;
      background: #090d1f;
      z-index: 1;
    }

    @keyframes rotateNeon {
      to { transform: rotate(360deg); }
    }

    .border-lights {
      position: relative;
      z-index: 2;
      display: flex;
      justify-content: space-between;
      padding: 0 8px 6px;
    }

    .border-lights.bottom {
      padding: 6px 8px 0;
    }

    .light-pip {
      width: 10px;
      height: 10px;
      border-radius: 50%;
      border: 1.5px solid #000;
      animation: pipPulse 1s infinite alternate;
    }

    .light-pip:nth-child(odd) {
      background: var(--neon-yellow);
      box-shadow: 0 0 8px var(--neon-yellow);
    }

    .light-pip:nth-child(even) {
      background: var(--neon-cyan);
      box-shadow: 0 0 8px var(--neon-cyan);
      animation-delay: 0.5s;
    }

    @keyframes pipPulse {
      0% { transform: scale(0.8); opacity: 0.7; }
      100% { transform: scale(1.25); opacity: 1; }
    }

    .board-surface {
      position: relative;
      z-index: 2;
    }

    .grid-15 {
      display: grid;
      grid-template-columns: repeat(15, 1fr);
      gap: 3px;
      aspect-ratio: 1 / 1;
      background: #050711;
      padding: 6px;
      border-radius: 16px;
    }

    .sq {
      border-radius: 6px;
      border: 1.5px solid rgba(255, 255, 255, 0.14);
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: clamp(0.45rem, 1vw, 0.75rem);
      font-weight: 700;
      cursor: pointer;
      overflow: hidden;
      position: relative;
    }

    .sq:hover {
      border-color: var(--neon-yellow);
    }

    .sq-base { background: #121836; color: var(--neon-cyan); }
    .sq-dl { background: #0e3a63; color: var(--neon-cyan); }
    .sq-tl { background: #134631; color: var(--neon-lime); }
    .sq-dw { background: #5c1638; color: var(--neon-pink); }
    .sq-tw { background: #5e2307; color: var(--neon-yellow); }
    .sq-star { background: #4a1769; color: var(--neon-yellow); }

    .cute-tile {
      width: 100%;
      height: 100%;
      border-radius: 9px;
      border: 2px solid #000000;
      display: flex;
      align-items: center;
      justify-content: center;
      position: relative;
      font-weight: 700;
      cursor: pointer;
      box-shadow: 0 3px 0 #000000, 0 0 10px rgba(0, 245, 255, 0.35);
      animation: tilePopIn 0.28s cubic-bezier(0.22, 1.4, 0.36, 1);
    }

    @keyframes tilePopIn {
      0% { transform: scale(0.45) rotate(-12deg); }
      100% { transform: scale(1) rotate(0deg); }
    }

    .cute-tile.picked {
      outline: 3px solid var(--neon-yellow);
      transform: translateY(-4px) scale(1.06);
      z-index: 4;
    }

    .cute-letter {
      font-size: clamp(0.85rem, 1.8vw, 1.32rem);
      line-height: 1;
    }

    .cute-pts {
      position: absolute;
      right: 3px;
      bottom: 2px;
      font-size: clamp(0.45rem, 0.72vw, 0.65rem);
      line-height: 1;
      color: #ffffff;
    }

    /* Flying tile clone when moving from rack to board */
    .fly-ghost {
      position: fixed;
      z-index: 9999;
      pointer-events: none;
      border-radius: 9px;
      border: 2px solid #000;
      display: flex;
      align-items: center;
      justify-content: center;
      font-weight: 700;
      transition: left 0.38s cubic-bezier(0.22, 1, 0.36, 1),
                  top 0.38s cubic-bezier(0.22, 1, 0.36, 1),
                  transform 0.38s ease;
    }

    .rack-box {
      margin-top: 14px;
      padding: 14px;
      border-radius: 20px;
      background: #101530;
      border: 2px solid var(--neon-pink);
    }

    .rack-row {
      display: flex;
      flex-wrap: wrap;
      justify-content: center;
      gap: 8px;
      min-height: 60px;
      margin-bottom: 12px;
    }

    .rack-slot {
      width: 52px;
      height: 56px;
    }

    .btns-row {
      display: flex;
      flex-wrap: wrap;
      gap: 8px;
      justify-content: center;
    }

    .player-list {
      display: flex;
      flex-direction: column;
      gap: 10px;
    }

    .p-card {
      display: flex;
      align-items: center;
      gap: 10px;
      padding: 10px;
      border-radius: 16px;
      background: #0b0f24;
      border: 2px solid rgba(255, 255, 255, 0.14);
    }

    .p-card.turn-now {
      border-color: var(--neon-lime);
      box-shadow: 0 0 16px rgba(124, 255, 43, 0.4);
    }

    .p-thumb {
      width: 42px;
      height: 42px;
      border-radius: 50%;
      border: 2px solid var(--neon-cyan);
      overflow: hidden;
      display: flex;
      align-items: center;
      justify-content: center;
      background: #19214a;
      flex-shrink: 0;
      font-size: 1.2rem;
    }

    .p-thumb img {
      width: 100%;
      height: 100%;
      object-fit: cover;
    }

    /* Sand Timer */
    .sand-card {
      margin-top: 14px;
      padding: 14px;
      border-radius: 18px;
      background: #0b0f24;
      border: 2px solid var(--neon-yellow);
      text-align: center;
    }

    .sand-flex {
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 12px;
      margin-top: 8px;
    }

    .sand-stream {
      stroke: var(--neon-yellow);
      stroke-width: 3;
      stroke-dasharray: 4 3;
      animation: drip 0.35s linear infinite;
    }

    @keyframes drip {
      to { stroke-dashoffset: -7; }
    }

    .status-bar {
      margin-bottom: 12px;
      padding: 10px 14px;
      border-radius: 14px;
      background: #121838;
      border: 2px solid var(--neon-cyan);
      color: var(--neon-yellow);
      font-weight: 700;
      text-align: center;
    }

    .feed-list {
      max-height: 220px;
      overflow-y: auto;
      display: flex;
      flex-direction: column;
      gap: 6px;
      font-size: 0.86rem;
    }

    .feed-entry {
      padding: 6px 8px;
      border-radius: 10px;
      background: #0b0f24;
      border-left: 3px solid var(--neon-pink);
      color: var(--neon-cyan);
    }
  </style>
</head>
<body>
  <div class="container">
    <div class="header">
      <h1 class="toon-text">Neon Scrabble Pop</h1>
      <p class="toon-text-sm">Cute Neon Tiles • Moving Border • Sand Timer • 2–4 Players + Online</p>
    </div>

    <!-- SETUP -->
    <div id="setupBox" class="box">
      <div class="controls-top">
        <button class="btn btn-cyan toon-text-sm" onclick="chooseMode('local')">Local Play</button>
        <button class="btn btn-pink toon-text-sm" onclick="chooseMode('online')">Online Room</button>
        <label class="toon-text-sm" style="color: var(--neon-lime);">
          Players:
          <select id="numPlayers" onchange="buildSetupCards()">
            <option value="2" selected>2</option>
            <option value="3">3</option>
            <option value="4">4</option>
          </select>
        </label>
        <label class="toon-text-sm" style="color: var(--neon-yellow);">
          Sand Timer:
          <select id="sandSeconds">
            <option value="60">60s</option>
            <option value="90" selected>90s</option>
            <option value="120">120s</option>
            <option value="0">Off</option>
          </select>
        </label>
      </div>

      <div id="setupCards" class="players-setup"></div>

      <div id="onlinePanel" class="online-panel">
        <div class="toon-text-sm" style="color: var(--neon-lime);">Online Multiplayer (PeerJS)</div>
        <div class="online-row">
          <button class="btn btn-lime toon-text-sm" onclick="makeRoom()">Create Room</button>
          <input id="roomCodeInput" type="text" placeholder="Enter code..." style="max-width: 190px;" />
          <button class="btn btn-cyan toon-text-sm" onclick="enterRoom()">Join Room</button>
          <span id="roomStatus" class="room-badge toon-text-sm">Offline</span>
        </div>
      </div>

      <div style="text-align: center; margin-top: 18px;">
        <button class="btn btn-yellow toon-text" style="font-size: 1.15rem; padding: 12px 28px;" onclick="beginGame()">
          Start Game
        </button>
      </div>
    </div>

    <!-- GAME STAGE -->
    <div id="gameStage" class="stage">
      <div class="box">
        <h3 class="toon-text-sm" style="margin: 0 0 10px; color: var(--neon-yellow);">Players</h3>
        <div id="playersHud" class="player-list"></div>

        <div class="sand-card">
          <div class="toon-text-sm" style="color: var(--neon-cyan);">Sand Timer</div>
          <div class="sand-flex">
            <svg width="50" height="68" viewBox="0 0 50 68">
              <rect x="6" y="4" width="38" height="5" rx="2" fill="#ffe600" stroke="#000" stroke-width="2" />
              <rect x="6" y="59" width="38" height="5" rx="2" fill="#ffe600" stroke="#000" stroke-width="2" />
              <path d="M11 9 L39 9 L25 34 L39 59 L11 59 L25 34 Z" fill="rgba(0,245,255,0.12)" stroke="#00f5ff" stroke-width="2.5" />
              <polygon id="sandTop" points="15,13 35,13 25,32" fill="#ff2e93" />
              <line id="sandLine" x1="25" y1="33" x2="25" y2="57" class="sand-stream" />
              <polygon id="sandBottom" points="25,46 35,57 15,57" fill="#7cff2b" />
            </svg>
            <div>
              <div id="sandText" class="toon-text" style="font-size: 1.45rem; color: var(--neon-pink);">90s</div>
              <div style="font-size: 0.78rem; color: var(--neon-lime);">Turn Clock</div>
            </div>
          </div>
        </div>
      </div>

      <div>
        <div id="statusBar" class="status-bar toon-text-sm">Ready!</div>

        <div class="moving-border-wrap">
          <div class="border-lights">
            <span class="light-pip"></span><span class="light-pip"></span><span class="light-pip"></span><span class="light-pip"></span>
            <span class="light-pip"></span><span class="light-pip"></span><span class="light-pip"></span><span class="light-pip"></span>
          </div>
          <div class="board-surface">
            <div id="boardGrid" class="grid-15"></div>
          </div>
          <div class="border-lights bottom">
            <span class="light-pip"></span><span class="light-pip"></span><span class="light-pip"></span><span class="light-pip"></span>
            <span class="light-pip"></span><span class="light-pip"></span><span class="light-pip"></span><span class="light-pip"></span>
          </div>
        </div>

        <div class="rack-box">
          <div style="display: flex; justify-content: space-between; margin-bottom: 8px;">
            <span id="rackTitle" class="toon-text-sm" style="color: var(--neon-yellow);">Your Tiles</span>
            <span id="bagInfo" class="toon-text-sm" style="color: var(--neon-lime);">Bag: 100</span>
          </div>
          <div id="rackRow" class="rack-row"></div>
          <div class="btns-row">
            <button class="btn btn-lime toon-text-sm" onclick="playWord()">Submit Word</button>
            <button class="btn btn-yellow toon-text-sm" onclick="recallTiles()">Recall</button>
            <button class="btn btn-cyan toon-text-sm" onclick="swapTile()">Swap</button>
            <button class="btn btn-pink toon-text-sm" onclick="passTurn()">Pass</button>
            <button class="btn btn-dark toon-text-sm" onclick="openSetup()">Setup</button>
          </div>
        </div>
      </div>

      <div class="box">
        <h3 class="toon-text-sm" style="margin: 0 0 10px; color: var(--neon-pink);">Game Feed</h3>
        <div id="feedList" class="feed-list"></div>
      </div>
    </div>
  </div>

  <script>
    const PTS = {
      A:1, B:3, C:3, D:2, E:1, F:4, G:2, H:4, I:1, J:8, K:5, L:1, M:3,
      N:1, O:1, P:3, Q:10, R:1, S:1, T:1, U:1, V:4, W:4, X:8, Y:4, Z:10
    };

    const COUNTS = {
      A:9, B:2, C:2, D:4, E:12, F:2, G:3, H:2, I:9, J:1, K:1, L:4, M:2,
      N:6, O:8, P:2, Q:1, R:6, S:4, T:6, U:4, V:2, W:2, X:1, Y:2, Z:1
    };

    const TILE_STYLES = [
      { bg: "linear-gradient(145deg, #00f5ff, #0077ff)", fg: "#ffe600" },
      { bg: "linear-gradient(145deg, #ff2e93, #ff6ec7)", fg: "#ffe600" },
      { bg: "linear-gradient(145deg, #7cff2b, #00c978)", fg: "#fff700" },
      { bg: "linear-gradient(145deg, #ffe600, #ff7b00)", fg: "#00f5ff" },
      { bg: "linear-gradient(145deg, #b54cff, #6528ff)", fg: "#7cff2b" }
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
      "12,6":"DL","12,8":"DL","14,3":"DL","14,11":"DL",
      "7,7":"STAR"
    };

    const app = {
      mode: "local",
      playerCount: 2,
      players: [],
      turn: 0,
      grid: Array.from({ length: 15 }, () => Array(15).fill(null)),
      staged: [],
      pickedIdx: null,
      bag: [],
      startedCenter: false,
      sandMax: 90,
      sandLeft: 90,
      sandTimer: null,
      peer: null,
      conns: [],
      seat: 0
    };

    function styleForLetter(ch) {
      const i = Math.max(0, (ch.charCodeAt(0) - 65) % TILE_STYLES.length);
      return TILE_STYLES[i];
    }

    function chooseMode(m) {
      app.mode = m;
      document.getElementById("onlinePanel").classList.toggle("show", m === "online");
      buildSetupCards();
    }

    function buildSetupCards() {
      const n = parseInt(document.getElementById("numPlayers").value, 10);
      app.playerCount = n;
      const wrap = document.getElementById("setupCards");
      wrap.innerHTML = "";

      for (let i = 0; i < n; i++) {
        if (!app.players[i]) {
          app.players[i] = { name: `Player ${i + 1}`, score: 0, rack: [], photo: null };
        }
        const p = app.players[i];
        const card = document.createElement("div");
        card.className = "setup-card";
        card.innerHTML = `
          <div class="avatar-ring" id="prevAvatar_${i}">
            ${p.photo ? `<img src="${p.photo}" alt="Avatar" />` : "🤩"}
          </div>
          <input type="text" id="nameInp_${i}" value="${esc(p.name)}" maxlength="16" />
          <label class="btn btn-dark toon-text-sm" style="padding: 6px 12px; font-size: 0.82rem;">
            Gallery Photo
            <input type="file" accept="image/*" style="display:none;" onchange="pickGalleryPhoto(event, ${i})" />
          </label>
        `;
        wrap.appendChild(card);
      }
    }

    function pickGalleryPhoto(ev, idx) {
      const file = ev.target.files[0];
      if (!file) return;
      const reader = new FileReader();
      reader.onload = function(e) {
        const img = new Image();
        img.onload = function() {
          const cv = document.createElement("canvas");
          cv.width = 128;
          cv.height = 128;
          cv.getContext("2d").drawImage(img, 0, 0, 128, 128);
          const url = cv.toDataURL("image/jpeg", 0.75);
          app.players[idx].photo = url;
          document.getElementById(`prevAvatar_${idx}`).innerHTML = `<img src="${url}" alt="Avatar" />`;
          syncOnline();
        };
        img.src = e.target.result;
      };
      reader.readAsDataURL(file);
    }

    function freshBag() {
      const arr = [];
      let id = 1;
      for (const [ch, cnt] of Object.entries(COUNTS)) {
        for (let k = 0; k < cnt; k++) {
          arr.push({ id: `${id++}_${ch}`, letter: ch, points: PTS[ch] });
        }
      }
      for (let i = arr.length - 1; i > 0; i--) {
        const j = Math.floor(Math.random() * (i + 1));
        [arr[i], arr[j]] = [arr[j], arr[i]];
      }
      return arr;
    }

    function takeTiles(n) {
      const out = [];
      while (out.length < n && app.bag.length > 0) {
        out.push(app.bag.pop());
      }
      return out;
    }

    function beginGame() {
      for (let i = 0; i < app.playerCount; i++) {
        const inp = document.getElementById(`nameInp_${i}`);
        if (inp && inp.value.trim()) app.players[i].name = inp.value.trim();
        app.players[i].score = 0;
        app.players[i].rack = [];
      }
      app.players = app.players.slice(0, app.playerCount);
      app.grid = Array.from({ length: 15 }, () => Array(15).fill(null));
      app.staged = [];
      app.pickedIdx = null;
      app.bag = freshBag();
      app.startedCenter = false;
      app.turn = 0;
      app.sandMax = parseInt(document.getElementById("sandSeconds").value, 10);

      for (let i = 0; i < app.playerCount; i++) {
        app.players[i].rack = takeTiles(7);
      }

      document.getElementById("setupBox").style.display = "none";
      document.getElementById("gameStage").classList.add("active");
      document.getElementById("feedList").innerHTML = "";
      pushFeed("Match started! Place first word on ★.");
      drawUI();
      runSandTimer();
      syncOnline();
    }

    function openSetup() {
      clearInterval(app.sandTimer);
      document.getElementById("gameStage").classList.remove("active");
      document.getElementById("setupBox").style.display = "block";
    }

    function canAct() {
      return app.mode === "local" || app.turn === app.seat;
    }

    function makeTileNode(tile, opts = {}) {
      const st = styleForLetter(tile.letter);
      const node = document.createElement("div");
      node.className = "cute-tile" + (opts.picked ? " picked" : "");
      node.style.background = st.bg;
      node.style.color = st.fg;

      const ch = document.createElement("span");
      ch.className = "cute-letter toon-text-sm";
      ch.textContent = tile.letter;

      const pt = document.createElement("span");
      pt.className = "cute-pts toon-text-sm";
      pt.textContent = tile.points;

      node.appendChild(ch);
      node.appendChild(pt);

      if (opts.draggable) {
        node.draggable = true;
        node.addEventListener("dragstart", (e) => {
          app.pickedIdx = opts.idx;
          e.dataTransfer.setData("text/plain", String(opts.idx));
          drawRack();
        });
      }
      return node;
    }

    function drawUI() {
      drawPlayers();
      drawBoard();
      drawRack();
      document.getElementById("bagInfo").textContent = `Bag: ${app.bag.length}`;
      const cur = app.players[app.turn];
      if (cur) {
        document.getElementById("statusBar").textContent = `Turn: ${cur.name}`;
      }
    }

    function drawPlayers() {
      const box = document.getElementById("playersHud");
      box.innerHTML = "";
      app.players.forEach((p, i) => {
        const el = document.createElement("div");
        el.className = "p-card" + (i === app.turn ? " turn-now" : "");
        const tag = app.mode === "online" && i === app.seat ? " (You)" : "";
        el.innerHTML = `
          <div class="p-thumb">
            ${p.photo ? `<img src="${p.photo}" alt="${esc(p.name)}" />` : "🤩"}
          </div>
          <div style="min-width:0; flex:1;">
            <div class="toon-text-sm" style="color:var(--neon-yellow); white-space:nowrap; overflow:hidden; text-overflow:ellipsis;">${esc(p.name)}${tag}</div>
            <div class="toon-text-sm" style="color:var(--neon-lime); font-size:0.9rem;">${p.score} pts</div>
          </div>
        `;
        box.appendChild(el);
      });
    }

    function drawBoard() {
      const gridEl = document.getElementById("boardGrid");
      gridEl.innerHTML = "";
      for (let r = 0; r < 15; r++) {
        for (let c = 0; c < 15; c++) {
          const b = BONUS[`${r},${c}`] || "";
          const sq = document.createElement("div");
          sq.className = "sq " + (
            b === "TW" ? "sq-tw" :
            b === "DW" ? "sq-dw" :
            b === "TL" ? "sq-tl" :
            b === "DL" ? "sq-dl" :
            b === "STAR" ? "sq-star" : "sq-base"
          );

          const stg = app.staged.find(x => x.r === r && x.c === c);
          const locked = app.grid[r][c];

          if (locked) {
            sq.appendChild(makeTileNode(locked));
          } else if (stg) {
            const tNode = makeTileNode(stg.tile, { picked: true });
            tNode.onclick = (e) => {
              e.stopPropagation();
              unstageAt(r, c);
            };
            sq.appendChild(tNode);
          } else {
            const lbl = document.createElement("span");
            lbl.className = "toon-text-sm";
            lbl.textContent = b === "STAR" ? "★" : b;
            sq.appendChild(lbl);
          }

          sq.addEventListener("dragover", e => e.preventDefault());
          sq.addEventListener("drop", e => {
            e.preventDefault();
            const idx = parseInt(e.dataTransfer.getData("text/plain"), 10);
            if (!Number.isNaN(idx)) stageTile(idx, r, c);
          });
          sq.addEventListener("click", () => {
            if (!locked && !stg && app.pickedIdx !== null) {
              stageTile(app.pickedIdx, r, c);
            }
          });
          gridEl.appendChild(sq);
        }
      }
    }

    function drawRack() {
      const row = document.getElementById("rackRow");
      row.innerHTML = "";
      const viewP = app.mode === "online" ? app.players[app.seat] : app.players[app.turn];
      if (!viewP) return;

      document.getElementById("rackTitle").textContent =
        `${viewP.name}'s Tiles` + (canAct() ? " • Your Turn" : "");

      const used = new Set(app.staged.map(s => s.idx));
      for (let i = 0; i < 7; i++) {
        const slot = document.createElement("div");
        slot.className = "rack-slot";
        const tile = viewP.rack[i];
        if (tile && !used.has(i)) {
          const tEl = makeTileNode(tile, {
            draggable: canAct(),
            idx: i,
            picked: app.pickedIdx === i
          });
          tEl.onclick = () => {
            if (!canAct()) return;
            app.pickedIdx = app.pickedIdx === i ? null : i;
            drawRack();
          };
          slot.appendChild(tEl);
        }
        row.appendChild(slot);
      }
    }

    function flyTileVisual(tile, fromRect, toRect) {
      const st = styleForLetter(tile.letter);
      const g = document.createElement("div");
      g.className = "fly-ghost";
      g.style.background = st.bg;
      g.style.color = st.fg;
      g.style.width = `${Math.max(34, toRect.width)}px`;
      g.style.height = `${Math.max(34, toRect.height)}px`;
      g.style.left = `${fromRect.left + fromRect.width / 2 - 18}px`;
      g.style.top = `${fromRect.top}px`;
      g.innerHTML = `<span class="toon-text-sm">${tile.letter}</span>`;
      document.body.appendChild(g);

      requestAnimationFrame(() => {
        g.style.left = `${toRect.left}px`;
        g.style.top = `${toRect.top}px`;
        g.style.transform = "scale(1.06) rotate(6deg)";
      });
      setTimeout(() => g.remove(), 420);
    }

    function stageTile(idx, r, c) {
      if (!canAct()) return;
      const p = app.players[app.turn];
      const tile = p.rack[idx];
      if (!tile || app.grid[r][c]) return;
      if (app.staged.some(s => s.r === r && s.c === c)) return;

      const rackRect = document.getElementById("rackRow").getBoundingClientRect();
      const sqs = document.querySelectorAll("#boardGrid .sq");
      const targetSq = sqs[r * 15 + c];
      if (targetSq) {
        flyTileVisual(tile, rackRect, targetSq.getBoundingClientRect());
      }

      app.staged.push({ r, c, tile, idx });
      app.pickedIdx = null;
      drawBoard();
      drawRack();
    }

    function unstageAt(r, c) {
      if (!canAct()) return;
      app.staged = app.staged.filter(s => !(s.r === r && s.c === c));
      drawBoard();
      drawRack();
    }

    function recallTiles() {
      if (!canAct()) return;
      app.staged = [];
      app.pickedIdx = null;
      drawBoard();
      drawRack();
    }

    function playWord() {
      if (!canAct()) return;
      if (!app.staged.length) {
        document.getElementById("statusBar").textContent = "Put tiles on the board first!";
        return;
      }

      const rs = app.staged.map(s => s.r);
      const cs = app.staged.map(s => s.c);
      const sameR = rs.every(x => x === rs[0]);
      const sameC = cs.every(x => x === cs[0]);
      if (!sameR && !sameC) {
        document.getElementById("statusBar").textContent = "Keep tiles in one row or column!";
        return;
      }

      if (!app.startedCenter && !app.staged.some(s => s.r === 7 && s.c === 7)) {
        document.getElementById("statusBar").textContent = "First word must touch ★!";
        return;
      }

      const temp = app.grid.map(row => row.slice());
      const stagedKeys = new Set(app.staged.map(s => `${s.r},${s.c}`));
      for (const s of app.staged) temp[s.r][s.c] = s.tile;

      if (app.startedCenter) {
        const connected = app.staged.some(({ r, c }) =>
          [[-1,0],[1,0],[0,-1],[0,1]].some(([dr, dc]) => {
            const nr = r + dr, nc = c + dc;
            return nr >= 0 && nr < 15 && nc >= 0 && nc < 15 && app.grid[nr][nc];
          })
        );
        if (!connected) {
          document.getElementById("statusBar").textContent = "Connect to an existing tile!";
          return;
        }
      }

      const words = collectWords(temp, stagedKeys);
      if (!words.length) {
        document.getElementById("statusBar").textContent = "Make a word of 2+ letters!";
        return;
      }

      let gained = 0;
      const tags = [];
      for (const w of words) {
        let base = 0;
        let mult = 1;
        for (const cell of w.cells) {
          let lp = cell.tile.points;
          if (stagedKeys.has(`${cell.r},${cell.c}`)) {
            const b = BONUS[`${cell.r},${cell.c}`];
            if (b === "DL") lp *= 2;
            else if (b === "TL") lp *= 3;
            else if (b === "DW" || b === "STAR") mult *= 2;
            else if (b === "TW") mult *= 3;
          }
          base += lp;
        }
        const totalW = base * mult;
        gained += totalW;
        tags.push(`${w.word} (+${totalW})`);
      }

      if (app.staged.length === 7) {
        gained += 50;
        tags.push("Full Rack (+50)");
      }

      const p = app.players[app.turn];
      const usedSet = new Set(app.staged.map(s => s.idx));
      for (const s of app.staged) app.grid[s.r][s.c] = s.tile;
      p.rack = p.rack.filter((_, i) => !usedSet.has(i));
      p.rack.push(...takeTiles(7 - p.rack.length));
      p.score += gained;
      app.staged = [];
      app.pickedIdx = null;
      app.startedCenter = true;

      pushFeed(`${p.name}: ${tags.join(", ")} = +${gained}`);
      nextTurn();
    }

    function collectWords(grid, stagedKeys) {
      const out = [];
      const seen = new Set();

      function scan(r, c, dr, dc) {
        let sr = r, sc = c;
        while (sr - dr >= 0 && sr - dr < 15 && sc - dc >= 0 && sc - dc < 15 && grid[sr - dr][sc - dc]) {
          sr -= dr;
          sc -= dc;
        }
        const cells = [];
        let cr = sr, cc = sc;
        while (cr >= 0 && cr < 15 && cc >= 0 && cc < 15 && grid[cr][cc]) {
          cells.push({ r: cr, c: cc, tile: grid[cr][cc] });
          cr += dr;
          cc += dc;
        }
        if (cells.length >= 2 && cells.some(x => stagedKeys.has(`${x.r},${x.c}`))) {
          const key = `${dr},${dc}:${cells[0].r},${cells[0].c}-${cells[cells.length-1].r},${cells[cells.length-1].c}`;
          if (!seen.has(key)) {
            seen.add(key);
            out.push({ word: cells.map(x => x.tile.letter).join(""), cells });
          }
        }
      }

      for (const s of app.staged) {
        scan(s.r, s.c, 0, 1);
        scan(s.r, s.c, 1, 0);
      }
      return out;
    }

    function swapTile() {
      if (!canAct()) return;
      if (!app.bag.length) {
        document.getElementById("statusBar").textContent = "Bag is empty!";
        return;
      }
      if (app.pickedIdx === null) {
        document.getElementById("statusBar").textContent = "Tap a tile in your rack first!";
        return;
      }
      const p = app.players[app.turn];
      const old = p.rack[app.pickedIdx];
      if (!old) return;
      app.staged = [];
      app.bag.push(old);
      app.bag.sort(() => Math.random() - 0.5);
      p.rack[app.pickedIdx] = takeTiles(1)[0] || null;
      p.rack = p.rack.filter(Boolean);
      app.pickedIdx = null;
      pushFeed(`${p.name} swapped a tile.`);
      nextTurn();
    }

    function passTurn() {
      if (!canAct()) return;
      const p = app.players[app.turn];
      app.staged = [];
      app.pickedIdx = null;
      pushFeed(`${p.name} passed.`);
      nextTurn();
    }

    function nextTurn() {
      app.turn = (app.turn + 1) % app.playerCount;
      drawUI();
      runSandTimer();
      syncOnline();
    }

    function runSandTimer() {
      clearInterval(app.sandTimer);
      if (app.sandMax <= 0) {
        document.getElementById("sandText").textContent = "∞";
        return;
      }
      app.sandLeft = app.sandMax;
      renderSand();
      app.sandTimer = setInterval(() => {
        app.sandLeft--;
        renderSand();
        if (app.sandLeft <= 0) {
          clearInterval(app.sandTimer);
          if (canAct()) {
            pushFeed(`Time up for ${app.players[app.turn].name}!`);
            nextTurn();
          }
        }
      }, 1000);
    }

    function renderSand() {
      document.getElementById("sandText").textContent = `${app.sandLeft}s`;
      const ratio = Math.max(0, Math.min(1, app.sandLeft / Math.max(1, app.sandMax)));
      const h = 19 * ratio;
      const y = 32 - h;
      const w = Math.max(1, 10 * ratio);
      document.getElementById("sandTop").setAttribute("points", `${25 - w},${y} ${25 + w},${y} 25,32`);
      document.getElementById("sandLine").style.opacity = app.sandLeft > 0 ? "1" : "0";
    }

    function pushFeed(msg) {
      const list = document.getElementById("feedList");
      const item = document.createElement("div");
      item.className = "feed-entry toon-text-sm";
      item.textContent = msg;
      list.prepend(item);
    }

    function esc(s) {
      return String(s).replace(/[&<>"']/g, c => ({
        "&":"&","<":"<",">":">",'"':""","'":"'"
      }[c]));
    }

    /* PeerJS Online Room */
    function makeRoom() {
      const code = "POP-" + Math.random().toString(36).substring(2, 7).toUpperCase();
      app.seat = 0;
      initPeer(code, true, null);
    }

    function enterRoom() {
      const code = document.getElementById("roomCodeInput").value.trim().toUpperCase();
      if (!code) return;
      initPeer(null, false, code);
    }

    function initPeer(idToUse, isCreator, hostCode) {
      if (app.peer) app.peer.destroy();
      const badge = document.getElementById("roomStatus");
      badge.textContent = "Connecting...";
      const peer = isCreator ? new Peer(idToUse) : new Peer();
      app.peer = peer;

      peer.on("open", (openId) => {
        if (isCreator) {
          badge.textContent = `Room: ${openId}`;
        } else {
          const c = peer.connect(hostCode);
          hookConn(c);
          badge.textContent = `Joined: ${hostCode}`;
        }
      });

      peer.on("connection", (c) => {
        hookConn(c);
        const s = app.conns.length;
        app.playerCount = Math.min(4, Math.max(app.playerCount, s + 1));
        document.getElementById("numPlayers").value = String(app.playerCount);
        buildSetupCards();
        c.send({ type: "SEAT", seat: s });
        syncOnline();
      });

      peer.on("error", () => {
        badge.textContent = "Retry room code";
      });
    }

    function hookConn(c) {
      app.conns.push(c);
      c.on("data", (msg) => {
        if (!msg || !msg.type) return;
        if (msg.type === "SEAT") {
          app.seat = msg.seat;
        } else if (msg.type === "STATE") {
          const mySeat = app.seat;
          app.grid = msg.grid;
          app.players = msg.players;
          app.playerCount = msg.players.length;
          app.turn = msg.turn;
          app.bag = msg.bag;
          app.startedCenter = msg.startedCenter;
          app.staged = [];
          app.seat = mySeat;
          document.getElementById("setupBox").style.display = "none";
          document.getElementById("gameStage").classList.add("active");
          drawUI();
          runSandTimer();
        }
      });
    }

    function syncOnline() {
      if (app.mode !== "online" || !app.conns.length) return;
      const payload = {
        type: "STATE",
        grid: app.grid,
        players: app.players,
        turn: app.turn,
        bag: app.bag,
        startedCenter: app.startedCenter
      };
      app.conns.forEach(c => { if (c.open) c.send(payload); });
    }

    buildSetupCards();
  </script>
</body>
</html>
