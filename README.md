
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Toy Scanner</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    :root {
      --bg: #050814;
      --accent: #4ade80;
      --accent-dark: #16a34a;
      --text: #f9fafb;
      --button-bg: #1f2937;
    }

    * {
      box-sizing: border-box;
    }

    body {
      margin: 0;
      min-height: 100vh;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI",
        sans-serif;
      background: radial-gradient(circle at top, #111827, #020617);
      color: var(--text);
      display: flex;
      align-items: center;
      justify-content: center;
    }

    .app {
      width: 100%;
      max-width: 480px;
      padding: 16px;
    }

    .title {
      text-align: center;
      margin: 0 0 12px;
      font-size: 1.4rem;
      letter-spacing: 0.04em;
    }

    .card {
      background: rgba(15, 23, 42, 0.9);
      border-radius: 20px;
      padding: 16px;
      box-shadow: 0 18px 45px rgba(0, 0, 0, 0.5);
      border: 1px solid rgba(148, 163, 184, 0.25);
    }

    .video-wrapper {
      position: relative;
      border-radius: 16px;
      overflow: hidden;
      background: #000;
    }

    #video {
      width: 100%;
      display: block;
      aspect-ratio: 3 / 4;
      object-fit: cover;
      background: #000;
    }

    .scan-hint {
      position: absolute;
      top: 8px;
      left: 50%;
      transform: translateX(-50%);
      background: rgba(15, 23, 42, 0.8);
      padding: 4px 10px;
      border-radius: 999px;
      font-size: 0.75rem;
      opacity: 0.85;
    }

    .tap-overlay {
      position: absolute;
      inset: 0;
      cursor: pointer;
    }

    .tap-label {
      position: absolute;
      bottom: 8px;
      right: 8px;
      background: rgba(15, 23, 42, 0.8);
      padding: 4px 8px;
      border-radius: 999px;
      font-size: 0.7rem;
      opacity: 0.9;
    }

    .hint {
      margin-top: 8px;
      font-size: 0.85rem;
      opacity: 0.8;
      text-align: center;
    }

    .controls {
      margin-top: 12px;
      display: flex;
      justify-content: center;
    }

    button {
      border: none;
      border-radius: 999px;
      padding: 10px 20px;
      font-size: 1rem;
      font-weight: 600;
      cursor: pointer;
      background: var(--button-bg);
      color: var(--text);
      display: inline-flex;
      align-items: center;
      gap: 8px;
      box-shadow: 0 8px 20px rgba(0, 0, 0, 0.4);
      transition: transform 0.08s ease, box-shadow 0.08s ease;
    }

    button span.emoji {
      font-size: 1.2rem;
    }

    button:active {
      transform: translateY(1px) scale(0.98);
      box-shadow: 0 4px 10px rgba(0, 0, 0, 0.4);
    }

    button:disabled {
      opacity: 0.6;
      cursor: default;
      box-shadow: none;
      transform: none;
    }

    button.primary {
      background: linear-gradient(135deg, var(--accent), var(--accent-dark));
      color: #022c22;
    }

    /* Green check overlay */
    #overlay {
      position: fixed;
      inset: 0;
      background: rgba(0, 0, 0, 0.75);
      display: flex;
      align-items: center;
      justify-content: center;
      opacity: 0;
      pointer-events: none;
      transition: opacity 0.2s ease;
      z-index: 20;
    }

    #overlay.visible {
      opacity: 1;
      pointer-events: auto;
    }

    .overlay-card {
      background: #0b1727;
      border-radius: 24px;
      padding: 24px 20px 20px;
      text-align: center;
      width: 80%;
      max-width: 320px;
      border: 1px solid rgba(34, 197, 94, 0.4);
      box-shadow: 0 18px 40px rgba(16, 185, 129, 0.4);
    }

    .check-circle {
      width: 80px;
      height: 80px;
      border-radius: 999px;
      border: 6px solid var(--accent);
      display: flex;
      align-items: center;
      justify-content: center;
      margin: 0 auto 12px;
      font-size: 3rem;
      color: var(--accent);
    }

    .overlay-title {
      font-size: 1.4rem;
      margin: 0 0 4px;
    }

    .overlay-sub {
      margin: 0 0 16px;
      font-size: 0.95rem;
      opacity: 0.85;
    }

    .scan-again-btn {
      background: var(--button-bg);
      color: var(--text);
      width: 100%;
      justify-content: center;
    }

    .tiny {
      font-size: 0.7rem;
      opacity: 0.6;
      margin-top: 4px;
      text-align: center;
    }

    #status {
      margin-top: 6px;
      font-size: 0.75rem;
      opacity: 0.75;
      text-align: center;
    }
  </style>
