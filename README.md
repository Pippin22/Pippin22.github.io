<body>
  <h1 class="title">🎒 Kids Video Locker</h1>

  <!-- Vault Screen -->
  <div id="vaultScreen" class="vaultScreen">
    <button id="vaultBtn" onclick="openVault()">🔓 Open My Vault</button>
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

  <style>
    body {
      font-family: Arial, sans-serif;
      text-align: center;
      margin: 0;
      padding: 0;
      background: linear-gradient(135deg, #89f7fe, #66a6ff);
    }

    .title {
      margin: 20px;
      font-size: 2em;
      color: #fff;
      text-shadow: 2px 2px 5px rgba(0,0,0,0.3);
    }

    .vaultScreen {
      display: flex;
      justify-content: center;
      align-items: center;
      height: 80vh;
    }

    #vaultBtn {
      font-size: 1.5em;
      padding: 20px 40px;
      border: none;
      border-radius: 50px;
      background: #ffcc00;
      color: #333;
      cursor: pointer;
      box-shadow: 0 4px 10px rgba(0,0,0,0.3);
      transition: all 0.3s ease;
    }

    #vaultBtn:hover {
      background: #ffdd33;
      transform: scale(1.05);
    }

    #addBtn {
      position: fixed;
      bottom: 20px;
      right: 20px;
      width: 60px;
      height: 60px;
      border-radius: 50%;
      font-size: 2em;
      border: none;
      background: #ff5722;
      color: #fff;
      box-shadow: 0 4px 10px rgba(0,0,0,0.3);
      cursor: pointer;
      display: none;
    }
  </style>

  <script>
    var STORAGE_KEY = "kids_videos_with_folders";
    var videos = [];

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
        showFolders(); // 👈 Goes directly to homepage
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

    function showFolderContents(folderName) {
      document.getElementById("foldersContainer").style.display = "none";
      document.getElementById("videoContainer").style.display = "flex";
      document.getElementById("homeBtn").style.display = "block";

      var container = document.getElementById("videoContainer");
      container.innerHTML = "";

      videos.filter(v => v.folder === folderName).forEach(v => {
        var card = document.createElement("div");
        card.className = "videoCard";

        var title = document.createElement("div");
        title.className = "title";
        title.textContent = v.title;
        card.appendChild(title);

        var iframe = document.createElement("iframe");
        iframe.width = "100%";
        iframe.height = "200";
        iframe.src = "https://www.youtube-nocookie.com/embed/" + v.vid + "?rel=0&modestbranding=1&controls=1";
        iframe.allowFullscreen = true;
        card.appendChild(iframe);

        var removeBtn = document.createElement("button");
        removeBtn.className = "removeBtn";
        removeBtn.textContent = "X";
        removeBtn.onclick = function(id){
          return function() {
            if (confirm("Remove this video?")) {
              videos = videos.filter(v => v.id !== id);
              save(); showFolderContents(folderName);
            }
          };
        }(v.id);
        card.appendChild(removeBtn);

        container.appendChild(card);
      });
    }

    function showAddForm() {
      document.getElementById("adminSection").style.display = "block";
      document.getElementById("foldersContainer").style.display = "none";
      document.getElementById("videoContainer").style.display = "none";
      document.getElementById("homeBtn").style.display = "block";
      document.getElementById("addBtn").style.display = "none";
    }

    function showFolders() {
      document.getElementById("adminSection").style.display = "none";
      document.getElementById("videoContainer").style.display = "none";
      document.getElementById("foldersContainer").style.display = "flex";
      document.getElementById("homeBtn").style.display = "none";
      document.getElementById("addBtn").style.display = "flex";
      renderFolders();
    }

    document.getElementById("videoForm").onsubmit = function(e){
      e.preventDefault();
      var url = document.getElementById("url").value;
      var title = document.getElementById("title").value || "Untitled";
      var folder = document.getElementById("folder").value || "General";
      var vid = extractYouTubeId(url);
      if (!vid) { alert("Invalid YouTube URL"); return; }
      videos.unshift({id: new Date().getTime(), vid: vid, title: title, folder: folder});
      save(); showFolders();
    };
  </script>
</body>