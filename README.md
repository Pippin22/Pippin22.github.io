
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Uchoosetube</title>
  <style>
    body {
      font-family: 'Comic Sans MS', cursive, sans-serif;
      margin: 0;
      padding: 0;
      background: linear-gradient(to bottom, #ffecd2, #fcb69f);
      text-align: center;
    }
    h1 {
      font-size: 3rem;
      margin-top: 2rem;
      color: #ff1493;
      text-shadow: 2px 2px #fff;
    }
    #intro {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      background: linear-gradient(135deg, #f6d365 0%, #fda085 100%);
    }
    #intro p {
      font-size: 1.5rem;
      margin-bottom: 2rem;
      color: #333;
    }
    #openVaultBtn {
      background: #ff69b4;
      color: white;
      border: none;
      border-radius: 20px;
      padding: 20px 40px;
      font-size: 2rem;
      cursor: pointer;
      box-shadow: 0 6px #c71585;
      transition: transform 0.2s;
    }
    #openVaultBtn:hover {
      transform: scale(1.1);
    }
    #pinOverlay {
      position: fixed;
      top: 0; left: 0; right: 0; bottom: 0;
      background: rgba(0,0,0,0.7);
      display: none;
      justify-content: center;
      align-items: center;
      z-index: 1000;
    }
    #pinBox {
      background: white;
      padding: 20px;
      border-radius: 20px;
      text-align: center;
      width: 300px;
    }
    #pinDisplay {
      font-size: 2rem;
      margin-bottom: 15px;
      letter-spacing: 10px;
    }
    .pinBtn {
      width: 60px; height: 60px;
      margin: 8px;
      font-size: 1.5rem;
      border-radius: 50%;
      border: none;
      background: #87ceeb;
      color: white;
      cursor: pointer;
      box-shadow: 0 4px #4682b4;
      transition: transform 0.2s;
    }
    .pinBtn:hover { transform: scale(1.1); }
    #foldersContainer, #videoContainer, #settingsContainer {
      display: none;
      flex-wrap: wrap;
      justify-content: center;
      gap: 15px;
      padding: 20px;
    }
    .folderCard {
      width: 140px; height: 120px;
      background: #fff176;
      border-radius: 12px;
      box-shadow: 0 4px 8px rgba(0,0,0,0.2);
      display: flex; flex-direction: column;
      align-items: center; justify-content: center;
      cursor: pointer;
      font-size: 1.2rem;
    }
    .videoCard {
      background: white;
      border-radius: 12px;
      overflow: hidden;
      width: 300px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.2);
      position: relative;
    }
    .videoCard iframe { width: 100%; height: 200px; border: none; }
    .removeBtn {
      position: absolute;
      top: 5px; right: 5px;
      background: red;
      color: white;
      border: none;
      padding: 5px 10px;
      border-radius: 6px;
      cursor: pointer;
    }
    .fixed-button {
      position: fixed;
      bottom: 20px;
      width: 70px;
      height: 70px;
      border-radius: 50%;
      color: white;
      font-size: 2.5rem;
      border: none;
      cursor: pointer;
    }
    #addBtn {
      right: 20px;
      background: #4caf50;
      display: none;
    }
    #settingsBtn {
      right: 100px;
      background: #607d8b;
      display: none;
    }
    #adminSection {
      display: none;
      position: fixed;
      top: 50%;
      left: 50%;
      transform: translate(-50%, -50%);
      background: white;
      padding: 20px;
      border-radius: 12px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.2);
      z-index: 100;
    }
    #adminSection input { margin-bottom: 10px; }
    .close-btn {
      position: absolute;
      top: 10px; right: 10px;
      background: #ccc;
      border: none;
      border-radius: 50%;
      width: 30px;
      height: 30px;
      cursor: pointer;
    }
  </style>
