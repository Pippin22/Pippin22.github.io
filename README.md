<body>
  <!-- 🌈 Vault Opening Screen -->
  <div id="vaultScreen" class="vault-screen">
    <h1 class="vault-title">🌟 Uchoosetube 🌟</h1>
    <p class="vault-subtitle">☀️ A safe place to watch only what YOU choose 🌈</p>

    <!-- Vault Button -->
    <button id="openVaultBtn" class="vault-btn" onclick="openVault()">🔑 OPEN VAULT</button>

    <!-- PIN entry -->
    <div id="pinEntry" class="pin-entry" style="display:none;">
      <p id="pinMessage">Enter your 4-digit PIN</p>
      <input type="password" id="pinInput" maxlength="4" class="pin-input">
      <button class="vault-btn" onclick="submitPin()">✅ Unlock</button>
    </div>
  </div>

  <!-- 📂 Main App Content -->
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
    let isNewUser = !localStorage.getItem("vaultPin");

    function openVault() {
      document.getElementById("openVaultBtn").style.display = "none";
      document.getElementById("pinEntry").style.display = "block";
      document.getElementById("pinMessage").innerText =
        isNewUser ? "✨ Create a new 4-digit PIN ✨" : "Enter your 4-digit PIN";
    }

    function submitPin() {
      let enteredPin = document.getElementById("pinInput").value;

      if (enteredPin.length !== 4) {
        alert("PIN must be 4 digits!");
        return;
      }

      if (isNewUser) {
        localStorage.setItem("vaultPin", enteredPin);
        alert("🎉 PIN created! Welcome to your vault.");
        unlockVault();
      } else {
        let storedPin = localStorage.getItem("vaultPin");
        if (enteredPin === storedPin) {
          unlockVault();
        } else {
          alert("❌ Wrong PIN! Try again.");
        }
      }
    }

    function unlockVault() {
      document.getElementById("vaultScreen").style.display = "none";
      document.getElementById("appContent").style.display = "block";
    }

    function showAddForm() {
      const form = document.getElementById("adminSection");
      form.style.display = form.style.display === "block" ? "none" : "block";
    }
  </script>
</body>