<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta name="format-detection" content="telephone=no">
  <title>Kids Video Locker</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f0f0f0;
      padding: 10px;
    }
    .container {
      max-width: 600px;
      margin: auto;
      background: white;
      padding: 20px;
      box-shadow: 0 0 10px rgba(0,0,0,0.1);
      border-radius: 8px;
    }
    h2 {
      text-align: center;
      color: #333;
    }
    input[type="text"], input[type="password"] {
      width: 100%;
      padding: 10px;
      margin-bottom: 10px;
      border: 1px solid #ccc;
      border-radius: 4px;
      box-sizing: border-box;
    }
    button {
      padding: 10px;
      width: 100%;
      border: none;
      border-radius: 4px;
      background-color: #007bff;
      color: white;
      cursor: pointer;
    }
    button:hover {
      background-color: #0056b3;
    }
    .video-item {
      display: flex;
      align-items: center;
      background: #fff;
      margin: 10px 0;
      padding: 15px;
      border: 1px solid #ddd;
      border-radius: 4px;
      box-shadow: 0 2px 4px rgba(0,0,0,0.05);
      word-wrap: break-word;
    }
    .video-item img {
      width: 120px;
      height: auto;
      margin-right: 15px;
      border-radius: 4px;
      flex-shrink: 0;
    }
    .video-item .link-info {
      flex-grow: 1;
      display: flex;
      justify-content: space-between;
      align-items: center;
      flex-wrap: wrap;
    }
    .video-item .link-info h4 {
      margin: 0;
      font-size: 1.1em;
      color: #333;
      word-wrap: break-word;
      flex-basis: 100%;
    }
    .video-controls button, .video-controls a {
      width: auto;
      padding: 8px 12px;
      font-size: 0.9em;
      margin-left: 8px;
      text-decoration: none;
      border-radius: 4px;
      color: white;
      text-align: center;
      display: inline-block;
    }
    .admin-area {
      margin-top: 20px;
      padding: 15px;
      background: #f8f9fa;
      border: 1px solid #e9ecef;
      border-radius: 4px;
      display: none;
    }
    .admin-area h3 {
      margin-top: 0;
    }
  </style>
