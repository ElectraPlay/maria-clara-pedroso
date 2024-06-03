Código para meu jogo do Tailes

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Fly tails</title>
    <link rel="stylesheet" href="styles.css">
</head>
<body>
    <div id="game-container">
        <div id="bird"></div>
        <div id="score">0</div>
        <div id="game-over" class="hidden">
            <div id="game-over-text">Fim de Jogo
                <br>Para continuar aperte <br> Reiniciar<Obj></Obj></div>
            <div id="final-score">Score: 0</div>
            <button id="restart-button">Reiniciar</button>
        </div>
    </div>
    <script src="script.js"></script>
</body>
</html>

body {
    margin: 0;
    display: flex;
    justify-content: center;
    align-items: center;
    height: 100vh;
    background-image:url(fundo.jpg);
    background-size: cover;
    font-family: Arial, sans-serif;
}

#game-container {
    position: relative;
    width: 10000px;
    height: 650px;
    background-image:url(fundo3.jpg);
    background-size: cover;
    overflow: hidden;
    border: none;
}

#bird {
    position: absolute;
    width: 80px;
    height: 70px;
    background-image: url('Tails_flying_sprite29.gif');
    background-size: cover;
    left: 50px;
    top: 50%;
    transform: translateY(-50%);

}

.obstacle {
    position: absolute;
    width: 60px;
    background-image: url(tubo3.jpg);
    border: none;
}

#score {
    position: absolute;
    top: 10px;
    left: 10px;
    font-size: 24px;
    color: #000;
}

#game-over {
    position: absolute;
    top: 50%;
    left: 50%;
    transform: translate(-50%, -50%);
    text-align: center;
    background-color: rgba(0, 0, 0, 0.8);
    color: #fff;
    padding: 20px;
    border-radius: 10px;
    display: flex;
    flex-direction: column;
    align-items: center;
}

#game-over.hidden {
    display: none;
}

#restart-button {
    margin-top: 10px;
    padding: 10px 20px;
    font-size: 16px;
    cursor: pointer;
}





const gameContainer = document.getElementById('game-container');
const bird = document.getElementById('bird');
const scoreDisplay = document.getElementById('score');
const gameOverDisplay = document.getElementById('game-over');
const finalScoreDisplay = document.getElementById('final-score');
const restartButton = document.getElementById('restart-button');

let birdY = gameContainer.clientHeight / 2 - bird.clientHeight / 2;
let birdVelocity = 0;
let gravity = 0.5;
let jump = -10;
let score = 0;
let gameActive = true;

document.addEventListener('keydown', () => {
    if (gameActive) birdVelocity = jump;
});

restartButton.addEventListener('click', () => {
    resetGame();
});

function createObstacle() {
    const obstacle = document.createElement('div');
    obstacle.classList.add('obstacle');
    obstacle.style.height = Math.random() * 200 + 50 + 'px';
    obstacle.style.left = gameContainer.clientWidth + 'px';
    obstacle.style.top = Math.random() < 0.5 ? '0' : 'auto';
    obstacle.style.bottom = Math.random() < 0.5 ? 'auto' : '0';
    gameContainer.appendChild(obstacle);
}

function updateObstacles() {
    const obstacles = document.querySelectorAll('.obstacle');
    obstacles.forEach(obstacle => {
        obstacle.style.left = parseInt(obstacle.style.left) - 5 + 'px';
        if (parseInt(obstacle.style.left) < -obstacle.clientWidth) {
            obstacle.remove();
            score++;
            scoreDisplay.textContent = score;
        }

        if (checkCollision(bird, obstacle)) {
            endGame();
        }
    });
}

function checkCollision(bird, obstacle) {
    const birdRect = bird.getBoundingClientRect();
    const obstacleRect = obstacle.getBoundingClientRect();
    return !(
        birdRect.top > obstacleRect.bottom ||
        birdRect.bottom < obstacleRect.top ||
        birdRect.right < obstacleRect.left ||
        birdRect.left > obstacleRect.right
    );
}

function resetGame() {
    birdY = gameContainer.clientHeight / 2 - bird.clientHeight / 2;
    birdVelocity = 0;
    score = 0;
    scoreDisplay.textContent = score;
    gameActive = true;
    gameOverDisplay.classList.add('hidden');
    document.querySelectorAll('.obstacle').forEach(obstacle => obstacle.remove());
}

function endGame() {
    gameActive = false;
    finalScoreDisplay.textContent = 'Score: ' + score;
    gameOverDisplay.classList.remove('hidden');
}

function gameLoop() {
    if (gameActive) {
        birdVelocity += gravity;
        birdY += birdVelocity;
        bird.style.top = birdY + 'px';

        if (birdY + bird.clientHeight > gameContainer.clientHeight || birdY < 0) {
            endGame();
        }

        if (Math.random() < 0.02) {
            createObstacle();
        }

        updateObstacles();
    }

    requestAnimationFrame(gameLoop);
}

gameLoop();


