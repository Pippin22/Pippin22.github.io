<!DOCTYPE html><html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Uchoosetube</title>
  <style>
    body { margin:0; font-family: 'Comic Sans MS', cursive; }/* Opening Screen */
#openingScreen {
  display:flex; flex-direction:column; justify-content:center; align-items:center;
  height:100vh; text-align:center;
  background: linear-gradient(to top right, #ffccff, #ccffff, #ffffcc);
}
#openingScreen h1 {
  font-size:48px; color:#ff33cc; text-shadow:2px 2px #fff;
}
#openingScreen p {
  font-size:24px; color:#333; max-width:500px;
}
#openVaultBtn {
  margin-top:20px; font-size:28px; padding:20px 40px;
  background:#ffcc00; color:#000; border:none;
  border-radius:20px; cursor:pointer;
  box-shadow:0 6px 12px rgba(0,0,0,0.3);
}

/* App Containers */
#app { display:none; padding:1rem; background:#f0f0f0; min-height:100vh; }
h1 { text-align:center; margin-bottom:1rem; color:#333; }
#foldersContainer, #videoContainer {
  display:flex; flex-wrap:wrap; gap:15px; justify-content:center;
}
.folderCard {
  width:120px; height:100px; background:#fff;
  border-radius:12px; box-shadow:0 4px 8px rgba(0,0,0,0.1);
  display:flex; flex-direction:column; justify-content:center; align-items:center;
  cursor

