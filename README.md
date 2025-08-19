<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Kids Video Locker</title>
  <style>
    body { font-family: Arial, sans-serif; background:#f0f0f0; margin:0; padding:1rem; }
    h1 { text-align:center; margin-bottom:1rem; }
    #authSection, #appSection { display:none; }
    .formBox {
      max-width:300px; margin:50px auto; padding:20px;
      background:white; border-radius:10px; box-shadow:0 4px 8px rgba(0,0,0,0.2);
    }
    .formBox input { width:100%; margin:5px 0; padding:8px; }
    .formBox button { width:100%; padding:10px; margin-top:10px; }
    #foldersContainer, #videoContainer {
      display: flex; flex-wrap: wrap; gap:15px; justify-content:center;
    }
    .folderCard {
      width:120px; height:100px;
      background:#fff; border-radius:12px;
      box-shadow:0 4px 8px rgba(0,0,0,0.1);
      display:flex; flex-direction:column;
      justify-content:center; align-items:center;
      cursor:pointer; transition:transform 0.2s;
    }
    .folderCard:hover { transform:scale(1.05); }
    .videoCard {
      background:#fff; border:1px solid #ccc;
      border-radius:6px; overflow:hidden; width:300px;
      position:relative;
    }
    .videoCard img, .videoCard iframe { width:100%; display:block; }
    .videoCard .title { padding:5px; font-size:14px; font-weight:bold; text-align:center; }
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
      cursor:pointer; display:flex; justify-content:center; align-items:center;
    }
    #homeBtn {
      position:fixed; top:10px; left:10px;
      background:#2196F3; color:white;
      border:none; padding:8px 14px;
      border-radius:6px; font-size:14px;
      cursor:pointer; display:none;
    }
    #logoutBtn {
      position:fixed; top:10px; right:10px;
      background:#FF5722; color:white;
      border:none; padding:8px 14px;
      border-radius:6px; font-size:14px;
      cursor:pointer; display:none;
    }
  </style>
</head>
<body>
  <h1>🎒 Kids Video Locker</h1>

  <!-- AUTH SECTION -->
  <div id="authSection">
    <div class="formBox">
      <h3>Login</h3>
      <input type="email" id="loginEmail" placeholder="Email">
      <input type="password" id="loginPassword" placeholder="Password">
      <button onclick="login()">Login</button>
      <hr>
      <h3>Register</h3>
      <input type="email" id="registerEmail" placeholder="Email">
      <input type="password" id="registerPassword" placeholder="Password">
      <button onclick="register()">Register</button>
    </div>
  </div>

  <!-- APP SECTION -->
  <div id="appSection">
    <button id="homeBtn" onclick="showAllVideos()">🏠 Home</button>
    <button id="logoutBtn" onclick="logout()">🚪 Logout</button>

    <div id="adminSection" style="display:none;">
      <form id="videoForm">
        <input type="text" id="url" placeholder="YouTube link" required style="width:100%;"><br>
        <input type="text" id="title" placeholder="Title (optional)" style="width:100%;"><br>
        <input type="text" id="folder" placeholder="Folder name (optional)" style="width:100%;"><br>
        <button type="submit">Add Video</button>
      </form>
    </div>

    <div id="videoContainer"></div>
    <div id="foldersContainer" style="display:none;"></div>

    <button id="addBtn" onclick="showAddForm()">+</button>
  </div>

  <!-- Firebase SDKs -->
  <script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-app-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-analytics-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-auth-compat.js"></script>
  <script src="https://www.gstatic.com/firebasejs/10.12.2/firebase-firestore-compat.js"></script>

  <script>
    // Firebase Config
    const firebaseConfig = {
      apiKey: "AIzaSyDqm5nAEcKJWLwFWJugG-5CeMNV1yeZxhk",
      authDomain: "uchoosetube-46d9d.firebaseapp.com",
      projectId: "uchoosetube-46d9d",
      storageBucket: "uchoosetube-46d9d.firebasestorage.app",
      messagingSenderId: