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
      border-radius
