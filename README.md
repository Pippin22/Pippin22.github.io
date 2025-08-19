<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Kids Video Locker</title>
  <style>
    body { font-family: Arial, sans-serif; background:#f0f0f0; margin:0; padding:1rem; }
    h1 { text-align:center; margin-bottom:1rem; }
    #foldersContainer, #videoContainer {
      display: flex; flex-wrap: wrap; gap:15px; justify-content:center;
    }
    .folderCard {
      width:120px; height:100px;
      background:#fff;
      border-radius:12px;
      box-shadow:0 4px 8px rgba(0,0,0,0.1);
      display:flex; flex-direction:column;
      justify-content:center; align-items:center;
      cursor:pointer; transition:transform 0.2s;
    }
    .folderCard:hover { transform:scale(1.05); }
    .folderIcon { font-size:40px; }
    .folderName { font-weight:bold; margin-top:5px; font-size:14px; text-align:center; }
    .videoCard {
      background:#fff; border:1px solid #ccc;
      border-radius:6px; overflow:hidden; width:300px;
      position:relative;
    }
    .videoCard img, .videoCard iframe { width:100%; display:block; }
    .videoCard .title { padding:5px; font-size:14px; font-weight:bold; text-align:center; }
    .lockOverlay {
      position:absolute; top:0; left:0; right:0; bottom:0;
      background:rgba(0,0,0,0.5); color:#fff;
      display:flex; align-items:center; justify-content:center;
      font-size:20px; font-weight:bold;
      cursor:pointer;
    }
    .removeBtn {
      background:red; color:#fff; border:none; padding:4px 8px;
      cursor:pointer; position:absolute; top:5px; right:5px;
      border-radius:4px;
    }
    #addBtn {
      position:fixed; bottom:20px; right:20px;
      width:50px; height:50px;
      background:#4CAF50; color:white;
      border:none; border-radius:50%;
      font-size:28px; font-weight:bold;
      box-shadow:0 4px 8px rgba(0,0,0,0.2);
      cursor:pointer; display:none;
      justify-content:center; align-items:center;
    }
    #homeBtn {
      position:fixed; top:10px; left:10px;
      background:#2196F3; color:white;
      border:none; padding:8px 14px;
      border-radius:6px; font-size:14px;
      cursor:pointer; display:none;
    }
    #adminSection { display:none; margin-top:60px; }
  </style>
</head>
<body>
  <h1>🎒 Kids Video Locker</h1>

  <button id="homeBtn" onclick="showFolders()">🏠 Home</button>

  <div id="adminSection">
    <form id="videoForm">
      <input type="text" id="url" placeholder="YouTube link" required style="width:100%;"><br>
      <input type="text" id="title" placeholder="Title (optional)" style="width:100%;"><br>
      <input type="text" id="folder" placeholder="Folder name (e.g. Cartoons)" style="width:100%;"><br>
      <button type="submit">Add Video</button>
    </form>
  </div>

  <div id="foldersContainer"></div>
  <div id="videoContainer" style="display:none;"></div>

  <button id="addBtn" onclick="showAddForm()">+</button>

  <script>
    var STORAGE_KEY = "kids_videos_with_folders";
    var videos = [];

    try {
      var saved = localStorage.getItem(STORAGE_KEY);
      if (saved) {
        videos = JSON.parse(saved);

        // MIGRATION: assign default folder if missing
        videos = videos.map(v => {
          if (!v.folder) v.folder = "General";
          return v;
        });
      }
    } catch(e){ videos = []; }

    function save() { localStorage.setItem(STORAGE_KEY, JSON.stringify(videos)); }

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

        if (v.locked) {
          var thumb = document.createElement("img");
          thumb.src = "https://img.youtube.com/vi/" + v.vid + "/hqdefault.jpg";
          card.appendChild(thumb);

          var overlay = document.createElement("div");
          overlay.className = "lockOverlay";
          overlay.textContent = "🔒 Locked";
          overlay.onclick = function(id){
            return function() {
              var pin = prompt("Enter PIN (9999):");
              if (pin === "9999") {
                videos = videos.map(x => x.id==id ? {...x, locked:false} : x);
                save(); showFolderContents(folderName);
              } else { alert("Wrong PIN"); }
            };
          }(v.id);
          card.appendChild(overlay);

        } else {
          var iframe = document.createElement("iframe");
          iframe.width = "100%";
          iframe.height = "200";
          iframe.src = "https://www.youtube-nocookie.com/embed/" + v.vid + "?rel=0&modestbranding=1&controls=1";
          iframe.allow = "accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture";
          iframe.allowFullscreen = true;
          card.appendChild(iframe);
        }

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
      videos.unshift({id: new Date().getTime(), vid: vid, title: title, folder: folder, locked: true});
      save(); showFolders();
    };

    showFolders(); // load folders at start
  </script>
</body>
</html>