</head>
<body>
  <div id="intro">
    <h1>🌈 Uchoosetube ☀️</h1>
    <p>A safe and fun place to watch only what YOU choose 🎬</p>
    <button id="openVaultBtn">🔓 Open My Vault</button>
  </div>
  <div id="pinOverlay">
    <div id="pinBox">
      <h2 id="pinMessage">Enter Your PIN</h2>
      <div id="pinDisplay">____</div>
      <div>
        <button class="pinBtn">1</button>
        <button class="pinBtn">2</button>
        <button class="pinBtn">3</button><br>
        <button class="pinBtn">4</button>
        <button class="pinBtn">5</button>
        <button class="pinBtn">6</button><br>
        <button class="pinBtn">7</button>
        <button class="pinBtn">8</button>
        <button class="pinBtn">9</button><br>
        <button class="pinBtn">0</button>
      </div>
    </div>
  </div>
  <div id="foldersContainer"></div>
  <div id="videoContainer"></div>
  <div id="settingsContainer">
    <h2>Settings</h2>
    <p>Manage app settings and account details.</p>
    <button id="changePinBtn">Change PIN</button>
    <button id="donateBtn" onclick="window.open('https://www.paypal.com/donate/?business=steve_parmenter@hotmail.com','_blank')">💛 Donate to PayPal</button>
  </div>
  <button id="addBtn" class="fixed-button">+</button>
  <button id="settingsBtn" class="fixed-button">⚙️</button>

  <div id="adminSection">
    <button class="close-btn" id="closeAdminBtn">X</button>
    <h3>Add Video</h3>
    <form id="videoForm">
      <input type="text" id="url" placeholder="YouTube link" required><br>
      <input type="text" id="title" placeholder="Title"><br>
      <input type="text" id="folder" placeholder="Folder name"><br>
      <button type="submit">Add Video</button>
    </form>
  </div>
  <script>
    var STORAGE_KEY = "kids_videos_with_folders";
    var PIN_KEY = "vault_pin";
    var videos = JSON.parse(localStorage.getItem(STORAGE_KEY) || "[]");
    var enteredPin = "";
    var pinMode = "enter"; // can be "vault", "settings", or "change"

    function save(){
      localStorage.setItem(STORAGE_KEY, JSON.stringify(videos));
    }

    function extractYouTubeId(url){
      if (url.includes("youtu.be/")) return url.split("youtu.be/")[1].split(/[?&]/)[0];
      if (url.includes("watch?v=")) return url.split("watch?v=")[1].split("&")[0];
      if (url.includes("shorts/")) return url.split("shorts/")[1].split(/[?&]/)[0];
      return null;
    }

    function renderFolders(){
      var container=document.getElementById("foldersContainer");
      container.innerHTML="";
      var folders={};
      videos.forEach(v=>{ if(!folders[v.folder]) folders[v.folder]=[]; });
      for (var folderName in folders){
        var card=document.createElement("div");
        card.className="folderCard";
        card.textContent="📁 "+folderName;
        card.onclick=function(name){return ()=>showFolderContents(name);}(folderName);
        container.appendChild(card);
      }
    }

    function showFolderContents(folderName){
      document.getElementById("foldersContainer").style.display="none";
      var container=document.getElementById("videoContainer");
      container.style.display="flex";
      container.innerHTML="";
      videos.filter(v=>v.folder===folderName).forEach(v=>{
        var card=document.createElement("div");
        card.className="videoCard";
        var iframe=document.createElement("iframe");
        iframe.src="https://www.youtube-nocookie.com/embed/"+v.vid+"?rel=0&modestbranding=1&controls=1";
        card.appendChild(iframe);
        var removeBtn=document.createElement("button");
        removeBtn.className="removeBtn";
        removeBtn.textContent="X";
        removeBtn.onclick=function(id){return ()=>{videos=videos.filter(x=>x.id!==id);save();showFolderContents(folderName);};}(v.id);
        card.appendChild(removeBtn);
        container.appendChild(card);
      });
    }

    document.getElementById("videoForm").onsubmit=function(e){
      e.preventDefault();
      var url=document.getElementById("url").value;
      var title=document.getElementById("title").value||"Untitled";
      var folder=document.getElementById("folder").value||"General";
      var vid=extractYouTubeId(url);
      if(!vid){alert("Invalid YouTube URL");return;}
      videos.unshift({id:Date.now(),vid:vid,title:title,folder:folder});
      save();
      document.getElementById("adminSection").style.display="none";
      renderFolders();
    }

    // New functions to manage different sections
    function accessVault() {
      document.getElementById("intro").style.display = "none";
      document.getElementById("foldersContainer").style.display = "flex";
      document.getElementById("addBtn").style.display = "block";
      document.getElementById("settingsBtn").style.display = "block";
      renderFolders();
    }

    function accessSettings() {
      document.getElementById("intro").style.display = "none";
      document.getElementById("settingsContainer").style.display = "flex";
      document.getElementById("addBtn").style.display = "block";
      document.getElementById("settingsBtn").style.display = "block";
    }

    function closePinOverlay(){
      document.getElementById("pinOverlay").style.display="none";
    }

    // PIN keypad
    document.querySelectorAll(".pinBtn").forEach(btn=>{
      btn.onclick=()=>{
        enteredPin+=btn.textContent;
        document.getElementById("pinDisplay").textContent=enteredPin.padEnd(4,"_");
        if(enteredPin.length===4){
          var savedPin=localStorage.getItem(PIN_KEY);
          if(pinMode==="change"){
            localStorage.setItem(PIN_KEY, enteredPin);
            closePinOverlay();
            alert("PIN changed successfully!");
          } else if(pinMode === "vault"){
            if(!savedPin){
              localStorage.setItem(PIN_KEY, enteredPin);
              closePinOverlay();
              accessVault();
            } else if(enteredPin === savedPin){
              closePinOverlay();
              accessVault();
            } else {
              alert("Wrong PIN");
              enteredPin = "";
              document.getElementById("pinDisplay").textContent="____";
            }
          } else if (pinMode === "settings") {
            if (enteredPin === savedPin) {
              closePinOverlay();
              accessSettings();
            } else {
              alert("Wrong PIN");
              enteredPin = "";
              document.getElementById("pinDisplay").textContent="____";
            }
          }
        }
      }
    });

    document.getElementById("openVaultBtn").onclick=function(){
      pinMode = "vault";
      document.getElementById("pinOverlay").style.display="flex";
      document.getElementById("settingsContainer").style.display="none";
      document.getElementById("foldersContainer").style.display="none";
      enteredPin="";
      document.getElementById("pinDisplay").textContent="____";
      document.getElementById("pinMessage").textContent = localStorage.getItem(PIN_KEY)?"Enter Your PIN":"Create a PIN";
    };

    document.getElementById("changePinBtn").onclick=function(){
      pinMode = "change";
      document.getElementById("pinOverlay").style.display="flex";
      document.getElementById("settingsContainer").style.display="none";
      document.getElementById("foldersContainer").style.display="none";
      enteredPin="";
      document.getElementById("pinDisplay").textContent="____";
      document.getElementById("pinMessage").textContent = "Create a new PIN";
    };

    document.getElementById("settingsBtn").onclick=function(){
      pinMode = "settings";
      document.getElementById("pinOverlay").style.display="flex";
      document.getElementById("foldersContainer").style.display="none";
      document.getElementById("settingsContainer").style.display="none";
      enteredPin="";
      document.getElementById("pinDisplay").textContent="____";
      document.getElementById("pinMessage").textContent = localStorage.getItem(PIN_KEY)?"Enter Your PIN to access settings":"Create a PIN";
    };

    // Add Video button logic
    document.getElementById("addBtn").onclick=function(){
      document.getElementById("adminSection").style.display="block";
    };
    document.getElementById("closeAdminBtn").onclick=function(){
      document.getElementById("adminSection").style.display="none";
    };
  </script>
</body>
