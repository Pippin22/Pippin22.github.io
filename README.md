<body>
  <!-- 🌈 Vault Opening Screen -->
  <div id="vaultScreen" class="vault-screen">
    <h1 class="vault-title">🌟 Uchoosetube 🌟</h1>
    <p class="vault-subtitle">☀️ A safe place to watch only what YOU choose 🌈</p>

    <!-- Vault Button -->
    <button class="vault-btn" onclick="showPinPad()">🔑 OPEN VAULT</button>

    <!-- Cartoon PIN Pad -->
    <div id="pinPad" class="pin-pad" style="display:none;">
      <p id="pinMessage" class="pin-message">Enter your 4-digit PIN</p>
      <div class="pin-dots" id="pinDots">____</div>
      <div class="pin-buttons">
        <button onclick="pressPin('1')">1</button>
        <button onclick="pressPin('2')">2</button>
        <button onclick="pressPin('3')">3</button>
        <button onclick="pressPin('4')">4</button>
        <button onclick="pressPin('5')">5</button>
        <button onclick="pressPin('6')">6</button>
        <button onclick="pressPin('7')">7</button>
        <button onclick="pressPin('8')">8</button>
        <button onclick="pressPin('9')">9</button>
        <button onclick="clearPin()">❌</button>
        <button onclick="pressPin('0')">0</button>
        <button onclick="submitPin()">✅</button>
      </div>
    </div>
  </div>

  <!-- 📂 App Content -->
  <div id="appContent" style="display:none;">
    <h1 class="app-title">🌈 Uchoosetube</h1>
    <div id="foldersContainer" class="folders"></div>
    <div id="videoContainer" class="videos" style="display:none;"></div>
    <button id="addBtn" onclick="showAddForm()" class="addBtn">➕</button>

    <!-- Add video form -->
    <div id="adminSection" style="display:none;">
      <form id="videoForm" class="video-form">
        <input type="text" id="url" placeholder="🎬 Paste YouTube Link" required>
        <input type="text" id="title" placeholder="📺 Video Title">
        <input type="text" id="folder" placeholder="📂 Folder (e.g. Cartoons)">
        <button type="submit" class="addVideoBtn">✅ Add Video</button>
      </form>
    </div>
  </div>

  <script>
    let enteredPin = "";
    let isNewUser = !localStorage.getItem("vaultPin");

    function showPinPad() {
      document.getElementById("pinPad").style.display = "block";
      document.getElementById("pin