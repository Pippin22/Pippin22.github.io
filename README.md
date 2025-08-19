
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
      box-shadow:0 4px 8px rgba