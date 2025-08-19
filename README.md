<body>
  <!-- 🔑 Vault Opening Screen -->
  <div id="vaultScreen" class="vault-screen">
    <h1 class="vault-title">Uchoosetube</h1>
    <p class="vault-subtitle">🔒 A safe place to watch only what you choose!</p>
    <div class="vault-btn" onclick="openVault()"> 
      <span class="vault-icon">🔑</span> 
      Open Vault
    </div>
  </div>

  <!-- 📂 App Content (hidden until vault is opened) -->
  <div id="appContent" style="display:none;">
    <h1 style="text-align:center; margin-top:10px;">Uchoosetube</h1>
    <div class="topbar">
      <button id="homeBtn" onclick="showFolders()" style="display:none;">🏠 Home</button>
      <button id="addBtn" onclick="showAddForm()" class="addBtn">＋</button>
    </div>

    <div id="adminSection" style="display:none;">
      <form id="videoForm">
        <input type="text" id="url" placeholder="Paste YouTube URL" required>
        <input type="text" id="title" placeholder="Video Title">
        <input type="text" id="folder" placeholder="Folder (e.g. Cartoons)">
        <button type="submit">Add Video</button>
      </form>
    </div>

    <div id="foldersContainer" class="folders"></div>
    <div id="videoContainer" class="videos" style="display:none;"></div>
  </div>

  <script>
    function openVault() {
      let pin = localStorage.getItem("vaultPin");
      if (!pin) {
        // First time setup
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
        // Existing user unlock
        let entered = prompt("Enter your PIN:");
        if (entered === pin) {
          document.getElementById("vaultScreen").style.display = "none";
          document.getElementById("appContent").style.display = "block";
        } else {
          alert("Incorrect PIN.");
        }
      }
    }
  </script>
</body>