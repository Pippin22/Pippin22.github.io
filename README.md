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
    #videoList div { background:#fff; margin:10px 0; padding:10px; border:1px solid #ccc; }
    button, input { padding:8px; margin-top:5px; }
    .locked { color:red; font-weight:bold; }
  </style>
</head>
<body>
  <h1>🎒 Kids Video Locker</h1>

  <!-- Admin login -->
  <div id="loginSection">
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
    var STORAGE_KEY = "kids_videos_v2";
    var videos = [];

    // Load saved
    try {
      var saved = localStorage.getItem(STORAGE_KEY);
      if (saved) videos = JSON.parse(saved);
    } catch(e){ videos = []; }

    function save() {
      localStorage.setItem(STORAGE_KEY, JSON.stringify(videos));
    }

    function extractYouTubeId(url) {
      if (url.indexOf("youtu.be/") !== -1) return url.split("youtu.be/")[1].split(/[?&]/)[0];
      if (url.indexOf("watch?v=") !== -1) return url.split("watch?v=")[1].split("&")[0];
      if (url.indexOf("shorts/") !== -1) return url.split("shorts/")[1].split(/[?&]/)[0];
      return null;
    }

    function render() {
      var container = document.getElementById("videoList");
      container.innerHTML = "";
      for (var i=0;i<videos.length;i++) {
        var v = videos[i];
        var div = document.createElement("div");
        var title = document.createElement("div");
        title.innerHTML = "<b>" + v.title + "</b>";
        div.appendChild(title);

        if (v.locked) {
          var lockMsg = document.createElement("div");
          lockMsg.innerHTML = "🔒 Locked";
          lockMsg.className = "locked";
          div.appendChild(lockMsg);

          var btn = document.createElement("button");
          btn.innerHTML = "Unlock";
          btn.onclick = (function(id){
            return function() {
              var pin = prompt("Enter unlock PIN:");
              if (pin === PASSCODE) {
                for (var k=0;k<videos.length;k++){ if(videos[k].id==id) videos[k].locked=false; }
                save(); render();
              } else { alert("Wrong PIN"); }
            };
          })(v.id);
          div.appendChild(btn);
        } else {
          var link = document.createElement("a");
          link.href = "https://www.youtube.com/watch?v=" + v.vid;
          link.target = "_blank";
          link.innerHTML = "▶ Play Video";
          div.appendChild(link);
        }

        container.appendChild(div);
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