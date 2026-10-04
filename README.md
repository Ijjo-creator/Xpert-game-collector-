# Xpert-game-collector-
<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">

<title>Xpert Coin Collector</title>

<style>
* {
    box-sizing: border-box;
    margin: 0;
    padding: 0;
    touch-action: none;
}

body {
    overflow: hidden;
    background: #111;
    font-family: Arial, sans-serif;
}

#game {
    position: relative;
    width: 100vw;
    height: 100vh;
    overflow: hidden;
    background:
        radial-gradient(circle at 50% 20%, #3b8dff, #10245c 65%, #071126);
}

/* Stars */
.star {
    position: absolute;
    width: 3px;
    height: 3px;
    background: white;
    border-radius: 50%;
    opacity: .8;
}

/* HUD */
#hud {
    position: absolute;
    top: 15px;
    left: 15px;
    z-index: 20;
    color: white;
    font-size: 19px;
    font-weight: bold;
    text-shadow: 2px 2px 5px black;
}

#hud div {
    margin-bottom: 6px;
}

/* Player */
#player {
    
    position: absolute;
    width: 50px;
    height: 60px;
    background: linear-gradient(#ff5252, #b40000);
    border-radius: 15px 15px 10px 10px;
    bottom: 200px;
    left: 50%;
    transform: translateX(-50%);
    z-index: 10;
    box-shadow: 0 5px 10px rgba(0,0,0,.5);
    
}

/* Player eyes */
#player::before {
    content: "👀";
    position: absolute;
    top: 10px;
    left: 8px;
    font-size: 28px;
}

/* Player feet */
#player::after {
    content: "";
    position: absolute;
    bottom: -8px;
    left: 6px;
    width: 40px;
    height: 13px;
    background: green;
    border-radius: 10px;
}

