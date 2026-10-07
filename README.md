<!DOCTYPE html>
<html lang="hi">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no" />
  <title>स्ट्राइड - स्पीड और वॉक ट्रैकर</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;600;700;800&family=Space+Grotesk:wght@500;700&display=swap" rel="stylesheet">
  
  <style>
    :root {
      --bg: #090d16;
      --card-bg: rgba(22, 30, 49, 0.7);
      --card-border: rgba(255, 255, 255, 0.08);
      --primary: #10b981;
      --primary-glow: rgba(16, 185, 129, 0.25);
      --accent: #38bdf8;
      --text: #f8fafc;
      --muted: #94a3b8;
      --danger: #ef4444;
      --warning: #f59e0b;
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      -webkit-tap-highlight-color: transparent;
    }

    body {
      font-family: 'Plus Jakarta Sans', sans-serif;
      background: radial-gradient(circle at 50% 10%, #1e1b4b 0%, var(--bg) 70%);
      color: var(--text);
      min-height: 100vh;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      padding: 20px 16px;
    }

    .tracker-card {
      width: 100%;
      max-width: 380px;
      background: var(--card-bg);
      backdrop-filter: blur(20px);
      -webkit-backdrop-filter: blur(20px);
      border: 1px solid var(--card-border);
      border-radius: 28px;
      padding: 28px 22px;
      box-shadow: 0 20px 50px rgba(0, 0, 0, 0.6);
      display: flex;
      flex-direction: column;
      align-items: center;
    }

    .header-bar {
      display: flex;
      justify-content: space-between;
      align-items: center;
      width: 100%;
      margin-bottom: 24px;
    }

    .app-title {
      font-size: 14px;
      font-weight: 700;
      letter-spacing: 1.2px;
      text-transform: uppercase;
      color: var(--muted);
    }

    .badge-signal {
      display: flex;
      align-items: center;
      gap: 6px;
      background: rgba(255, 255, 255, 0.05);
      border: 1px solid var(--card-border);
      padding: 4px 10px;
      border-radius: 20px;
      font-size: 11px;
      font-weight: 600;
      color: var(--muted);
    }

    .signal-dot {
      width: 8px;
      height: 8px;
      border-radius: 50%;
      background: #64748b;
      transition: all 0.3s ease;
    }

    .signal-dot.live {
      background: var(--primary);
      box-shadow: 0 0 8px var(--primary);
    }

    .signal-dot.connecting {
      background: var(--warning);
      box-shadow: 0 0 8px var(--warning);
      animation: pulse 1s infinite alternate;
    }

    @keyframes pulse {
      from { opacity: 0.4; }
      to { opacity: 1; }
    }

    .speed-gauge-wrap {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      margin: 10px 0 24px 0;
      position: relative;
    }

    .speed-value {
      font-family: 'Space Grotesk', sans-serif;
      font-size: 82px;
      font-weight: 700;
      line-height: 0.9;
      color: var(--text);
      text-shadow: 0 0 30px var(--primary-glow);
    }

    .speed-unit {
      margin-top: 10px;
      font-size: 13px;
      font-weight: 700;
      letter-spacing: 1.5px;
      color: var(--accent);
      text-transform: uppercase;
    }

    .stats-matrix {
      width: 100%;
      display: grid;
      grid-template-columns: repeat(3, 1fr);
      gap: 8px;
      background: rgba(15, 23, 42, 0.6);
      border: 1px solid var(--card-border);
      border-radius: 18px;
      padding: 14px 10px;
      margin-bottom: 24px;
    }

    .stat-col {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      text-align: center;
    }

    .stat-col:not(:last-child) {
      border-right: 1px solid var(--card-border);
    }

    .stat-label {
      font-size: 11px;
      font-weight: 600;
      color: var(--muted);
      margin-bottom: 4px;
    }

    .stat-val {
      font-size: 16px;
      font-weight: 700;
      color: var(--text);
    }

    .actions {
      display: flex;
      flex-direction: column;
      width: 100%;
      gap: 10px;
    }

    .btn-main {
      width: 100%;
      padding: 16px;
      border: none;
      border-radius: 16px;
      font-size: 16px;
      font-weight: 700;
      cursor: pointer;
      display: flex;
      align-items: center;
      justify-content: center;
      gap: 8px;
      transition: all 0.2s ease;
      background: linear-gradient(135deg, #10b981 0%, #059669 100%);
      color: #ffffff;
      box-shadow: 0 8px 20px var(--primary-glow);
    }

    .btn-main.connecting {
      background: linear-gradient(135deg, #f59e0b 0%, #d97706 100%);
      box-shadow: 0 8px 20px rgba(245, 158, 11, 0.25);
    }

    .btn-main.tracking {
      background: linear-gradient(135deg, #ef4444 0%, #dc2626 100%);
      box-shadow: 0 8px 20px rgba(239, 68, 68, 0.25);
    }

    .btn-main:active {
      transform: scale(0.98);
    }

    .btn-sub {
      width: 100%;
      padding: 12px;
      background: transparent;
      border: 1px solid var(--card-border);
      border-radius: 14px;
      color: var(--muted);
      font-size: 14px;
      font-weight: 600;
      cursor: pointer;
      transition: all 0.2s;
    }

    .btn-sub:active {
      background: rgba(255, 255, 255, 0.05);
    }

    .status-msg {
      margin-top: 16px;
      font-size: 12px;
      color: #fbbf24;
      text-align: center;
      min-height: 18px;
    }
  </style>
</head>
<body>

  <main class="tracker-card">
    <div class="header-bar">
      <span class="app-title">स्ट्राइड ट्रैकर</span>
      <div class="badge-signal">
        <div class="signal-dot" id="dot"></div>
        <span id="acc-text">GPS ऑफ</span>
      </div>
    </div>

    <div class="speed-gauge-wrap">
      <div class="speed-value" id="speed">0.0</div>
      <div class="speed-unit">किमी/घंटा (km/h)</div>
    </div>

    <div class="stats-matrix">
      <div class="stat-col">
        <span class="stat-label">दूरी</span>
        <span class="stat-val" id="dist">0 m</span>
      </div>
      <div class="stat-col">
        <span class="stat-label">समय</span>
        <span class="stat-val" id="timer">00:00</span>
      </div>
      <div class="stat-col">
        <span class="stat-label">औसत गति</span>
        <span class="stat-val" id="avg-speed">0.0</span>
      </div>
    </div>

    <div class="actions">
      <button class="btn-main" id="track-btn" onclick="toggleTracking()">
        <span>ट्रैकिंग शुरू करें</span>
      </button>
      <button class="btn-sub" onclick="resetAll()">रीसेट करें</button>
    </div>

    <p class="status-msg" id="status">शुरू करने के लिए बटन दबाएं</p>
  </main>

  <script>
    let watchId = null;
    let wakeLock = null;
    let lastPos = null;
    let totalMeters = 0;
    let startTime = null;
    let timerInterval = null;
    let connectionTimeout = null;
    let hasReceivedValidData = false;

    function getDistance(lat1, lon1, lat2, lon2) {
      const R = 6371e3;
      const dLat = ((lat2 - lat1) * Math.PI) / 180;
      const dLon = ((lon2 - lon1) * Math.PI) / 180;
      const a =
        Math.sin(dLat / 2) * Math.sin(dLat / 2) +
        Math.cos((lat1 * Math.PI) / 180) *
          Math.cos((lat2 * Math.PI) / 180) *
          Math.sin(dLon / 2) *
          Math.sin(dLon / 2);
      return R * 2 * Math.atan2(Math.sqrt(a), Math.sqrt(1 - a));
    }

    async function requestWakeLock() {
      try {
        if ('wakeLock' in navigator) {
          wakeLock = await navigator.wakeLock.request('screen');
        }
      } catch (err) {
        console.warn("Wake Lock समर्थित नहीं:", err);
      }
    }

    function releaseWakeLock() {
      if (wakeLock !== null) {
        wakeLock.release();
        wakeLock = null;
      }
    }

    function updateTimer() {
      const elapsedSeconds = Math.floor((Date.now() - startTime) / 1000);
      const mins = String(Math.floor(elapsedSeconds / 60)).padStart(2, '0');
      const secs = String(elapsedSeconds % 60).padStart(2, '0');
      document.getElementById('timer').textContent = `${mins}:${secs}`;

      if (elapsedSeconds > 2 && totalMeters > 0) {
        const avgKmh = (totalMeters / elapsedSeconds) * 3.6;
        document.getElementById('avg-speed').textContent = avgKmh.toFixed(1);
      }
    }

    // ट्रैकिंग बंद करने का कॉमन फ़ंक्शन
    function stopTrackingUI(message = "ट्रैकिंग रोक दी गई है") {
      if (watchId !== null) {
        navigator.geolocation.clearWatch(watchId);
        watchId = null;
      }
      if (timerInterval) {
        clearInterval(timerInterval);
        timerInterval = null;
      }
      if (connectionTimeout) {
        clearTimeout(connectionTimeout);
        connectionTimeout = null;
      }

      releaseWakeLock();

      const btn = document.getElementById("track-btn");
      const dot = document.getElementById("dot");
      const status = document.getElementById("status");

      btn.className = "btn-main";
      btn.innerHTML = "<span>ट्रैकिंग शुरू करें</span>";
      dot.className = "signal-dot";
      document.getElementById("acc-text").textContent = "GPS ऑफ";
      status.textContent = message;
    }

    function toggleTracking() {
      const btn = document.getElementById("track-btn");
      const status = document.getElementById("status");
      const dot = document.getElementById("dot");

      // अगर ट्रैकिंग पहले से चल रही है, तो रोकें
      if (watchId !== null) {
        stopTrackingUI();
        return;
      }

      if (!("geolocation" in navigator)) {
        status.textContent = "तकनीकी समस्या: GPS इस डिवाइस में समर्थित नहीं है";
        return;
      }

      // कनेक्टिंग स्टेट
      hasReceivedValidData = false;
      btn.className = "btn-main connecting";
      btn.innerHTML = "<span>कनेक्ट हो रहा है...</span>";
      dot.className = "signal-dot connecting";
      status.textContent = "सिग्नल खोज रहे हैं... (8 सेकंड में स्वतः बंद हो जाएगा)";

      // फ़ेल-सेफ़ टाइमआउट: 8 सेकंड में सिग्नल न मिले तो स्वतः रोकें
      connectionTimeout = setTimeout(() => {
        if (!hasReceivedValidData) {
          stopTrackingUI("तकनीकी समस्या: 8 सेकंड में GPS सिग्नल नहीं मिला। पुनः प्रयास करें।");
        }
      }, 8000);

      watchId = navigator.geolocation.watchPosition(
        (pos) => {
          const lat = pos.coords.latitude;
          const lon = pos.coords.longitude;
          const acc = Math.round(pos.coords.accuracy);

          // पहला वैध सिग्नल मिलते ही टाइमर शुरू करें और टाइमआउट क्लियर करें
          if (!hasReceivedValidData) {
            hasReceivedValidData = true;
            if (connectionTimeout) clearTimeout(connectionTimeout);

            if (!startTime) startTime = Date.now();
            if (!timerInterval) timerInterval = setInterval(updateTimer, 1000);
            requestWakeLock();

            btn.className = "btn-main tracking";
            btn.innerHTML = "<span>ट्रैकिंग रोकें</span>";
            dot.className = "signal-dot live";
          }

          document.getElementById("acc-text").textContent = `±${acc}m`;

          if (acc > 25) {
            status.textContent = "कमजोर सिग्नल (±" + acc + "m)... खुले में जाएं";
            return;
          }

          let currentSpeed = 0;
          if (pos.coords.speed !== null && pos.coords.speed > 0.3) {
            currentSpeed = pos.coords.speed * 3.6;
          }

          if (lastPos) {
            const distance = getDistance(lastPos.lat, lastPos.lon, lat, lon);
            const timeDiff = (pos.timestamp - lastPos.time) / 1000;

            if (distance >= 2.5 && timeDiff > 0.6) {
              totalMeters += distance;
              if (currentSpeed === 0) {
                currentSpeed = (distance / timeDiff) * 3.6;
              }
              lastPos = { lat, lon, time: pos.timestamp };
            }
          } else {
            lastPos = { lat, lon, time: pos.timestamp };
          }

          if (currentSpeed > 35) currentSpeed = 0;

          document.getElementById("speed").textContent = currentSpeed.toFixed(1);

          if (totalMeters >= 1000) {
            document.getElementById("dist").textContent = (totalMeters / 1000).toFixed(2) + " km";
          } else {
            document.getElementById("dist").textContent = Math.round(totalMeters) + " m";
          }

          status.textContent = "सक्रिय: ट्रैकिंग चालू है";
        },
        (err) => {
          const errors = {
            1: "अनुमति नहीं मिली: लोकेशन परमिशन दें",
            2: "GPS सिग्नल नहीं मिला",
            3: "GPS टाइमआउट हुआ"
          };
          stopTrackingUI(`तकनीकी त्रुटि: ${errors[err.code] || "अज्ञात त्रुटि"} - ट्रैकिंग रद्द`);
        },
        {
          enableHighAccuracy: true,
          maximumAge: 1000,
          timeout: 7500
        }
      );
    }

    function resetAll() {
      stopTrackingUI("डेटा रीसेट कर दिया गया है");

      lastPos = null;
      totalMeters = 0;
      startTime = null;

      document.getElementById("speed").textContent = "0.0";
      document.getElementById("dist").textContent = "0 m";
      document.getElementById("timer").textContent = "00:00";
      document.getElementById("avg-speed").textContent = "0.0";
    }
  </script>
</body>
</html>