</head>
<body>
  <div class="app">
    <h1 class="title">Toy Scanner</h1>

    <div class="card">
      <div class="video-wrapper">
        <video id="video" autoplay playsinline></video>
        <div class="scan-hint" id="scanHint">Tap to scan an item</div>
        <div class="tap-overlay" id="tapOverlay"></div>
        <div class="tap-label">Tap to scan</div>
      </div>

      <p class="hint">
       Scan barcode now for train boarding validation.<br />
      </p>

      <div class="controls">
        <button id="startButton" class="primary">
          <span class="emoji">📷</span>
          <span>Start Scanner</span>
        </button>
      </div>

      <p class="tiny">
        Camera might require HTTPS and permission.
      </p>

      <p id="status">Status: Ready</p>
    </div>
  </div>

  <!-- Green check overlay -->
  <div id="overlay">
    <div class="overlay-card">
      <div class="check-circle">✓</div>
      <h2 class="overlay-title">BEEP! All Aboard the Birthday Express!</h2>
      <p class="overlay-sub">This passenger is ready for boarding ✅</p>
      <button id="scanAgainButton" class="scan-again-btn">
        <span class="emoji">🔁</span>
        <span>Scan Another</span>
      </button>
    </div>
  </div>

  <script>
    const video = document.getElementById("video");
    const startButton = document.getElementById("startButton");
    const overlay = document.getElementById("overlay");
    const scanAgainButton = document.getElementById("scanAgainButton");
    const tapOverlay = document.getElementById("tapOverlay");
    const statusEl = document.getElementById("status");
    const scanHint = document.getElementById("scanHint");

    let stream = null;
    let cameraTried = false;

    function setStatus(text) {
      statusEl.textContent = "Status: " + text;
    }

    async function startCamera() {
      setStatus("Requesting camera...");
      try {
        stream = await navigator.mediaDevices.getUserMedia({
          video: { facingMode: { ideal: "environment" } },
          audio: false,
        });
        video.srcObject = stream;
        await video.play();
        setStatus("Camera started. Tap to scan.");
      } catch (err) {
        console.error(err);
        alert("Could not access camera: " + err.message);
        setStatus("Camera failed. Pretend mode only (tap still works).");
        startButton.disabled = false;
        scanHint.textContent = "Camera off – tap to pretend scan";
      }
    }

    function playBeep() {
      const AudioContext = window.AudioContext || window.webkitAudioContext;
      if (!AudioContext) {
        setStatus("Beep not supported on this device.");
        return;
      }

      const ctx = new AudioContext();
      const osc = ctx.createOscillator();
      const gain = ctx.createGain();

      osc.type = "square";
      osc.frequency.value = 900;

      osc.connect(gain);
      gain.connect(ctx.destination);

      const now = ctx.currentTime;
      gain.gain.setValueAtTime(0.25, now);
      gain.gain.exponentialRampToValueAtTime(0.001, now + 0.2);

      osc.start(now);
      osc.stop(now + 0.25);
      osc.onended = () => ctx.close();
    }

    function showOverlay() {
      overlay.classList.add("visible");
    }

    function hideOverlay() {
      overlay.classList.remove("visible");
    }

    function triggerScan() {
      playBeep();
      showOverlay();
      setStatus("Item scanned. Tap 'Scan Another' to continue.");
    }

    startButton.addEventListener("click", () => {
      cameraTried = true;

      if (!navigator.mediaDevices || !navigator.mediaDevices.getUserMedia) {
        alert("Camera access is not supported in this browser.");
        setStatus("Camera not supported. Pretend mode only.");
        scanHint.textContent = "Camera off – tap to pretend scan";
        return;
      }

      startButton.disabled = true;
      setStatus("Starting camera...");
      startCamera();
    });

    // Child taps anywhere on the camera view to "scan"
    tapOverlay.addEventListener("click", () => {
      // We DO NOT block this anymore: tapping always works,
      // whether the camera started or not.
      if (!cameraTried) {
        // First tap before pressing Start? Still allow it (pure pretend mode).
        setStatus("Pretend scan (camera not started).");
      }
      triggerScan();
    });

    scanAgainButton.addEventListener("click", () => {
      hideOverlay();
      setStatus("Ready to scan again. Tap the screen.");
    });

    window.addEventListener("beforeunload", () => {
      if (stream) {
        stream.getTracks().forEach((t) => t.stop());
      }
    });
  </script>
</body>
</html>
