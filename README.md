<body>
  <!-- 🔑 Vault Opening Screen -->
  <div id="vaultScreen" class="vault-screen">
    <h1 class="vault-title">🌟 Uchoosetube 🌟</h1>
    <p class="vault-subtitle">🔒 A safe place to watch only what YOU choose!</p>
    <button class="vault-btn" onclick="openVault()">🔑 OPEN VAULT</button>
  </div>

  <!-- 📂 App Content (hidden until vault is opened) -->
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
    function openVault() {
      let pin = localStorage.getItem("vaultPin");
      if (!pin) {
        let newPin = prompt("Create a 4-digit PIN:");
        if (newPin && /^\d{4}$/.test(newPin)) {
          localStorage.setItem("vaultPin", newPin);
          alert("PIN created! Welcome to your vault.");
          document.getElementById("vaultScreen").style.display = "none";
          document.getElementById("appContent").style.display = "block";
        } else {
          alert("Invalid PIN. Please use 4 digits.");
        }
      } else {
        let entered = prompt("Enter your PIN:");
        if (entered === pin) {
          document.getElementById("vaultScreen").style.display = "none";
          document.getElementById("appContent").style.display = "block";
        } else {
          alert("