<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Kids Video Locker</title>
  <style>
    body { font-family: Arial, sans-serif; background:#f0f0f0; margin:0; padding:1rem; }
    h1 { text-align:center; }
    #adminSection { display:none; margin-bottom:1rem; }
    .folder { margin-bottom:2rem; }
    .folder h2 { text-align:left; margin-bottom:0.5rem; }
    #videoList { display:flex; flex-wrap:wrap; gap:10px; justify-content:center; }
    .videoCard { background:#fff; border:1px solid #ccc; border-radius:6px; overflow:hidden; width:300px; position:relative; }
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
    button, input, select { padding:8px; margin-top:5px; }
  </style>
</head>
<body>
  <h1>🎒 Kids Video Locker</h1>

  <!-- Admin login -->
  <div id="loginSection" style="text-align:center;margin-bottom:1rem;">
    <input type="password" id="adminPass" placeholder="Enter passcode">
    <button onclick="checkPass()">Unlock Add Video</button>
  </div>

  <!-- Hidden until pass entered -->
  <div id="adminSection">
    <form id="videoForm">
      <input type="text" id="url" placeholder="YouTube link" required style="width:100%;"><br>
      <input type="text" id="title" placeholder="Title (optional)" style="width:100%;"><br>
      <input type="text" id="folder" placeholder="Folder name (e.g. Cartoons)" style="width:100%;"><br>
      <button type="submit">Add Video</button>
    </form>
  </div>

  <div id="foldersContainer"></div>

  <script>
    var PASSCODE = "9999";
    var STORAGE_KEY = "kids_videos_with_folders";
    var videos = [];

    // Load saved videos
    try {
      var saved = localStorage.getItem(STORAGE_KEY);
      if (saved) videos = JSON.parse(saved);
    } catch(e){ videos = []; }

    function save() {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(videos));
    }

    function extractYouTubeId(url) {
      if (url.includes("youtu.be/")) return url.split("youtu.be/")[1].split(/[?&]/)[0];
      if (url.includes("watch?v=")) return url.split("watch?v=")[1].split("&")[0];
      if (url.includes("shorts/")) return url.split("shorts/")[1].split(/[?&]/)[0];
      return null;
    }

    function render() {
      var container = document.getElementById("foldersContainer");
      container.innerHTML = "";

      // Group by folder
      var folders = {};
      videos.forEach(v => {
        if (!folders[v.folder]) folders[v.folder] = [];
        folders[v.folder].push(v);
      });

      for (var folderName in folders) {
        var folderDiv = document.createElement("div");
        folderDiv.className = "folder";

        var h2 = document.createElement("h2");
        h2.textContent = folderName;
        folderDiv.appendChild(h2);

        var list = document.createElement("div");
        list.id = "videoList";

        folders[folderName].forEach(v => {
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
                var pin = prompt("Enter unlock PIN:");
                if (pin === PASSCODE) {
                  videos = videos.map(x => x.id==id ? {...x, locked:false} : x);
                  save(); render();
                } else { alert("Wrong PIN"); }
              };
            }(v.id);
            card.appendChild(overlay);

          } else {
            var iframe = document.createElement("iframe");
            iframe.width = "100%";
            iframe.height = "200";
            iframe.src = "https://www.youtube-nocookie.com/embed/" + v.vid + "?rel=0&modestbranding=1&controls=1";
            iframe.title = v.title;
            iframe.allow = "accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture";
            iframe.allowFullscreen = true;
            card.appendChild(iframe);
          }

          // Remove button
          var removeBtn = document.createElement("button");
          removeBtn.className = "removeBtn";
          removeBtn.textContent = "X";
          removeBtn.onclick = function(id){
            return function() {
              if (confirm("Remove this video?")) {
                videos = videos.filter(v => v.id !== id);
                save(); render();
              }
            };
          }(v.id);
          card.appendChild(removeBtn);

          list.appendChild(card);
        });

        folderDiv.appendChild(list);
        container.appendChild(folderDiv);
      }
    }

    // Add video form
    document.getElementById("videoForm").onsubmit = function(e){
      e.preventDefault();
      var url = document.getElementById("url").value;
      var title = document.getElementById("title").value || "Untitled";
      var folder = document.getElementById("folder").value || "General";
      var vid = extractYouTubeId(url);
      if (!vid) { alert("Invalid YouTube URL"); return; }
      videos.unshift({id: new Date().getTime(), vid: vid, title: title, folder: folder, locked: true});
      save(); render(); this.reset();
    };

    function checkPass() {
      var entered = document.getElementById("adminPass").value;
      if (entered === PASSCODE) {
        document.getElementById("adminSection").style.display = "block";
        document.getElementById("loginSection").style.display = "none";
      } else {
        alert("Wrong passcode");
      }
    }

    render();
  </script>
</body>
</html>