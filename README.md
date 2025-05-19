<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1" />
<title>Catch the Hearts Game ❤️</title>
<style>
  body, html {
    margin: 0; padding: 0; overflow: hidden; background: #ffe6f0; font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    display: flex; flex-direction: column; align-items: center; justify-content: center; height: 100vh;
  }
  #gameArea {
    position: relative;
    width: 100vw;
    height: 80vh;
    background: #ffbad2;
    overflow: hidden;
    border-radius: 12px;
    box-shadow: 0 0 15px rgba(255, 0, 100, 0.6);
  }
  .heart {
    position: absolute;
    top: 0;
    width: 40px;
    height: 40px;
    cursor: pointer;
    user-select: none;
  }
  .heart-shape {
    position: relative;
    width: 40px;
    height: 36px;
    background: #ff2d55;
    transform: rotate(-45deg);
    border-radius: 10px 10px 0 0;
  }
  .heart-shape::before,
  .heart-shape::after {
    content: "";
    position: absolute;
    width: 40px;
    height: 36px;
    background: #ff2d55;
    border-radius: 50%;
  }
  .heart-shape::before {
    top: -18px;
    left: 0;
  }
  .heart-shape::after {
    left: 18px;
    top: 0;
  }
  #scoreBoard {
    margin: 10px;
    font-size: 1.3rem;
    color: #b8003b;
    font-weight: bold;
  }
  #livesDisplay {
    margin: 5px;
    font-size: 1.5rem;
    color: #ff2d55;
  }
  #compliment {
    margin: 10px;
    font-size: 1.2rem;
    color: #b8003b;
    font-weight: 600;
    height: 24px;
    min-height: 24px;
  }
  #gameOver {
    position: absolute;
    top: 40%;
    left: 50%;
    transform: translate(-50%, -50%);
    background: #fff0f6cc;
    border: 2px solid #ff2d55;
    padding: 25px 40px;
    font-size: 1.6rem;
    color: #b8003b;
    border-radius: 14px;
    display: none;
    z-index: 100;
    text-align: center;
  }
  #startBtn {
    margin-top: 15px;
    padding: 12px 30px;
    font-size: 1.2rem;
    background-color: #ff2d55;
    border: none;
    border-radius: 10px;
    color: white;
    cursor: pointer;
    user-select: none;
  }
  #startBtn:hover {
    background-color: #e0264c;
  }

  @keyframes flutter {
    0%   { transform: scale(1) rotate(0deg); }
    50%  { transform: scale(1.3) rotate(15deg); }
    100% { transform: scale(1) rotate(0deg); }
  }

  .heart-flutter {
    animation: flutter 0.5s ease-in-out;
  }

</style>
</head>
<body>

<div id="scoreBoard">Score: 0 | High Score: 0</div>
<div id="livesDisplay">❤️❤️❤️</div>
<div id="compliment"></div>
<div id="gameArea"></div>
<div id="gameOver">Game Over!<br />Press Start to play again.</div>
<button id="startBtn">Start Game</button>

<audio id="bgMusic" src="aashiqtera.mp3" loop></audio>

<script>
  const gameArea = document.getElementById("gameArea");
  const scoreBoard = document.getElementById("scoreBoard");
  const livesDisplay = document.getElementById("livesDisplay");
  const compliment = document.getElementById("compliment");
  const gameOver = document.getElementById("gameOver");
  const startBtn = document.getElementById("startBtn");
  const bgMusic = document.getElementById("bgMusic");

  let score = 0;
  let highScore = 0;
  let lives = 3;
  let heartFallSpeed = 2;
  let heartSpawner;
  let speedIncreaser;
  let hearts = [];

  function createHeart() {
    const heart = document.createElement("div");
    heart.classList.add("heart");
    heart.style.left = Math.random() * (window.innerWidth - 50) + "px";

    const heartShape = document.createElement("div");
    heartShape.classList.add("heart-shape");
    heart.appendChild(heartShape);

    heart.addEventListener("click", () => {
      // Flutter animation on heart
      if(heart.classList.contains("heart-flutter")) return; // prevent double tapping

      heart.classList.add("heart-flutter");
      setTimeout(() => {
        if (gameArea.contains(heart)) {
          gameArea.removeChild(heart);
          hearts = hearts.filter(h => h !== heart);
        }
      }, 500);

      score++;
      if (score > highScore) {
        highScore = score;
        showCompliment();
      }
      updateScore();
    });

    gameArea.appendChild(heart);
    return heart;
  }

  function dropHeart() {
    const heart = createHeart();
    let top = 0;
    hearts.push(heart);

    const fallInterval = setInterval(() => {
      top += heartFallSpeed;
      heart.style.top = top + "px";

      if (top > window.innerHeight) {
        clearInterval(fallInterval);
        if (gameArea.contains(heart)) {
          gameArea.removeChild(heart);
          hearts = hearts.filter(h => h !== heart);
        }
        lives--;
        updateLives();
        if (lives === 0) {
          endGame();
        }
      }
    }, 20);
  }

  function updateScore() {
    scoreBoard.textContent = `Score: ${score} | High Score: ${highScore}`;
  }

  function updateLives() {
    livesDisplay.textContent = "❤️".repeat(lives);
  }

  function showCompliment() {
    const compliments = [
      "You're amazing! 🌟",
      "Keep shining! ✨",
      "Awesome job! 💖",
      "You're a star! 🌟",
      "Keep going! 💪"
    ];
    compliment.textContent = compliments[Math.floor(Math.random() * compliments.length)];
    compliment.style.display = "block";
    setTimeout(() => {
      compliment.style.display = "none";
    }, 2000);
  }

  function startGame() {
    score = 0;
    lives = 3;
    heartFallSpeed = 2;
    updateScore();
    updateLives();
    gameOver.style.display = "none";
    startBtn.style.display = "none";

    bgMusic.play().catch(e => {
      /* autoplay might be blocked on some browsers, user interaction needed */
    });

    heartSpawner = setInterval(dropHeart, 1200);
    speedIncreaser = setInterval(() => {
      if (heartFallSpeed < 15) heartFallSpeed += 0.15;
    }, 5000);
  }

  function endGame() {
    clearInterval(heartSpawner);
    clearInterval(speedIncreaser);
    gameOver.style.display = "block";
    startBtn.style.display = "block";
    bgMusic.pause();
    bgMusic.currentTime = 0;

    hearts.forEach(h => {
      if (gameArea.contains(h)) gameArea.removeChild(h);
    });
    hearts = [];
  }

  startBtn.addEventListener("click", startGame);
</script>

</body>
</html>
