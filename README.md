<body>
  <!-- 🔑 Opening Vault Screen -->
  <div id="vaultScreen" class="vault-screen">
    <h1 class="vault-title">🔒 Welcome to Your Vault</h1>
    <button onclick="openVault()" class="vault-btn">Open Vault</button>
  </div>

  <!-- 📂 App Content (hidden until vault opened) -->
  <div id="appContent" style="display:none;">
    <!-- Top bar -->
    <div class="topbar">
      <button id="homeBtn" onclick="showFolders()" style="display:none;">🏠 Home</button>
      <button id="addBtn" onclick="showAddForm()" class="addBtn">＋</button>
    </div>

    <!-- Add video form -->
    <div id="adminSection" style="display:none;">
      <form id="videoForm">
        <input type="text" id="url" placeholder="YouTube URL" required>
        <input type="text" id="title" placeholder="Title">
        <input type="text" id="folder" placeholder="Folder (e.g. Cartoons)">
        <button type="submit">Add Video</button>
      </form>
    </div>

    <!-- Folder cards go here -->
    <div id="foldersContainer" class="folders"></div>

    <!-- Video cards go here -->
    <div id="videoContainer" class="videos" style="display:none;"></div>
  </div>
</body>