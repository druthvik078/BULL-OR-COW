<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Bulls and Cows with Names & Timer</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background: #f9f9f9;
      text-align: center;
      padding: 30px;
    }
    h1 {
      color: #444;
    }
    .section {
      margin: 20px 0;
    }
    input, button {
      padding: 10px;
      font-size: 16px;
      margin: 5px;
    }
    .log {
      text-align: left;
      max-width: 500px;
      margin: 20px auto;
      background: #fff;
      padding: 15px;
      border-radius: 5px;
      box-shadow: 0 0 5px rgba(0,0,0,0.1);
    }
    .winner {
      font-weight: bold;
      color: green;
    }
    #timer {
      font-size: 18px;
      color: red;
      margin-top: 10px;
    }
  </style>
</head>
<body>

<h1>🐂 Bulls and 🐄 Cows - Multiplayer + Timer</h1>

<div class="section">
  <p><strong>Player 1 Name:</strong></p>
  <input type="text" id="name1" placeholder="Enter name">
  <p><strong>Player 1 Secret Number:</strong></p>
  <input type="password" id="secret1" maxlength="4" placeholder="4-digit secret">
</div>

<div class="section">
  <p><strong>Player 2 Name:</strong></p>
  <input type="text" id="name2" placeholder="Enter name">
  <p><strong>Player 2 Secret Number:</strong></p>
  <input type="password" id="secret2" maxlength="4" placeholder="4-digit secret">
</div>

<button onclick="startGame()">Start Game</button>

<div class="section" id="gameArea" style="display:none;">
  <h3 id="turnTitle">Player 1's Turn</h3>
  <div id="timer">Time Left: 20s</div>
  <input type="text" id="guess" maxlength="4" placeholder="Enter your guess">
  <button onclick="submitGuess()">Guess</button>

  <div id="result"></div>
  <div class="log" id="log"></div>
</div>

<script>
  let secret1 = '', secret2 = '';
  let name1 = '', name2 = '';
  let currentPlayer = 1;
  let gameStarted = false;
  let timer, timeLeft = 20;

  function isValidSecret(secret) {
    return /^\d{4}$/.test(secret) && new Set(secret).size === 4;
  }

  function startGame() {
    secret1 = document.getElementById("secret1").value.trim();
    secret2 = document.getElementById("secret2").value.trim();
    name1 = document.getElementById("name1").value.trim() || "Player 1";
    name2 = document.getElementById("name2").value.trim() || "Player 2";

    if (!isValidSecret(secret1) || !isValidSecret(secret2)) {
      alert("Both players must enter valid 4-digit numbers with unique digits.");
      return;
    }

    document.getElementById("gameArea").style.display = "block";
    document.getElementById("turnTitle").innerText = `${name1}'s Turn`;
    document.getElementById("result").innerText = '';
    document.getElementById("log").innerText = '';
    gameStarted = true;
    startTimer();
  }

  function startTimer() {
    clearInterval(timer);
    timeLeft = 20;
    document.getElementById("timer").innerText = `Time Left: ${timeLeft}s`;
    timer = setInterval(() => {
      timeLeft--;
      document.getElementById("timer").innerText = `Time Left: ${timeLeft}s`;
      if (timeLeft <= 0) {
        clearInterval(timer);
        autoPassTurn();
      }
    }, 1000);
  }

  function autoPassTurn() {
    let currentName = currentPlayer === 1 ? name1 : name2;
    const log = document.getElementById("log");
    const logEntry = document.createElement("div");
    logEntry.textContent = `${currentName} ran out of time! Turn skipped.`;
    log.prepend(logEntry);
    switchTurn();
  }

  function submitGuess() {
    if (!gameStarted) return;

    let guess = document.getElementById("guess").value.trim();
    if (!/^\d{4}$/.test(guess) || new Set(guess).size !== 4) {
      document.getElementById("result").innerText = "Enter a valid 4-digit number with unique digits.";
      return;
    }

    clearInterval(timer);

    let opponentSecret = currentPlayer === 1 ? secret2 : secret1;
    let currentName = currentPlayer === 1 ? name1 : name2;

    let bulls = 0, cows = 0;
    for (let i = 0; i < 4; i++) {
      if (guess[i] === opponentSecret[i]) {
        bulls++;
      } else if (opponentSecret.includes(guess[i])) {
        cows++;
      }
    }

    const log = document.getElementById("log");
    const logEntry = document.createElement("div");
    logEntry.textContent = `${currentName} guessed: ${guess} — 🐂 Bulls: ${bulls}, 🐄 Cows: ${cows}`;
    log.prepend(logEntry);

    if (bulls === 4) {
      document.getElementById("result").innerHTML = `<span class="winner">🎉 ${currentName} wins! The secret was ${opponentSecret}</span>`;
      gameStarted = false;
      clearInterval(timer);
      return;
    }

    switchTurn();
  }

  function switchTurn() {
    currentPlayer = currentPlayer === 1 ? 2 : 1;
    let nextName = currentPlayer === 1 ? name1 : name2;
    document.getElementById("turnTitle").innerText = `${nextName}'s Turn`;
    document.getElementById("guess").value = '';
    document.getElementById("result").innerText = '';
    startTimer();
  }
</script>

</body>
</html>
