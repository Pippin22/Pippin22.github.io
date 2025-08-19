<body>
  <h1>🎒 Kids Video Locker</h1>

  <!-- Vault unlock -->
  <div id="vaultScreen">
    <button onclick="openVault()">🔓 Open My Vault</button>
  </div>

  <!-- App content (hidden until vault opens) -->
  <div id="appContent" style="display:none;">
    <button id="homeBtn" onclick="showFolders()">🏠 Home</button>

    <div id="adminSection">
      <form id="videoForm">
        <input type="text" id="url" placeholder="YouTube link" required><br>
        <input type="text" id="title" placeholder="Title (optional)"><br>
        <input type="text" id="folder" placeholder="Folder name"><br>
        <button type="submit">Add Video</button>
      </form>
    </div>

    <div id="foldersContainer"></div>
    <div id="videoContainer" style="display:none;"></div>

    <button id="addBtn" onclick="showAddForm()">+</button>
  </div>

  <script>
    var STORAGE_KEY = "kids_videos_with_folders";
    var videos = [];

    // Load from localStorage
    try {
      var saved = localStorage.getItem(STORAGE_KEY);
      if (saved) videos = JSON.parse(saved);
    } catch(e){ videos = []; }

    function save() { localStorage.setItem(STORAGE_KEY, JSON.stringify(videos)); }

    // 🔑 Unlock vault
    function openVault() {
      var pin = prompt("Enter PIN (default: 9999)");
      if (pin === "9999") {
        document.getElementById("vaultScreen").style.display = "none";
        document.getElementById("appContent").style.display = "block";
        showFolders();
      } else {
        alert("Wrong PIN");
      }
    }

    function extractYouTubeId(url) {
      if (url.includes("youtu.be/")) return url.split("youtu.be/")[1].split(/[?&]/)[0];
      if (url.includes("watch?v=")) return url.split("watch?v=")[1].split("&")[0];
      if (url.includes("shorts/")) return url.split("shorts/")[1].split(/[?&]/)[0];
      return null;
    }

    // Render folders
    function renderFolders() {
      var container = document.getElementById("foldersContainer");
      container.innerHTML = "";
      var folders = {};
      videos.forEach(v => { if (!folders[v.folder]) folders[v.folder] = []; });

      for (var folderName in folders) {
        var card = document.createElement("div");
        card.className = "folderCard";
        card.onclick = (function(name){ return function(){ showFolderContents(name); }; })(folderName);

        var icon = document.createElement("div");
        icon.className = "folderIcon";
        icon.textContent = "📁";
        card.appendChild(icon);

        var nameDiv = document.createElement("div");
        nameDiv.className = "folderName";
        nameDiv.textContent = folderName;
        card.appendChild(nameDiv);

        container.appendChild(card);
      }
    }

    // Show folder contents
    function showFolderContents(folderName) {
      document.getElementById("foldersContainer").style.display =