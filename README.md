<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>Pro Cricket Broadcast Scoreboard (128:25)</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&family=Rajdhani:wght@600;700;800&display=swap" rel="stylesheet">
  <!-- PeerJS for Optional 2-Device PRISM Live Remote Sync -->
  <script src="https://cdn.jsdelivr.net/npm/peerjs@1.5.5/dist/peerjs.min.js"></script>

  <style>
    :root {
      --bg-dark: #070b14;
      --panel-bg: #111827;
      --border-subtle: rgba(255, 255, 255, 0.1);
      --text-main: #f8fafc;
      --text-muted: #94a3b8;
      --bat-accent: #00d2ff;
      --bowl-accent: #f43f5e;
      --gold: #facc15;
      --chroma-green: #00b140;
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      -webkit-tap-highlight-color: transparent;
    }

    html, body {
      min-height: 100vh;
    }

    body {
      font-family: 'Inter', -apple-system, BlinkMacSystemFont, sans-serif;
      background: radial-gradient(circle at top, #131c31 0%, var(--bg-dark) 75%);
      color: var(--text-main);
      display: flex;
      flex-direction: column;
      align-items: center;
      padding: 14px 12px 90px;
      transition: background 0.25s ease;
    }

    /* Background Modes */
    html.bg-transparent, body.bg-transparent {
      background: transparent !important;
    }

    html.bg-green, body.bg-green {
      background: var(--chroma-green) !important;
    }

    /* Overlay Mode (Exact 128:25 Edge-to-Edge for PRISM Live) */
    body.overlay-mode {
      padding: 0 !important;
      justify-content: flex-start;
    }
    body.overlay-mode.pos-bottom {
      justify-content: flex-end;
    }
    body.overlay-mode .top-toolbar,
    body.overlay-mode .remote-bar,
    body.overlay-mode .control-panel,
    body.overlay-mode .floating-show-btn {
      display: none !important;
    }

    /* Controls Hidden Mode */
    body.controls-hidden {
      padding: 8px;
    }
    body.controls-hidden .top-toolbar,
    body.controls-hidden .remote-bar,
    body.controls-hidden .control-panel {
      display: none !important;
    }
    body.controls-hidden.pos-bottom {
      justify-content: flex-end;
      padding-bottom: 12px;
    }

    .floating-show-btn {
      display: none;
      position: fixed;
      top: 10px;
      right: 10px;
      z-index: 999;
      background: rgba(15, 23, 42, 0.78);
      color: #e2e8f0;
      border: 1px solid rgba(255, 255, 255, 0.2);
      padding: 6px 12px;
      border-radius: 99px;
      font-size: 0.75rem;
      font-weight: 600;
      cursor: pointer;
      backdrop-filter: blur(6px);
    }
    body.controls-hidden .floating-show-btn {
      display: inline-flex;
      align-items: center;
      gap: 5px;
    }
    body.controls-hidden.pos-top .floating-show-btn {
      top: auto;
      bottom: 12px;
    }

    /* ================= TOP TOOLBAR ================= */
    .top-toolbar {
      width: 100%;
      max-width: 1280px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      gap: 8px;
      margin-bottom: 12px;
      flex-wrap: wrap;
    }

    .brand-title {
      font-family: 'Rajdhani', sans-serif;
      font-size: 1.25rem;
      font-weight: 800;
      letter-spacing: 0.8px;
      text-transform: uppercase;
      display: flex;
      align-items: center;
      gap: 8px;
    }

    .live-badge {
      background: #ef4444;
      color: #fff;
      font-size: 0.68rem;
      padding: 2px 7px;
      border-radius: 4px;
      letter-spacing: 1px;
    }

    .ratio-badge {
      background: rgba(56, 189, 248, 0.16);
      color: #38bdf8;
      border: 1px solid rgba(56, 189, 248, 0.35);
      font-size: 0.7rem;
      padding: 2px 7px;
      border-radius: 4px;
    }

    .toolbar-btns {
      display: flex;
      gap: 6px;
      flex-wrap: wrap;
    }

    .tool-btn {
      background: rgba(255, 255, 255, 0.08);
      color: #f1f5f9;
      border: 1px solid rgba(255, 255, 255, 0.16);
      padding: 7px 12px;
      border-radius: 8px;
      font-size: 0.78rem;
      font-weight: 600;
      cursor: pointer;
    }
    .tool-btn:hover { background: rgba(255, 255, 255, 0.15); }
    .tool-btn.active-green { background: #00b140; border-color: #00b140; color: #fff; }
    .tool-btn.active-trans { background: #0284c7; border-color: #0284c7; color: #fff; }

    /* ================= PRISM REMOTE SYNC BAR ================= */
    .remote-bar {
      width: 100%;
      max-width: 1280px;
      background: rgba(15, 23, 42, 0.85);
      border: 1px solid rgba(56, 189, 248, 0.25);
      border-radius: 10px;
      padding: 9px 12px;
      margin-bottom: 14px;
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 8px;
      flex-wrap: wrap;
    }

    .remote-left {
      display: flex;
      align-items: center;
      gap: 8px;
      flex-wrap: wrap;
    }

    .sync-pill {
      font-size: 0.74rem;
      font-weight: 700;
      padding: 3px 9px;
      border-radius: 99px;
      background: rgba(250, 204, 21, 0.15);
      color: #fde047;
      border: 1px solid rgba(250, 204, 21, 0.3);
    }
    .sync-pill.connected {
      background: rgba(16, 185, 129, 0.18);
      color: #34d399;
      border-color: rgba(16, 185, 129, 0.4);
    }

    .room-input-wrap {
      display: flex;
      align-items: center;
      gap: 5px;
      font-size: 0.78rem;
      color: var(--text-muted);
    }
    .room-input-wrap input {
      width: 68px;
      background: #090d16;
      border: 1px solid rgba(255, 255, 255, 0.16);
      color: #fff;
      border-radius: 6px;
      padding: 4px 6px;
      text-align: center;
      font-family: 'Rajdhani', sans-serif;
      font-weight: 700;
      font-size: 0.92rem;
    }

    .copy-prism-btn {
      background: linear-gradient(135deg, #0ea5e9 0%, #2563eb 100%);
      color: #fff;
      border: none;
      padding: 7px 12px;
      border-radius: 7px;
      font-size: 0.76rem;
      font-weight: 700;
      cursor: pointer;
    }

    /* ================= STRICT 128:25 BROADCAST OVERLAY STAGE ================= */
    .scoreboard-container {
      width: 100%;
      max-width: 1280px;
      margin-bottom: 16px;
    }

    body.overlay-mode .scoreboard-container,
    body.controls-hidden .scoreboard-container {
      margin-bottom: 0;
      max-width: none;
    }

    /* Fixed 128:25 Aspect Ratio Wrapper */
    .scoreboard-stage {
      width: 100%;
      aspect-ratio: 128 / 25;
      position: relative;
      overflow: hidden;
      border-radius: 10px;
    }

    /* Canonical 1280x250 Broadcast Canvas (Auto-scaled by JS) */
    .cricket-bug {
      width: 1280px;
      height: 250px;
      transform-origin: top left;
      position: absolute;
      top: 0;
      left: 0;
      display: flex;
      flex-direction: column;
      background: linear-gradient(180deg, #162238 0%, #080f1c 100%);
      border: 2px solid rgba(255, 255, 255, 0.22);
      border-radius: 16px;
      overflow: hidden;
      box-shadow: inset 0 2px 0 rgba(255, 255, 255, 0.16);
      transition: border-color 0.25s ease, box-shadow 0.25s ease;
    }

    .cricket-bug.glow-four {
      border-color: #38bdf8;
      box-shadow: inset 0 0 36px rgba(56, 189, 248, 0.55);
    }

    .cricket-bug.glow-six {
      border-color: #facc15;
      box-shadow: inset 0 0 42px rgba(250, 204, 21, 0.65);
    }

    /* ================= 4 & 6 BROADCAST CELEBRATION OVERLAY ================= */
    .event-overlay-banner {
      position: absolute;
      inset: 0;
      z-index: 40;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 32px;
      padding: 0 40px;
      pointer-events: none;
      opacity: 0;
      transform: translateY(105%);
      transition: transform 0.32s cubic-bezier(0.22, 1, 0.36, 1), opacity 0.22s ease;
      overflow: hidden;
    }

    .event-overlay-banner.active {
      opacity: 1;
      transform: translateY(0);
    }

    .event-overlay-banner.type-four {
      background: linear-gradient(110deg, #0369a1 0%, #0284c7 45%, #0ea5e9 70%, #075985 100%);
    }

    .event-overlay-banner.type-six {
      background: linear-gradient(110deg, #581c87 0%, #9333ea 45%, #db2777 75%, #ca8a04 100%);
    }

    .fx-shine {
      position: absolute;
      top: -20%;
      left: -40%;
      width: 35%;
      height: 140%;
      background: linear-gradient(90deg, transparent, rgba(255, 255, 255, 0.45), transparent);
      transform: skewX(-25deg);
      pointer-events: none;
    }

    .event-overlay-banner.active .fx-shine {
      animation: fxSweep 1.1s ease-out 0.12s forwards;
    }

    @keyframes fxSweep {
      0% { left: -45%; }
      100% { left: 135%; }
    }

    .fx-badge {
      width: 148px;
      height: 148px;
      border-radius: 50%;
      display: flex;
      align-items: center;
      justify-content: center;
      font-family: 'Rajdhani', sans-serif;
      font-size: 98px;
      font-weight: 800;
      line-height: 1;
      color: #ffffff;
      background: rgba(0, 0, 0, 0.34);
      border: 6px solid rgba(255, 255, 255, 0.9);
      box-shadow: 0 0 35px rgba(255, 255, 255, 0.5);
      flex-shrink: 0;
    }

    .type-six .fx-badge {
      border-color: #fde047;
      color: #fde047;
      box-shadow: 0 0 40px rgba(250, 204, 21, 0.8);
    }

    .event-overlay-banner.active .fx-badge {
      animation: fxBounce 0.55s cubic-bezier(0.34, 1.56, 0.64, 1);
    }

    @keyframes fxBounce {
      0% { transform: scale(0.2) rotate(-20deg); opacity: 0; }
      65% { transform: scale(1.16) rotate(5deg); opacity: 1; }
      100% { transform: scale(1) rotate(0deg); opacity: 1; }
    }

    .fx-text-group {
      display: flex;
      flex-direction: column;
      justify-content: center;
      text-align: left;
      z-index: 2;
    }

    .fx-title {
      font-family: 'Rajdhani', sans-serif;
      font-size: 92px;
      font-weight: 800;
      letter-spacing: 4px;
      text-transform: uppercase;
      line-height: 0.9;
      color: #ffffff;
      text-shadow: 0 5px 18px rgba(0, 0, 0, 0.5);
    }

    .type-six .fx-title {
      color: #fef08a;
    }

    .event-overlay-banner.active .fx-title {
      animation: fxTextSlide 0.4s ease-out;
    }

    @keyframes fxTextSlide {
      0% { transform: translateX(35px); opacity: 0; }
      100% { transform: translateX(0); opacity: 1; }
    }

    .fx-sub {
      font-size: 28px;
      font-weight: 700;
      letter-spacing: 3px;
      text-transform: uppercase;
      color: rgba(255, 255, 255, 0.94);
      margin-top: 6px;
    }

    .fx-sparkles {
      position: absolute;
      inset: 0;
      background-image:
        radial-gradient(circle, rgba(255, 255, 255, 0.85) 3px, transparent 3.5px),
        radial-gradient(circle, rgba(253, 224, 71, 0.85) 3px, transparent 3.5px);
      background-size: 72px 72px, 96px 96px;
      background-position: 0 0, 32px 24px;
      opacity: 0;
      pointer-events: none;
    }

    .event-overlay-banner.active .fx-sparkles {
      animation: fxSparklePulse 1.3s ease-out infinite;
    }

    @keyframes fxSparklePulse {
      0% { opacity: 0.08; transform: scale(0.98); }
      50% { opacity: 0.42; transform: scale(1.03); }
      100% { opacity: 0.08; transform: scale(0.98); }
    }

    /* ================= 1280x250 BROADCAST BAR ROWS ================= */
    .bug-main-row {
      flex: 1;
      display: flex;
      align-items: stretch;
      justify-content: space-between;
      min-height: 0;
    }

    .bug-score-section {
      display: flex;
      align-items: center;
      background: linear-gradient(90deg, rgba(0, 210, 255, 0.2) 0%, rgba(15, 23, 42, 0.45) 100%);
      border-left: 14px solid var(--bat-accent);
      padding: 0 28px;
      gap: 26px;
      flex: 1;
      min-width: 0;
    }

    .teams-pill {
      display: flex;
      align-items: baseline;
      gap: 10px;
      font-family: 'Rajdhani', sans-serif;
      font-weight: 800;
      text-transform: uppercase;
      white-space: nowrap;
    }

    .bat-team-name { font-size: 56px; color: #ffffff; letter-spacing: 1px; line-height: 1; }
    .vs-divider { font-size: 28px; color: var(--text-muted); font-weight: 700; }
    .bowl-team-name { font-size: 40px; color: #cbd5e1; opacity: 0.85; line-height: 1; }

    .main-score-box {
      background: linear-gradient(135deg, #00c6ff 0%, #0072ff 100%);
      color: #ffffff;
      padding: 8px 26px;
      border-radius: 14px;
      font-family: 'Rajdhani', sans-serif;
      font-size: 74px;
      font-weight: 800;
      line-height: 0.95;
      letter-spacing: 1px;
      box-shadow: 0 6px 20px rgba(0, 114, 255, 0.45);
      white-space: nowrap;
    }

    .overs-crr-box {
      display: flex;
      flex-direction: column;
      justify-content: center;
      line-height: 1.05;
    }

    .overs-text {
      font-family: 'Rajdhani', sans-serif;
      font-size: 46px;
      font-weight: 700;
      color: var(--gold);
      white-space: nowrap;
    }

    .overs-text small {
      font-size: 24px;
      color: var(--text-muted);
      font-weight: 600;
      margin-left: 6px;
    }

    .crr-text {
      font-size: 23px;
      font-weight: 700;
      color: #cbd5e1;
      letter-spacing: 0.6px;
      white-space: nowrap;
    }

    /* Right: This Over Tracker */
    .bug-over-section {
      display: flex;
      align-items: center;
      gap: 18px;
      padding: 0 28px;
      background: rgba(5, 9, 17, 0.78);
      border-left: 2px solid rgba(255, 255, 255, 0.12);
      flex-shrink: 0;
    }

    .this-over-label {
      font-size: 20px;
      font-weight: 700;
      text-transform: uppercase;
      letter-spacing: 1.2px;
      color: var(--text-muted);
      white-space: nowrap;
    }

    .balls-track {
      display: flex;
      align-items: center;
      gap: 8px;
      justify-content: flex-end;
      flex-wrap: nowrap;
    }

    .ball-dot {
      min-width: 44px;
      height: 44px;
      border-radius: 999px;
      display: inline-flex;
      align-items: center;
      justify-content: center;
      font-family: 'Rajdhani', sans-serif;
      font-size: 26px;
      font-weight: 800;
      background: #1e293b;
      color: #e2e8f0;
      border: 2px solid rgba(255, 255, 255, 0.18);
      flex-shrink: 0;
    }

    .ball-dot.empty {
      width: 44px;
      background: rgba(255, 255, 255, 0.04);
      border: 2px dashed rgba(255, 255, 255, 0.16);
      color: transparent;
    }

    .ball-dot.run-4 { background: #0284c7; color: #fff; border-color: #38bdf8; }
    .ball-dot.run-6 { background: #9333ea; color: #fff; border-color: #c084fc; box-shadow: 0 0 12px rgba(168, 85, 247, 0.55); }
    .ball-dot.wicket { background: #dc2626; color: #fff; border-color: #f87171; }
    .ball-dot.extra {
      background: #d97706;
      color: #fff;
      border-color: #fbbf24;
      font-size: 21px;
      padding: 0 11px;
    }
    .ball-dot.extra-boundary {
      background: linear-gradient(135deg, #d97706 0%, #0284c7 100%);
      border-color: #fde047;
    }
    .ball-dot.dot-ball { background: #334155; color: #cbd5e1; }

    /* ================= PLAYERS STRIP (BATSMEN & BOWLER) ================= */
    .bug-players-strip {
      height: 58px;
      flex-shrink: 0;
      display: flex;
      align-items: center;
      justify-content: space-between;
      background: rgba(8, 13, 24, 0.94);
      border-top: 2px solid rgba(255, 255, 255, 0.12);
      padding: 0 28px;
      gap: 20px;
      font-family: 'Rajdhani', sans-serif;
    }

    .bug-players-strip.hidden { display: none; }

    .batsmen-inline {
      display: flex;
      align-items: center;
      gap: 36px;
      min-width: 0;
    }

    .player-item {
      display: inline-flex;
      align-items: baseline;
      gap: 10px;
      font-size: 32px;
      font-weight: 700;
      text-transform: uppercase;
      color: #cbd5e1;
      white-space: nowrap;
    }

    .player-item.on-strike { color: #ffffff; }
    .strike-arrow { color: var(--gold); font-size: 24px; }
    .player-score { color: #38bdf8; font-weight: 800; }
    .player-item.on-strike .player-score { color: var(--gold); }
    .player-balls { font-size: 25px; color: var(--text-muted); font-weight: 600; }

    .bowler-inline {
      display: flex;
      align-items: baseline;
      gap: 12px;
      font-size: 32px;
      font-weight: 700;
      text-transform: uppercase;
      white-space: nowrap;
      border-left: 2px solid rgba(255, 255, 255, 0.14);
      padding-left: 26px;
    }

    .bowler-tag { font-size: 21px; color: #fb7185; letter-spacing: 1px; font-weight: 700; }
    .bowler-name-txt { color: #ffffff; }
    .bowler-fig { color: #fda4af; font-weight: 800; }

    /* Bottom Strip: Target Info */
    .bug-target-strip {
      height: 46px;
      flex-shrink: 0;
      display: none;
      background: linear-gradient(90deg, #0f172a 0%, #1e293b 50%, #0f172a 100%);
      border-top: 2px solid rgba(255, 255, 255, 0.12);
      padding: 0 28px;
      font-family: 'Rajdhani', sans-serif;
      font-size: 28px;
      font-weight: 700;
      letter-spacing: 1.2px;
      text-transform: uppercase;
      align-items: center;
      justify-content: space-between;
      gap: 16px;
      color: #f8fafc;
    }

    .bug-target-strip.visible { display: flex; }
    .target-highlight { color: var(--gold); }
    .won-highlight { color: #34d399; }

    /* ================= MOBILE / OPERATOR CONTROL PANEL ================= */
    .control-panel {
      width: 100%;
      max-width: 1280px;
      display: flex;
      flex-direction: column;
      gap: 12px;
    }

    .ctrl-card {
      background: var(--panel-bg);
      border: 1px solid var(--border-subtle);
      border-radius: 14px;
      padding: 14px;
      box-shadow: 0 8px 25px rgba(0, 0, 0, 0.35);
    }

    .ctrl-section-title {
      font-family: 'Rajdhani', sans-serif;
      font-size: 0.95rem;
      font-weight: 700;
      text-transform: uppercase;
      letter-spacing: 1px;
      color: var(--text-muted);
      margin-bottom: 10px;
      display: flex;
      justify-content: space-between;
      align-items: center;
      gap: 8px;
    }

    .runs-grid {
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 10px;
      margin-bottom: 10px;
    }

    .tap-btn {
      font-family: 'Rajdhani', sans-serif;
      font-size: 1.55rem;
      font-weight: 800;
      height: 58px;
      border: none;
      border-radius: 11px;
      color: #fff;
      cursor: pointer;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      line-height: 1;
      transition: transform 0.08s ease, filter 0.15s ease;
      user-select: none;
    }

    .tap-btn:active { transform: scale(0.94); }
    .tap-btn small {
      font-family: 'Inter', sans-serif;
      font-size: 0.62rem;
      font-weight: 600;
      opacity: 0.88;
      margin-top: 2px;
      text-transform: uppercase;
    }

    .btn-dot { background: #334155; }
    .btn-run { background: #1e3a5f; border: 1px solid rgba(56, 189, 248, 0.3); }
    .btn-four { background: linear-gradient(135deg, #0284c7 0%, #0369a1 100%); box-shadow: 0 4px 12px rgba(2, 132, 199, 0.35); }
    .btn-six { background: linear-gradient(135deg, #9333ea 0%, #6b21a8 100%); box-shadow: 0 4px 12px rgba(147, 51, 234, 0.35); }

    .actions-grid {
      display: grid;
      grid-template-columns: 1.2fr 1fr 1fr;
      gap: 10px;
      margin-bottom: 12px;
    }

    .btn-wicket {
      background: linear-gradient(135deg, #ef4444 0%, #b91c1c 100%);
      box-shadow: 0 4px 14px rgba(239, 68, 68, 0.35);
      font-size: 1.25rem;
    }
    .btn-extra {
      background: linear-gradient(135deg, #d97706 0%, #b45309 100%);
      font-size: 1.15rem;
    }

    .extra-
