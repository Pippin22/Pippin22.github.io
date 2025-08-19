<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Kids Video Locker</title>
  <style>
    body { font-family: Arial, sans-serif; background:#f0f0f0; margin:0; padding:1rem; }
    h1 { text-align:center; }
    #adminSection { display:none; margin-bottom:1rem; }
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
    button, input { padding:8px; margin-top:5px; }
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
      <button type="submit">Add Video</button>
    </form>
  </div>

  <div id="videoList"></div>

  <script>
    var PASSCODE = "9999";
    var STORAGE_KEY = "kids_videos_v5";
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
      var container = document.getElementById("videoList");
      container.innerHTML = "";
      for (var i=0;i<videos.length;i++) {
        var v = videos[i];
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
          overlay.onclick = (function(id){
            return function() {
              var pin = prompt("Enter unlock PIN:");
              if (pin === PASSCODE) {
                for (var k=0;k<videos.length;k++){ if(videos[k].id==id) videos[k].locked=false; }
                save(); render();
              } else { alert("Wrong PIN"); }
            };
          })(v.id);
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

        container.appendChild(card);
      }
    }

    // Add video form
    document.getElementById("videoForm").onsubmit = function(e){
      e.preventDefault();
      var url = document.getElementById("url").value;
      var title = document.getElementById("title").value || "Untitled";
      var vid = extractYouTubeId(url);
      if (!vid) { alert("Invalid YouTube URL"); return; }
      videos.unshift({id: new Date().getTime(), vid: vid, title: title, locked: true});
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