/* Coins */
.coin {
    position: absolute;
    width: 42px;
    height: 42px;
    border-radius: 50%;
    background: radial-gradient(circle at 35% 30%, #fff36a, #ffd000 45%, #d89000);
    border: 3px solid #fff1a0;
    box-shadow: 0 0 15px rgba(255,210,0,.9);
    display: flex;
    align-items: center;
    justify-content: center;
    color: #9b6500;
    font-size: 24px;
    font-weight: bold;
    z-index: 8;
}

/* Bomb */
.bomb {
    position: absolute;
    width: 44px;
    height: 44px;
    border-radius: 50%;
    background: radial-gradient(circle at 30% 25%, #555, #111 60%);
    box-shadow: 0 0 10px rgba(255,0,0,.7);
    display: flex;
    justify-content: center;
    align-items: center;
    font-size: 27px;
    z-index: 8;
}

/* Touch instruction */
#instruction {
    position: absolute;
    bottom: 20px;
    left: 50%;
    transform: translateX(-50%);
    width: 90%;
    text-align: center;
    color: white;
    font-size: 15px;
    font-weight: bold;
    text-shadow: 2px 2px 5px black;
    z-index: 20;
}

/* Screens */
.screen {
    position: absolute;
    inset: 0;
    z-index: 100;
    background: rgba(0,0,0,.75);
    display: flex;
    align-items: center;
    justify-content: center;
    text-align: center;
    color: white;
}

.panel {
    width: 85%;
    max-width: 400px;
}

.panel h1 {
    font-size: 35px;
    margin-bottom: 15px;
}

.panel p {
    font-size: 17px;
    line-height: 1.5;
    margin-bottom: 22px;
}

button {
    padding: 15px 30px;
    border: none;
    border-radius: 12px;
    background: #ffc400;
    color: #222;
    font-size: 19px;
    font-weight: bold;
}

button:active {
    transform: scale(.94);
}

.hidden {
    display: none;
}

/* Score animation */
.scorePop {
    position: absolute;
    color: #ffe000;
    font-size: 25px;
    font-weight: bold;
    pointer-events: none;
    animation: pop .6s forwards;
    z-index: 50;
}

@keyframes pop {
    0% {
        transform: translateY(0) scale(1);
        opacity: 1;
    }

    100% {
        transform: translateY(-60px) scale(1.4);
        opacity: 0;
    }
}
</style>
</head>

<body>

<div id="game">

    <!-- HUD -->
    <div id="hud">
        <div>🪙 Coins: <span id="score">0</span></div>
        <div>❤️ Lives: <span id="lives">3</span></div>
        <div>⭐ Level: <span id="level">1</span></div>
    </div>

    <!-- Player -->
    <div id="player"></div>

    <!-- Touch Area -->
    <div id="touchArea"
         style="position:absolute; inset:0; z-index:5;">
    </div>

    <div id="instruction">
        👆 TOUCH ABOVE OR BELOW THE PLAYER AND DRAG TO COLLECT THE COINS
    </div>

    <!-- Start Screen -->
    <div class="screen" id="startScreen">
        <div class="panel">
            <h1>🪙 COIN COLLECTOR</h1>

            <p>
                Move your character by touching and
                dragging your finger across the screen.
                Collect coins and avoid bombs!
            </p>

            <button id="startButton">
                START GAME
            </button>
        </div>
    </div>

    <!-- Game Over -->
    <div class="screen hidden" id="gameOverScreen">
        <div class="panel">

            <h1>GAME OVER</h1>

            <p>
                Coins collected:
                <strong id="finalScore">0</strong>
            </p>

            <button id="restartButton">
                PLAY AGAIN
            </button>

        </div>
    </div>

</div>

<script>

/* =========================
   VARIABLES
========================= */

const game = document.getElementById("game");
const player = document.getElementById("player");
const touchArea = document.getElementById("touchArea");

const scoreText = document.getElementById("score");
const livesText = document.getElementById("lives");
const levelText = document.getElementById("level");

const startScreen =
    document.getElementById("startScreen");

const gameOverScreen =
    document.getElementById("gameOverScreen");

const startButton =
    document.getElementById("startButton");

const restartButton =
    document.getElementById("restartButton");

const finalScore =
    document.getElementById("finalScore");

let playerX = 0;

let score = 0;
let lives = 3;
let level = 1;

let speed = 3;

let running = false;

let animationFrame;

let objects = [];


/* =========================
   SETUP
========================= */

function setupGame() {

    objects.forEach(obj => {

        if (obj.element) {
            obj.element.remove();
        }

    });

    objects = [];

    score = 0;
    lives = 5;
    level = 1;
    speed = 3;

    scoreText.textContent = score;
    livesText.textContent = lives;
    levelText.textContent = level;

    playerX =
        (game.clientWidth - player.clientWidth) / 2;

    player.style.left =
        playerX + "px";

    createObjects();
}


/* =========================
   CREATE OBJECTS
========================= */

function createObjects() {

    for (let i = 0; i < 6; i++) {

        createCoin(
            -100 - Math.random() * 700
        );
    }

    for (let i = 0; i < 3; i++) {

        createBomb(
            -200 - Math.random() * 900
        );
    }
}


/* =========================
   COIN
========================= */

function createCoin(y) {

    const coin =
        document.createElement("div");

    coin.className = "coin";

    coin.textContent = "★$";

    game.appendChild(coin);

    const maxX =
        game.clientWidth - 50;

    const x =
        Math.random() * maxX;

    coin.style.left =
        x + "px";

    coin.style.top =
        y + "px";

    objects.push({
        element: coin,
        type: "coin",
        x: x,
        y: y
    });
}


/* =========================
   BOMB
========================= */

function createBomb(y) {

    const bomb =
        document.createElement("div");

    bomb.className = "bomb";

    bomb.textContent = "👹";

    game.appendChild(bomb);

    const maxX =
        game.clientWidth - 50;

    const x =
        Math.random() * maxX;

    bomb.style.left =
        x + "px";

    bomb.style.top =
        y + "px";

    objects.push({
        element: bomb,
        type: "bomb",
        x: x,
        y: y
    });
}


/* =========================
   TOUCH CONTROL
========================= */

let touching = false;

touchArea.addEventListener(
    "pointerdown",
    function(e) {

        if (!running) return;

        touching = true;

        movePlayer(e.clientX);
    }
);


touchArea.addEventListener(
    "pointermove",
    function(e) {

        if (!running || !touching) return;

        movePlayer(e.clientX);
    }
);


touchArea.addEventListener(
    "pointerup",
    function() {

        touching = false;
    }
);


touchArea.addEventListener(
    "pointercancel",
    function() {

        touching = false;
    }
);


function movePlayer(screenX) {

    let x =
        screenX -
        player.clientWidth / 2;

    const minX = 5;

    const maxX =
        game.clientWidth -
        player.clientWidth -
        5;

    x =
        Math.max(minX, x);

    x =
        Math.min(maxX, x);

    playerX = x;

    player.style.left =
        playerX + "px";
}


/* =========================
   COLLISION
========================= */

function isColliding(a, b) {

    const r1 =
        a.getBoundingClientRect();

    const r2 =
        b.getBoundingClientRect();

    const padding = 8;

    return !(
        r1.right - padding < r2.left ||
        r1.left + padding > r2.right ||
        r1.bottom - padding < r2.top ||
        r1.top + padding > r2.bottom
    );
}


/* =========================
   SCORE EFFECT
========================= */

function scoreEffect(x, y) {

    const pop =
        document.createElement("div");

    pop.className = "scorePop";

    pop.textContent = "+5 🪙💫";

    pop.style.left =
        x + "px";

    pop.style.top =
        y + "px";

    game.appendChild(pop);

    setTimeout(() => {

        pop.remove();

    }, 600);
}


/* =========================
   UPDATE OBJECTS
========================= */

function updateObjects() {

    objects.forEach(obj => {

        obj.y += speed;

        obj.element.style.top =
            obj.y + "px";

        /* Collision */

        if (
            obj.element &&
            isColliding(player, obj.element)
        ) {

            if (obj.type === "coin") {

                score+=5;

                scoreText.textContent =
                    score;

                scoreEffect(
                    obj.x,
                    obj.y
                );

                resetObject(obj);

            } else {

                lives--;

                livesText.textContent =
                    lives;

                resetObject(obj);

                if (lives <= 0) {

                    endGame();
                }
            }
        }


        /* Object leaves screen */

        if (
            obj.y >
            game.clientHeight + 70
        ) {

            resetObject(obj);
        }
    });
}


/* =========================
   RESET OBJECT
========================= */

function resetObject(obj) {

    obj.y =
        -80 -
        Math.random() * 500;

    obj.x =
        Math.random() *
        (game.clientWidth - 50);

    obj.element.style.left =
        obj.x + "px";

    obj.element.style.top =
        obj.y + "px";

    /*
    Increase level
    */

    if (
        obj.type === "coin" &&
        score > 0 &&
        score % 50 === 0
    ) {

        level =
            Math.floor(score / 50) + 1;

        levelText.textContent =
            level;

        speed =
            3 + level * .2;
    }
}

/* =========================
   GAME LOOP
========================= */

function gameLoop() {

    if (!running) return;

    updateObjects();

    animationFrame =
        requestAnimationFrame(gameLoop);
}


/* =========================
   START GAME
========================= */

function startGame() {

    cancelAnimationFrame(
        animationFrame
    );

    setupGame();

    running = true;

    startScreen.classList.add(
        "hidden"
    );

    gameOverScreen.classList.add(
        "hidden"
    );

    gameLoop();
}


/* =========================
   GAME OVER
========================= */

function endGame() {

    running = false;

    cancelAnimationFrame(
        animationFrame
    );

    finalScore.textContent =
        score;

    gameOverScreen.classList.remove(
        "hidden"
    );
}


/* =========================
   BUTTONS
========================= */

startButton.addEventListener(
    "click",
    startGame
);

restartButton.addEventListener(
    "click",
    startGame
);

</script>

</body>
</html>
