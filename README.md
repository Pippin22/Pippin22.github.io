<script>
  var STORAGE_KEY = "kids_videos_with_folders";
  var PIN_KEY = "vault_pin";
  var videos = [];

  try {
    var saved = localStorage.getItem(STORAGE_KEY);
    if (saved) videos = JSON.parse(saved);
  } catch(e){ videos = []; }

  function save() { localStorage.setItem(STORAGE_KEY, JSON.stringify(videos)); }

  // 🔑 Open Vault with PIN creation on first run
  function openVault() {
    var savedPin = localStorage.getItem(PIN_KEY);

    if (!savedPin) {
      // First-time setup
      var newPin = prompt("Create a new PIN for your vault:");
      if (newPin && newPin.length >= 4) {
        localStorage.setItem(PIN_KEY, newPin);
        alert("Your vault PIN has been set. Don’t forget it!");
        document.getElementById("vaultScreen").style.display = "none";
        document.getElementById("appContent").style.display = "block";
        showFolders();
      } else {
        alert("PIN must be at least 4 digits.");
      }
    } else {
      // Normal unlock
      var entered = prompt("Enter your PIN:");
      if (entered === savedPin) {
        document.getElementById("vaultScreen").style.display = "none";
        document.getElementById("appContent").style.display = "block";
        showFolders();
      } else {
        alert("Wrong PIN");
      }
    }
  }

  // Extract YouTube Video ID
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

  // Show videos inside folder
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
      // ✅ No external YouTube link, embedded only
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
            save();