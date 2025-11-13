<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <title>Toy Scan Button</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    body {
      margin: 0;
      min-height: 100vh;
      display: flex;
      align-items: center;
      justify-content: center;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
      background: #0f172a;
      color: #e5e7eb;
    }

    .container {
      text-align: center;
    }

    #scanButton {
      padding: 12px 32px;
      font-size: 1.2rem;
      font-weight: 600;
      border-radius: 999px;
      border: none;
      cursor: pointer;
      background: linear-gradient(135deg, #4ade80, #16a34a);
      color: #022c22;
      box-shadow: 0 10px 25px rgba(0, 0, 0, 0.4);
      transition: transform 0.08s ease, box-shadow 0.08s ease;
    }

    #scanButton:active {
      transform: translateY(1px) scale(0.98);
      box-shadow: 0 4px 12px rgba(0, 0, 0, 0.5);
    }

    #result {
      margin-top: 24px;
      font-size: 4rem; /* big emoji */
      min-height: 4rem; /* keep layout from jumping */
    }
  </style>
</head>
<body>
  <div class="container">
    <button id="scanButton">Scan</button>
    <div id="result"></div>
  </div>

  <script>
    const button = document.getElementById("scanButton");
    const result = document.getElementById("result");

    function playBeep() {
      const AudioContext = window.AudioContext || window.webkitAudioContext;
      if (!AudioContext) return; // if super old browser, just skip sound

      const ctx = new AudioContext();
      const osc = ctx.createOscillator();
      const gain = ctx.createGain();

      osc.type = "square";
      osc.frequency.value = 900; // beep pitch (Hz)

      osc.connect(gain);
      gain.connect(ctx.destination);

      const now = ctx.currentTime;
      gain.gain.setValueAtTime(0.3, now);
      gain.gain.exponentialRampToValueAtTime(0.001, now + 0.2);

      osc.start(now);
      osc.stop(now + 0.25);

      osc.onended = () => ctx.close();
    }

    button.addEventListener("click", () => {
      // Play the beep
      playBeep();

      // Show big green check
      result.textContent = "✅";

      // Optional: briefly disable the button so kids can't spam it mid-animation
      button.disabled = true;

      // Reset after 800ms (change this if you want longer/shorter)
      setTimeout(() => {
        result.textContent = "";
        button.disabled = false;
      }, 800);
    });
  </script>
</body>
</html>

