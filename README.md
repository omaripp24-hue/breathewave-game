<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>BreatheWave Multiplayer</title>

  <style>
    :root {
      --bg1: #020617;
      --bg2: #0f172a;
      --accent: #38bdf8;
      --accent-soft: #0ea5e9;
      --card: #020617cc;
      --border-subtle: #1f2937;
      --text-muted: #9ca3af;
    }

    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      min-height: 100vh;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI",
        sans-serif;
      color: #f9fafb;
      background: radial-gradient(circle at top, #0f172a 0, #020617 55%);
      overflow: hidden;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    /* Animated background blobs */
    .bg-orb {
      position: fixed;
      width: 420px;
      height: 420px;
      border-radius: 50%;
      filter: blur(60px);
      opacity: 0.65;
      pointer-events: none;
      mix-blend-mode: screen;
      z-index: 0;
    }

    .bg-orb.orb1 {
      background: #22d3ee;
      top: -80px;
      left: -60px;
      animation: float1 16s infinite alternate ease-in-out;
    }

    .bg-orb.orb2 {
      background: #6366f1;
      bottom: -120px;
      right: -80px;
      animation: float2 22s infinite alternate ease-in-out;
    }

    @keyframes float1 {
      0% { transform: translate3d(0, 0, 0) scale(1); }
      100% { transform: translate3d(60px, 80px, 0) scale(1.15); }
    }

    @keyframes float2 {
      0% { transform: translate3d(0, 0, 0) scale(1.1); }
      100% { transform: translate3d(-80px, -40px, 0) scale(1); }
    }

    /* Layout */
    .shell {
      position: relative;
      z-index: 1;
      width: 100%;
      max-width: 1100px;
      padding: 24px;
    }

    .card {
      display: grid;
      grid-template-columns: minmax(0, 1.2fr) minmax(0, 1fr);
      gap: 24px;
      background: linear-gradient(
          135deg,
          rgba(15, 23, 42, 0.96),
          rgba(15, 23, 42, 0.85)
        );
      border: 1px solid rgba(148, 163, 184, 0.22);
      border-radius: 24px;
      box-shadow:
        0 18px 60px rgba(15, 23, 42, 0.85),
        0 0 0 1px rgba(15, 23, 42, 0.7);
      padding: 24px 28px;
      backdrop-filter: blur(18px);
    }

    @media (max-width: 860px) {
      body {
        padding: 12px;
        overflow-y: auto;
      }
      .card {
        grid-template-columns: minmax(0, 1fr);
      }
    }

    /* Left panel – text + controls */
    .left {
      display: flex;
      flex-direction: column;
      gap: 16px;
      justify-content: space-between;
    }

    .header {
      display: flex;
      align-items: center;
      gap: 10px;
    }

    .badge-logo {
      width: 40px;
      height: 40px;
      border-radius: 14px;
      background: radial-gradient(circle at 30% 30%, #e0f2fe, #38bdf8 40%, #0f172a 80%);
      display: flex;
      align-items: center;
      justify-content: center;
      box-shadow: 0 0 0 1px rgba(15, 23, 42, 0.7);
    }

    .badge-logo span {
      font-size: 20px;
      filter: drop-shadow(0 0 10px rgba(8, 47, 73, 0.9));
    }

    h1 {
      margin: 0;
      font-size: 1.6rem;
      letter-spacing: 0.03em;
    }

    .tagline {
      margin: 4px 0 0;
      color: var(--text-muted);
      font-size: 0.9rem;
    }

    .pill-row {
      display: flex;
      flex-wrap: wrap;
      gap: 8px;
      margin-top: 4px;
    }

    .pill {
      padding: 4px 10px;
      border-radius: 999px;
      border: 1px solid rgba(148, 163, 184, 0.6);
      font-size: 0.7rem;
      text-transform: uppercase;
      letter-spacing: 0.08em;
      color: #e5e7eb;
      background: rgba(15, 23, 42, 0.8);
    }

    .controls-group {
      margin-top: 8px;
      padding: 12px 14px;
      border-radius: 14px;
      border: 1px solid rgba(148, 163, 184, 0.3);
      background: radial-gradient(circle at top left, #0b1120, #020617);
      display: flex;
      flex-direction: column;
      gap: 10px;
    }

    .controls-row {
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
    }

    label {
      font-size: 0.78rem;
      text-transform: uppercase;
      letter-spacing: 0.08em;
      color: var(--text-muted);
    }

    select {
      margin-top: 4px;
      padding: 6px 10px;
      border-radius: 999px;
      border: 1px solid rgba(148, 163, 184, 0.6);
      background: rgba(15, 23, 42, 0.95);
      color: #f9fafb;
      font-size: 0.84rem;
      outline: none;
    }

    .controls-buttons {
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
      margin-top: 6px;
    }

    button {
      padding: 7px 16px;
      border-radius: 999px;
      border: none;
      font-size: 0.86rem;
      cursor: pointer;
      display: inline-flex;
      align-items: center;
      gap: 6px;
      transition: transform 0.15s ease, box-shadow 0.15s ease, background 0.15s ease,
        opacity 0.15s ease;
      white-space: nowrap;
    }

    button.primary {
      background: linear-gradient(135deg, #38bdf8, #0ea5e9);
      color: #0b1120;
      box-shadow:
        0 10px 30px rgba(8, 47, 73, 0.6),
        0 0 0 1px rgba(8, 47, 73, 0.6);
      font-weight: 600;
    }

    button.secondary {
      background: rgba(15, 23, 42, 0.96);
      color: #e5e7eb;
      border: 1px solid rgba(148, 163, 184, 0.7);
    }

    button:hover {
      transform: translateY(-1px) scale(1.01);
      box-shadow: 0 14px 35px rgba(15, 23, 42, 0.6);
    }

    button:active {
      transform: translateY(0);
      opacity: 0.9;
      box-shadow: none;
    }

    .scores {
      display: flex;
      flex-wrap: wrap;
      gap: 10px;
      margin-top: 4px;
    }

    .score-card {
      flex: 1;
      min-width: 150px;
      border-radius: 14px;
      border: 1px solid rgba(148, 163, 184, 0.35);
      background: radial-gradient(circle at top, #020617, #020617f2);
      padding: 8px 10px;
      display: flex;
      flex-direction: column;
      gap: 2px;
    }

    .score-label {
      font-size: 0.72rem;
      color: var(--text-muted);
      text-transform: uppercase;
      letter-spacing: 0.08em;
    }

    .score-value {
      font-size: 1.2rem;
      font-weight: 600;
    }

    .status-text {
      margin-top: 4px;
      font-size: 0.8rem;
      color: var(--text-muted);
    }

    /* Right panel – game visuals & picture */
    .right {
      position: relative;
      border-radius: 18px;
      padding: 16px;
      background: radial-gradient(circle at top, #020617f9, #020617d0);
      border: 1px solid rgba(148, 163, 184, 0.4);
      overflow: hidden;
      display: flex;
      flex-direction: column;
      gap: 12px;
    }

    .hero-image-wrap {
      position: relative;
      width: 100%;
      border-radius: 14px;
      overflow: hidden;
      border: 1px solid rgba(148, 163, 184, 0.35);
      background: radial-gradient(circle at top, #0b1120, #020617);
      min-height: 140px;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .hero-image-wrap::before {
      content: "";
      position: absolute;
      inset: 0;
      background: radial-gradient(circle at 10% 0%, rgba(56, 189, 248, 0.4), transparent 55%);
      opacity: 0.7;
      pointer-events: none;
    }

    /* Replace this image with your own in /images/ if you like */
    .hero-image {
      width: 100%;
      max-height: 220px;
      object-fit: cover;
      opacity: 0.9;
      mix-blend-mode: screen;
    }

    .hero-fallback {
      font-size: 0.85rem;
      color: var(--text-muted);
      padding: 6px 8px;
    }

    .game-visual {
      margin-top: 4px;
      display: flex;
      flex-direction: column;
      gap: 10px;
      align-items: center;
    }

    .breath-circle-wrapper {
      position: relative;
      width: 220px;
      height: 220px;
      display: flex;
      align-items: center;
      justify-content: center;
    }

    #circle {
      width: 190px;
      height: 190px;
      border-radius: 50%;
      background:
        radial-gradient(circle at 30% 20%, #e0f2fe, transparent 40%),
        radial-gradient(circle at 70% 80%, #bae6fd, transparent 50%),
        radial-gradient(circle at 50% 50%, #0ea5e9, #0369a1 65%, #020617 100%);
      box-shadow:
        0 0 40px rgba(56, 189, 248, 0.85),
        0 0 0 1px rgba(15, 23, 42, 0.9);
      transition: transform 1s ease-in-out, box-shadow 1s ease-in-out, filter 0.4s ease;
    }

    #circle.breathe-in {
      transform: scale(1.14);
      box-shadow:
        0 0 60px rgba(56, 189, 248, 1),
        0 0 0 2px rgba(56, 189, 248, 0.8);
      filter: saturate(1.15);
    }

    #circle.breathe-hold {
      transform: scale(1.2);
      box-shadow:
        0 0 70px rgba(56, 189, 248, 1),
        0 0 0 2px rgba(129, 140, 248, 0.9);
      filter: saturate(1.2) brightness(1.03);
    }

    #circle.breathe-out {
      transform: scale(1.0);
      box-shadow:
        0 0 35px rgba(56, 189, 248, 0.8),
        0 0 0 1px rgba(15, 23, 42, 0.9);
      filter: saturate(0.95);
    }

    .breath-label {
      position: absolute;
      bottom: -6px;
      font-size: 0.78rem;
      color: var(--text-muted);
      text-transform: uppercase;
      letter-spacing: 0.18em;
    }

    #hold-zone {
      margin-top: 6px;
      width: 140px;
      height: 120px;
      border-radius: 16px;
      border: 1px dashed rgba(148, 163, 184, 0.8);
      background: rgba(15, 23, 42, 0.92);
      display: flex;
      justify-content: center;
      align-items: center;
      font-size: 0.8rem;
      color: var(--text-muted);
      transition: background 0.25s ease, transform 0.2s ease, border-color 0.25s ease,
        box-shadow 0.25s ease;
      text-align: center;
      padding: 8px;
    }

    #hold-zone.active {
      background: radial-gradient(circle at top, #0ea5e955, #020617f2);
      border-color: var(--accent-soft);
      transform: translateY(-1px) scale(1.03);
      box-shadow: 0 0 30px rgba(56, 189, 248, 0.6);
      color: #e0f2fe;
    }

    .hint {
      font-size: 0.78rem;
      color: var(--text-muted);
      text-align: center;
      margin-top: 2px;
    }
  </style>
</head>
<body>
  <!-- Background glows -->
  <div class="bg-orb orb1"></div>
  <div class="bg-orb orb2"></div>

  <div class="shell">
    <div class="card">
      <!-- LEFT: text + controls -->
      <div class="left">
        <div>
          <div class="header">
            <div class="badge-logo">
              <span>🫧</span>
            </div>
            <div>
              <h1>BreatheWave</h1>
              <p class="tagline">Multiplayer breathing meditation with rhythm and focus.</p>
            </div>
          </div>

          <div class="pill-row">
            <div class="pill">2 Players</div>
            <div class="pill">Breath Training</div>
            <div class="pill">Mindfulness Game</div>
          </div>

          <div class="controls-group">
            <div class="controls-row">
              <div>
                <label for="mode-select">Breathing Mode</label><br />
                <select id="mode-select">
                  <option value="starter">Starter · 4s in / 6s out</option>
                  <option value="flow">Flow · 4s in / 5s hold / 4s out</option>
                  <option value="recharge">Recharge · 6s in / 6s out</option>
                </select>
              </div>

              <div>
                <label for="level-select">Level Length</label><br />
                <select id="level-select">
                  <option value="1">Level 1 · 1 minute</option>
                  <option value="2">Level 2 · 3 minutes</option>
                  <option value="3">Level 3 · 5 minutes</option>
                </select>
              </div>
            </div>

            <div class="controls-buttons">
              <button id="start-btn" class="primary">
                ▶ Start Round
              </button>
              <button id="pause-btn" class="secondary">
                ⏸ Pause
              </button>
              <button id="complete-btn" class="secondary">
                ✅ Complete
              </button>
            </div>
          </div>
        </div>

        <div>
          <div class="scores">
            <div class="score-card">
              <span class="score-label">Player 1 Score</span>
              <span class="score-value" id="p1-score">0</span>
            </div>
            <div class="score-card">
              <span class="score-label">Player 2 Score</span>
              <span class="score-value" id="p2-score">0</span>
            </div>
          </div>
          <p id="status" class="status-text">Ready. Player 1 will begin when you start the round.</p>
        </div>
      </div>

      <!-- RIGHT: image + game visual -->
      <div class="right">
        <div class="hero-image-wrap">
          <!--
            Optional: put your own image at /images/breathewave-hero.jpg
            and use: <img src="images/breathewave-hero.jpg" class="hero-image" alt="BreatheWave illustration" />
          -->
          <div class="hero-fallback">
            Add a hero image here by placing a file like<br />
            <code>images/breathewave-hero.jpg</code> in your repo and updating this section.
          </div>
        </div>

        <div class="game-visual">
          <div class="breath-circle-wrapper">
            <div id="circle"></div>
            <div class="breath-label" id="breath-label">Waiting…</div>
          </div>

          <div id="hold-zone">
            Hold your mouse here to stay “breathing”
          </div>
          <p class="hint">
            Keep your cursor in the Hold Zone during the breath cycle to earn points.
            Players switch every full breath.
          </p>
        </div>
      </div>
    </div>
  </div>

  <!-- Audio cues -->
  <audio id="inhale-sound" src="https://actions.google.com/sounds/v1/cartoon/clang_and_wobble.ogg"></audio>
  <audio id="exhale-sound" src="https://actions.google.com/sounds/v1/cartoon/wood_plank_flicks.ogg"></audio>

  <script>
    const circle = document.getElementById("circle");
    const startBtn = document.getElementById("start-btn");
    const pauseBtn = document.getElementById("pause-btn");
    const completeBtn = document.getElementById("complete-btn");
    const modeSelect = document.getElementById("mode-select");
    const levelSelect = document.getElementById("level-select");
    const holdZone = document.getElementById("hold-zone");
    const p1ScoreDisplay = document.getElementById("p1-score");
    const p2ScoreDisplay = document.getElementById("p2-score");
    const statusDisplay = document.getElementById("status");
    const breathLabel = document.getElementById("breath-label");

    let p1 = 0;
    let p2 = 0;
    let activePlayer = 1;

    let breathing = false;
    let paused = false;
    let holding = false;
    let roundEndTime = null;

    const inhaleSound = document.getElementById("inhale-sound");
    const exhaleSound = document.getElementById("exhale-sound");

    const modes = {
      starter: { inhale: 4000, hold: 0, exhale: 6000 },
      flow: { inhale: 4000, hold: 5000, exhale: 4000 },
      recharge: { inhale: 6000, hold: 0, exhale: 6000 }
    };

    const levels = {
      1: 60 * 1000,      // 1 min
      2: 3 * 60 * 1000,  // 3 min
      3: 5 * 60 * 1000   // 5 min
    };

    holdZone.addEventListener("mouseenter", () => {
      holding = true;
      holdZone.classList.add("active");
    });

    holdZone.addEventListener("mouseleave", () => {
      holding = false;
      holdZone.classList.remove("active");
    });

    function scorePoint() {
      if (!holding || !breathing || paused) return;
      if (activePlayer === 1) {
        p1 += 5;
        p1ScoreDisplay.textContent = p1;
      } else {
        p2 += 5;
        p2ScoreDisplay.textContent = p2;
      }
    }

    function swapPlayer() {
      activePlayer = activePlayer === 1 ? 2 : 1;
    }

    function endRound(manual = false) {
      if (!breathing) return;
      breathing = false;
      paused = false;
      circle.className = "";
      breathLabel.textContent = "Round idle";
      const msg = manual ? "Round ended." : "Round complete!";
      statusDisplay.textContent = `${msg} Final – P1: ${p1} | P2: ${p2}`;
      pauseBtn.textContent = "Pause";
    }

    function isRoundOver() {
      return roundEndTime && Date.now() >= roundEndTime;
    }

    async function waitWithPause(duration) {
      let remaining = duration;
      const step = 100;
      while (remaining > 0 && breathing) {
        if (!paused) {
          const slice = Math.min(step, remaining);
          await new Promise(res => setTimeout(res, slice));
          remaining -= slice;
        } else {
          await new Promise(res => setTimeout(res, step));
        }
        if (isRoundOver()) break;
      }
    }

    function animateBreath(mode) {
      breathing = true;

      async function cycle() {
        if (!breathing) return;
        if (isRoundOver()) { endRound(false); return; }

        // INHALE
        statusDisplay.textContent = `Player ${activePlayer} – Inhale`;
        breathLabel.textContent = "Inhale";
        circle.className = "breathe-in";
        inhaleSound.currentTime = 0;
        inhaleSound.play();
        await waitWithPause(mode.inhale);
        if (!breathing || isRoundOver()) { endRound(false); return; }
        scorePoint();

        // HOLD
        if (mode.hold > 0) {
          statusDisplay.textContent = `Player ${activePlayer} – Hold`;
          breathLabel.textContent = "Hold";
          circle.className = "breathe-hold";
          await waitWithPause(mode.hold);
          if (!breathing || isRoundOver()) { endRound(false); return; }
        }

        // EXHALE
        statusDisplay.textContent = `Player ${activePlayer} – Exhale`;
        breathLabel.textContent = "Exhale";
        circle.className = "breathe-out";
        exhaleSound.currentTime = 0;
        exhaleSound.play();
        await waitWithPause(mode.exhale);
        if (!breathing || isRoundOver()) { endRound(false); return; }
        scorePoint();

        swapPlayer();
        cycle();
      }

      cycle();
    }

    startBtn.addEventListener("click", () => {
      // reset state
      p1 = 0;
      p2 = 0;
      p1ScoreDisplay.textContent = "0";
      p2ScoreDisplay.textContent = "0";
      activePlayer = 1;
      paused = false;
      breathing = false;
      pauseBtn.textContent = "Pause";

      const modeKey = modeSelect.value;
      const mode = modes[modeKey];
      const level = levels[levelSelect.value];
      roundEndTime = Date.now() + level;

      statusDisplay.textContent = "Round started! Player 1 begins.";
      breathLabel.textContent = "Inhale soon…";
      animateBreath(mode);
    });

    pauseBtn.addEventListener("click", () => {
      if (!breathing) return;
      paused = !paused;
      pauseBtn.textContent = paused ? "Resume" : "Pause";
      statusDisplay.textContent = paused
        ? "Paused. Keep your cursor ready in the Hold Zone."
        : `Resumed – Player ${activePlayer}'s breath.`;
    });

    completeBtn.addEventListener("click", () => {
      if (!breathing) return;
      endRound(true);
    });
  </script>
</body>
</html>
