body {
  margin: 0;
  font-family: 'Comic Sans MS', cursive, sans-serif;
  text-align: center;
}

/* 🌈 Vault Screen Styling */
.vault-screen {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background: linear-gradient(135deg, #ff9a9e, #fad0c4, #fbc2eb, #a6c1ee, #84fab0, #8fd3f4);
  background-size: 400% 400%;
  animation: rainbow 12s ease infinite;
}

@keyframes rainbow {
  0%{background-position:0% 50%}
  50%{background-position:100% 50%}
  100%{background-position:0% 50%}
}

.vault-title {
  font-size: 4em;
  margin-bottom: 20px;
  color: #fff;
  text-shadow: 3px 3px 6px rgba(0,0,0,0.4);
}

.vault-subtitle {
  font-size: 2em;
  margin-bottom: 40px;
  color: #ffffcc;
}

.vault-btn {
  font-size: 2em;
  padding: 20px 40px;
  margin-top: 20px;
  border-radius: 50px;
  background: gold;
  border: 4px solid orange;
  cursor: pointer;
  box-shadow: 0 8px 16px rgba(0,0,0,0.3);
  transition: transform 0.2s, background 0.2s;
}

.vault-btn:hover {
  transform: scale(1.1);
  background: #fff176;
}

/* 🔢 PIN Entry */
.pin-entry {
  margin-top: 30px;
}

.pin-entry p {
  font-size: 1.5em;
  margin-bottom: 10px;
  color: #fff;
}

.pin-input {
  font-size: 2em;
  text-align: center;
  padding: 10px;
  border-radius: 15px;
  border: 2px solid #ffcc80;
  margin-bottom: 15px;
}