</head>
<body>

  <div class="container">
    <h2>🎒 Kids Video Locker</h2>

    <form id="videoForm">
      <input type="text" id="url" placeholder="YouTube link" required>
      <input type="text" id="title" placeholder="Title (optional)">
      <button type="submit">Add Video</button>
    </form>

    <div style="text-align: center; margin-top: 15px;">
      <button id="adminBtn" style="background-color: #6c757d; width: auto;">Admin</button>
    </div>

    <div id="adminPanel" class="admin-area">
      <h3>Admin Controls</h3>
      <input type="password" id="newPin" placeholder="Enter New PIN">
      <button id="changePinBtn">Change PIN</button>
    </div>

    <div id="videoList"></div>
  </div>

  <script>
    var ADMIN_PIN = localStorage.getItem("admin_pin") || "1234";
    var STORAGE_KEY = "kids_videos_v4";
    var videoList = document.getElementById("videoList");
    var form = document.getElementById("videoForm");
    var adminBtn = document.getElementById("adminBtn");
    var adminPanel = document.getElementById("adminPanel");
    var changePinBtn = document.getElementById("changePinBtn");
    var newPinInput = document.getElementById("newPin");
    
    var videos = [];
    var adminMode = false;

    try {
      var saved = localStorage.getItem(STORAGE_KEY);
      if (saved) videos = JSON.parse(saved);
    } catch (e) { videos = []; }

    function saveVideos() {
      try { localStorage.setItem(STORAGE_KEY, JSON.stringify(videos)); } catch(e){}
    }
    
    function savePin() {
      try { localStorage.setItem("admin_pin", ADMIN_PIN); } catch(e){}
    }

    function extractYouTubeId(url) {
      var regExp = /^.*(?:(?:youtu\.be\/|v\/|vi\/|u\/\w\/|embed\/)|(?:(?:watch)?\?v=|\&v=))([^#\&\?]*).*/;
      var match = url.match(regExp);
      if (match && match[1].length === 11) {
        return match[1];
      }
      return null;
    }

    function renderVideos() {
      videoList.innerHTML = "";
      videos.forEach((v, index) => {
        var div = document.createElement("div");
        div.className = "video-item";

        var youtubeId = extractYouTubeId(v.url || "default-url");
        if (youtubeId) {
          var img = document.createElement("img");
          img.src = "https://img.youtube.com/vi/" + youtubeId + "/hqdefault.jpg";
          img.alt = "YouTube Thumbnail";
          div.appendChild(img);
        }

        var linkInfo = document.createElement("div");
        linkInfo.className = "link-info";
        
        var title = document.createElement("h4");
        title.innerHTML = v.title;
        linkInfo.appendChild(title);
        
        var controls = document.createElement("div");
        controls.className = "video-controls";
        
        if (v.locked) {
          var unlockBtn = document.createElement("button");
          unlockBtn.innerHTML = "Unlock";
          unlockBtn.style.backgroundColor = '#28a745';
          unlockBtn.onclick = () => {
            var pin = prompt("Enter PIN to unlock:");
            if (pin === ADMIN_PIN) {
              v.locked = false;
              saveVideos();
              renderVideos();
            } else {
              alert("Incorrect PIN.");
            }
          };
          controls.appendChild(unlockBtn);
        } else {
          var playBtn = document.createElement("a");
          playBtn.href = v.url;
          playBtn.target = "_blank";
          playBtn.innerHTML = "▶ Play";
          playBtn.style.backgroundColor = '#17a2b8';
          controls.appendChild(playBtn);
        }

        if (adminMode) {
          var deleteBtn = document.createElement("button");
          deleteBtn.innerHTML = "Delete";
          deleteBtn.style.backgroundColor = '#dc3545';
          deleteBtn.onclick = () => {
            var pin = prompt("Enter PIN to delete:");
            if (pin === ADMIN_PIN) {
              if (confirm("Are you sure you want to delete this video?")) {
                videos.splice(index, 1);
                saveVideos();
                renderVideos();
              }
            } else {
              alert("Incorrect PIN.");
            }
          };
          controls.appendChild(deleteBtn);
        }
        
        linkInfo.appendChild(controls);
        div.appendChild(linkInfo);
        videoList.appendChild(div);
      });
    }

    form.onsubmit = function(e) {
      e.preventDefault();
      var url = document.getElementById("url").value.trim();
      var title = document.getElementById("title").value || "Untitled";
      var vid = extractYouTubeId(url);
      if (!vid) {
        alert("Invalid YouTube URL.");
        return;
      }
      
      if (!url.startsWith("http://") && !url.startsWith("https://")) {
        url = "https://" + url;
      }

      videos.unshift({id: new Date().getTime(), url: url, vid: vid, title: title, locked: true});
      saveVideos();
      renderVideos();
      form.reset();
    };

    adminBtn.onclick = () => {
      var pin = prompt("Enter PIN to access admin controls:");
      if (pin === ADMIN_PIN) {
        adminMode = !adminMode;
        adminPanel.style.display = adminMode ? 'block' : 'none';
        adminBtn.innerHTML = adminMode ? 'Exit Admin' : 'Admin';
        renderVideos();
      } else {
        alert("Incorrect PIN.");
      }
    };

    changePinBtn.onclick = () => {
      var newPin = newPinInput.value;
      if (newPin.length > 0) {
        var confirmPin = prompt("Re-enter new PIN to confirm:");
        if (newPin === confirmPin) {
          ADMIN_PIN = newPin;
          savePin();
          alert("PIN successfully changed!");
          newPinInput.value = '';
          adminMode = false;
          adminPanel.style.display = 'none';
          adminBtn.innerHTML = 'Admin';
          renderVideos();
        } else {
          alert("PINs do not match.");
        }
      } else {
        alert("Please enter a new PIN.");
      }
    };

    renderVideos();
  </script>
</body>
</html>
