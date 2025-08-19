
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>🎒 Kids Video Locker</title>
  <style>
    body { font-family: Arial, sans-serif; background:#f0f0f0; margin:0; padding:1rem; }
    h1 { text-align:center; margin-bottom:1rem; }

    /* Auth Box */
    #authSection, #appSection { display:none; }
    .formBox {
      max-width:300px; margin:50px auto; padding:20px;
      background:white; border-radius:10px; box-shadow:0 4px 8px rgba(0,0,0,0.2);
    }
    .formBox input { width:100%; margin:5px 0; padding:8px; }
    .formBox button { width:100%; padding:10px; margin-top:10px; }

    /* App UI */
    #foldersContainer, #videoContainer {
      display:grid; grid-template-columns:repeat(auto-fill, minmax(150px,1fr));
      gap:15px; margin-top:20px;
    }
    .folder {
      background:white; border-radius:8px; padding:10px; text-align:center;
      box-shadow:0 2px 5px rgba(0,0,0,0.2); cursor:pointer;