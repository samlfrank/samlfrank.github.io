# samlfrank.github.io

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Toy Barcode Scanner</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    :root {
      --bg: #050814;
      --accent: #4ade80;
      --accent-dark: #16a34a;
      --text: #f9fafb;
      --button-bg: #1f2933;
      --button-bg-hover: #111827;
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
    }

    #video {
      width: 100%;
      background: #000;
      display: block;
      aspect-ratio: 3 / 4;
      object-fit: cover;
    }

    /* Invisible tap area to trigger pretend scan if needed */
    .tap-overlay {
      position: absolute;
      inset: 0;
      cursor: pointer;
    }

    .tap-label {
      position: absolute;
      bottom: 8px;
      right: 8px;
      background: rgba(15, 23, 42, 0.75);
      padding: 4px 8px;
      border-radius: 999px;
      font-size: 0.7rem;
      opacity: 0.8;
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
      transition: transform 0.08s ease, box-shadow 0.08s ease,
        background 0.12s ease;
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
      transform: none;
      box-shadow: none;
    }

    button.primary {
      background: linear-gradient(135deg, var(--accent), var(--accent-dark));
      color: #022c22;
    }

    button.primary:active {
      background: linear-gradient(135deg, var(--accent-dark), var(--accent));
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
  </style>
</head>
<body>
  <div class="app">
    <h1 class="title">Toy Scanner</h1>

    <div class="card">
      <div class="video-wrapper">
        <video id="video" autoplay playsinline></video>
        <!-- Tap anywhere in the video to force a pretend scan -->
        <div class="tap-overlay" id="tapOverlay"></div>
        <div class="tap-label">Tap to scan (pretend)</div>
      </div>

      <p class="hint">
        Point at any barcode on a box or toy. When it finds one, you’ll hear a
        beep and see a big green check ✔️<br />
        If not, just tap the video for a pretend scan.
      </p>

      <div class="controls">
        <button id="startButton" class="primary">
          <span class="emoji">📷</span>
          <span>Start Scanner</span>
        </button>
      </div>

      <p class="tiny">
        Tip: Open over HTTPS and turn off mute so you can hear the beep.
      </p>
    </div>
  </div>

  <!-- Green check overlay -->
  <div id="overlay">
    <div class="overlay-card">
      <div class="check-circle">✓</div>
      <h2 class="overlay-title">Beep! Scanned!</h2>
      <p class="overlay-sub">This item is now in the pretend cart ✅</p>
      <button id="scanAgainButton" class="scan-again-btn">
        <span class="emoji">🔁</span>
        <span>Scan Another</span>
      </button>
    </div>
  </div>

  <script type="module">
    // Try to load a polyfill so barcode detection works on more browsers.
    import { BarcodeDetector as BarcodeDetectorPolyfill } from "https://fastly.jsdelivr.net/npm/barcode-detector@2/dist/es/pure.min.js";

    if (!("BarcodeDetector" in window)) {
      window.BarcodeDetector = BarcodeDetectorPolyfill;
    }

    const video = document.getElementById("video");
    const startButton = document.getElementById("startButton");
    const overlay = document.getElementById("overlay");
    const scanAgainButton = document.getElementById("scanAgainButton");
    const tapOverlay = document.getElementById("tapOverlay");

    let detector = null;
    let stream = null;
    let scanning = false;
    let barcodeSupported = true;

    async function startCamera() {
      try {
        stream = await navigator.mediaDevices.getUserMedia({
          video: { facingMode: { ideal: "environment" } },
          audio: false,
        });
        video.srcObject = stream;
        await video.play();
      } catch (err) {
        console.error(err);
        alert("Could not access camera: " + err.message);
        startButton.disabled = false;
        barcodeSupported = false;
      }
    }

    function playBeep() {
      const AudioContext = window.AudioContext || window.webkitAudioContext;
      if (!AudioContext) return;

      const ctx = new AudioContext();
      const osc = ctx.createOscillator();
      const gain = ctx.createGain();

      osc.type = "square";
      osc.frequency.value = 1000; // beep pitch

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

    // This is used both for real and pretend scans
    function triggerScanSuccess() {
      scanning = false;
      playBeep();
      showOverlay();
    }

    async function scanLoop() {
      if (!scanning || !detector || !barcodeSupported) return;

      try {
        const barcodes = await detector.detect(video);

        if (barcodes && barcodes.length > 0) {
          triggerScanSuccess();
          return;
        }
      } catch (err) {
        console.error("Barcode detect error:", err);
        // If detection keeps failing, fall back to pretend mode
        barcodeSupported = false;
      }

      requestAnimationFrame(scanLoop);
    }

    async function startScanner() {
      if (!navigator.mediaDevices || !navigator.mediaDevices.getUserMedia) {
        alert("Camera access is not supported in this browser.");
        startButton.disabled = false;
        barcodeSupported = false;
        return;
      }

      if (!window.BarcodeDetector) {
        // Real barcode scanning not available; we'll still allow pretend scans.
        barcodeSupported = false;
      } else {
        if (!detector) {
          detector = new window.BarcodeDetector();
        }
      }

      startButton.disabled = true;

      await startCamera();

      scanning = true;
      scanLoop();
    }

    startButton.addEventListener("click", () => {
      startScanner();
    });

    // Pretend scan when user taps the video area
    tapOverlay.addEventListener("click", () => {
      // Only allow tap-scan when camera has at least been started once
      triggerScanSuccess();
    });

    scanAgainButton.addEventListener("click", () => {
      hideOverlay();
      if (stream) {
        scanning = true;
        scanLoop();
      }
    });

    window.addEventListener("beforeunload", () => {
      if (stream) {
        stream.getTracks().forEach((t) => t.stop());
      }
    });
  </script>
</body>
</html>
