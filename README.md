<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Bulls and Cows Setup</title>
  <style>
    body { font-family: Arial; text-align: center; padding: 40px; background: #f0f0f0; }
    input { padding: 10px; font-size: 16px; margin: 10px; }
    button { padding: 10px 20px; font-size: 18px; }
  </style>
</head>
<body>

  <h1>🎮 Bulls and Cows Game Setup</h1>

  <label><strong>Digit Length (3-6):</strong></label><br>
  <input type="number" id="length" value="4" min="3" max="6"><br><br>

  <label><strong>Player 1 Name:</strong></label><br>
  <input type="text" id="name1" placeholder="Player 1"><br><br>

  <label><strong>Player 2 Name:</strong></label><br>
  <input type="text" id="name2" placeholder="Player 2"><br><br>

  <button onclick="start()">Continue</button>

  <script>
    function start() {
      const len = parseInt(document.getElementById("length").value);
      const name1 = document.getElementById("name1").value || "Player 1";
      const name2 = document.getElementById("name2").value || "Player 2";

      if (len < 3 || len > 6) {
        alert("Digit length must be between 3 and 6.");
        return;
      }

      localStorage.setItem("digitLength", len);
      localStorage.setItem("name1", name1);
      localStorage.setItem("name2", name2);
      localStorage.removeItem("secret1");
      localStorage.removeItem("secret2");
      localStorage.setItem("turn", "1");

      window.location.href = "player1.html";
    }
  </script>

</body>
</html>

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Player 1 Turn</title>
  <style>
    body { font-family: Arial; padding: 30px; text-align: center; background: #eef; }
    .hidden { display: none; }
    input, button { padding: 10px; font-size: 16px; margin: 5px; }
  </style>
</head>
<body>

  <h2>👤 Player 1 Page</h2>
  <p id="nameLabel"></p>

  <div id="secretArea">
    <p>Enter your secret (e.g., 1 3 5 7):</p>
    <input type="text" id="secretInput" placeholder="space-separated digits">
    <button onclick="saveSecret()">Submit Secret</button>
  </div>

  <div id="gameArea" class="hidden">
    <h3>Guess Player 2's Secret</h3>
    <p>Time left: <span id="timer">20</span>s</p>
    <input type="text" id="guessInput" placeholder="Your guess">
    <button onclick="submitGuess()">Submit Guess</button>
    <div id="result"></div>
    <div id="log"></div>
  </div>

  <script>
    const name1 = localStorage.getItem("name1");
    const name2 = localStorage.getItem("name2");
    const digitLength = parseInt(localStorage.getItem("digitLength"));

    document.getElementById("nameLabel").textContent = `Hello, ${name1}!`;

    let timer, timeLeft = 20;

    function parseInput(str) {
      return str.trim().split(/\s+/).map(Number);
    }

    function isValid(arr) {
      return arr.length === digitLength &&
        new Set(arr).size === digitLength &&
        arr.every(n => n >= 1 && n <= 9);
    }

    function saveSecret() {
      const val = parseInput(document.getElementById("secretInput").value);
      if (!isValid(val)) {
        alert(`Enter ${digitLength} unique numbers from 1 to 9.`);
        return;
      }
      localStorage.setItem("secret1", JSON.stringify(val));
      document.getElementById("secretArea").style.display = "none";
      document.getElementById("gameArea").classList.remove("hidden");
      startTimer();
    }

    function startTimer() {
      timeLeft = 20;
      document.getElementById("timer").textContent = timeLeft;
      timer = setInterval(() => {
        timeLeft--;
        document.getElementById("timer").textContent = timeLeft;
        if (timeLeft <= 0) {
          clearInterval(timer);
          autoPass();
        }
      }, 1000);
    }

    function autoPass() {
      document.getElementById("result").textContent = "⏱ Time’s up! Passing turn.";
      setTimeout(() => {
        localStorage.setItem("turn", "2");
        window.location.href = "player2.html";
      }, 2000);
    }

    function submitGuess() {
      clearInterval(timer);
      const guess = parseInput(document.getElementById("guessInput").value);
      const secret2 = JSON.parse(localStorage.getItem("secret2") || "[]");
      if (!isValid(guess) || secret2.length !== digitLength) {
        alert("Invalid guess or opponent has not set secret yet.");
        return;
      }

      let bulls = 0, cows = 0;
      for (let i = 0; i < digitLength; i++) {
        if (guess[i] === secret2[i]) bulls++;
        else if (secret2.includes(guess[i])) cows++;
      }

      document.getElementById("log").innerHTML += `<p>Guessed: ${guess.join(" ")} → 🐂 ${bulls}, 🐄 ${cows}</p>`;
      if (bulls === digitLength) {
        document.getElementById("result").innerHTML = `<strong>🎉 You Win!</strong>`;
        return;
      }

      localStorage.setItem("turn", "2");
      setTimeout(() => window.location.href = "player2.html", 1000);
    }
  </script>

</body>
</html>
