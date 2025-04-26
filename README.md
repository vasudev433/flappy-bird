# flappy-bird
Here's the updated code with a Mario figure that appears when the game ends and jumps on the bird:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Flappy Bird</title>
    <style>
        body {
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #70c5ce;
            font-family: Arial, sans-serif;
        }
        
        #game-container {
            position: relative;
            width: 400px;
            height: 600px;
            overflow: hidden;
            background-color: #70c5ce;
            border: 2px solid #000;
        }
        
        #bird {
            position: absolute;
            width: 40px;
            height: 30px;
            background-color: #ffcc00;
            border-radius: 50% 50% 50% 50% / 60% 60% 40% 40%;
            left: 50px;
            top: 300px;
            z-index: 10;
            transition: transform 0.2s;
        }
        
        .pipe {
            position: absolute;
            width: 60px;
            background-color: #5cb85c;
            right: -60px;
        }
        
        .pipe-top {
            top: 0;
            border-bottom: 2px solid #000;
        }
        
        .pipe-bottom {
            bottom: 0;
            border-top: 2px solid #000;
        }
        
        #score {
            position: absolute;
            top: 20px;
            right: 20px;
            font-size: 24px;
            font-weight: bold;
            color: white;
            text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.5);
            z-index: 100;
        }
        
        #game-over {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: rgba(0, 0, 0, 0.7);
            color: white;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            display: none;
            z-index: 200;
        }
        
        #start-screen {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: rgba(0, 0, 0, 0.7);
            color: white;
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            z-index: 200;
        }
        
        button {
            background-color: #5cb85c;
            border: none;
            color: white;
            padding: 10px 20px;
            text-align: center;
            text-decoration: none;
            display: inline-block;
            font-size: 16px;
            margin: 10px 2px;
            cursor: pointer;
            border-radius: 5px;
        }
        
        #mario {
            position: absolute;
            width: 40px;
            height: 60px;
            background-image: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 40 60"><rect x="10" y="10" width="20" height="10" fill="%23ff0000"/><rect x="10" y="20" width="20" height="10" fill="%23ff0000"/><rect x="5" y="20" width="5" height="10" fill="%23ff0000"/><rect x="30" y="20" width="5" height="10" fill="%23ff0000"/><rect x="15" y="30" width="10" height="10" fill="%23d3a329"/><rect x="10" y="40" width="5" height="10" fill="%233366ff"/><rect x="25" y="40" width="5" height="10" fill="%233366ff"/><rect x="15" y="50" width="5" height="10" fill="%233366ff"/><rect x="20" y="50" width="5" height="10" fill="%233366ff"/><circle cx="12" cy="15" r="3" fill="%23ffffff"/><circle cx="28" cy="15" r="3" fill="%23ffffff"/><circle cx="12" cy="15" r="1" fill="%23000000"/><circle cx="28" cy="15" r="1" fill="%23000000"/><rect x="15" y="25" width="10" height="5" fill="%23d3a329"/></svg>');
            left: 400px;
            top: 300px;
            z-index: 15;
            display: none;
        }
        
        .bird-squashed {
            transform: scaleY(0.3) translateY(15px);
            background-color: #ff3300;
        }
    </style>
</head>
<body>
    <div id="game-container">
        <div id="score">0</div>
        <div id="bird"></div>
        <div id="mario"></div>
        <div id="game-over">
            <h2>Game Over</h2>
            <p>Your score: <span id="final-score">0</span></p>
            <button id="restart-button">Play Again</button>
        </div>
        <div id="start-screen">
            <h2>Flappy Bird</h2>
            <p>Click or press space to jump</p>
            <button id="start-button">Start Game</button>
        </div>
    </div>

    <script>
        // Game variables
        const bird = document.getElementById('bird');
        const mario = document.getElementById('mario');
        const gameContainer = document.getElementById('game-container');
        const scoreDisplay = document.getElementById('score');
        const gameOverScreen = document.getElementById('game-over');
        const finalScoreDisplay = document.getElementById('final-score');
        const startScreen = document.getElementById('start-screen');
        const startButton = document.getElementById('start-button');
        const restartButton = document.getElementById('restart-button');
        
        let gameRunning = false;
        let score = 0;
        let gravity = 0.5;
        let birdVelocity = 0;
        let birdPosition = 300;
        let pipeSpeed = 2;
        let pipeGap = 200;
        let pipes = [];
        let gameLoopId;
        let pipeGenerationInterval;
        
        // Game dimensions
        const gameHeight = 600;
        const gameWidth = 400;
        const birdWidth = 40;
        const birdHeight = 30;
        
        // Event listeners
        startButton.addEventListener('click', startGame);
        restartButton.addEventListener('click', startGame);
        
        document.addEventListener('keydown', function(e) {
            if (e.code === 'Space' && gameRunning) {
                jump();
            } else if (e.code === 'Space' && !gameRunning && startScreen.style.display === 'none') {
                startGame();
            }
        });
        
        gameContainer.addEventListener('click', function() {
            if (gameRunning) {
                jump();
            } else if (!gameRunning && startScreen.style.display === 'none') {
                startGame();
            }
        });
        
        // Jump function
        function jump() {
            birdVelocity = -10;
        }
        
        // Start game function
        function startGame() {
            // Reset game state
            score = 0;
            scoreDisplay.textContent = score;
            birdVelocity = 0;
            birdPosition = 300;
            bird.style.top = birdPosition + 'px';
            bird.className = '';
            pipes.forEach(pipe => pipe.element.remove());
            pipes = [];
            mario.style.display = 'none';
            
            // Hide screens
            gameOverScreen.style.display = 'none';
            startScreen.style.display = 'none';
            
            // Start game loop
            gameRunning = true;
            gameLoopId = requestAnimationFrame(gameLoop);
            
            // Start generating pipes
            pipeGenerationInterval = setInterval(generatePipe, 2000);
        }
        
        // Generate pipes
        function generatePipe() {
            if (!gameRunning) return;
            
            const minHeight = 50;
            const maxHeight = gameHeight - pipeGap - minHeight;
            const pipeHeight = Math.floor(Math.random() * (maxHeight - minHeight + 1)) + minHeight;
            
            // Create top pipe
            const topPipe = document.createElement('div');
            topPipe.className = 'pipe pipe-top';
            topPipe.style.height = pipeHeight + 'px';
            gameContainer.appendChild(topPipe);
            
            // Create bottom pipe
            const bottomPipe = document.createElement('div');
            bottomPipe.className = 'pipe pipe-bottom';
            bottomPipe.style.height = (gameHeight - pipeHeight - pipeGap) + 'px';
            gameContainer.appendChild(bottomPipe);
            
            pipes.push({
                element: topPipe,
                x: gameWidth,
                height: pipeHeight,
                passed: false
            });
            
            pipes.push({
                element: bottomPipe,
                x: gameWidth,
                height: gameHeight - pipeHeight - pipeGap,
                passed: false
            });
        }
        
        // Mario jumps on bird animation
        function marioJumpOnBird() {
            mario.style.display = 'block';
            mario.style.left = '400px';
            mario.style.top = birdPosition + 'px';
            
            // Mario runs in from the right
            let marioRun = setInterval(() => {
                let currentLeft = parseInt(mario.style.left);
                if (currentLeft > 70) {
                    mario.style.left = (currentLeft - 10) + 'px';
                } else {
                    clearInterval(marioRun);
                    
                    // Mario jumps
                    let jumpHeight = 0;
                    let jumpingUp = true;
                    let jumpInterval = setInterval(() => {
                        if (jumpingUp) {
                            jumpHeight += 5;
                            if (jumpHeight >= 50) jumpingUp = false;
                        } else {
                            jumpHeight -= 5;
                            if (jumpHeight <= 0) {
                                clearInterval(jumpInterval);
                                // Squash the bird
                                bird.classList.add('bird-squashed');
                                // Show game over after animation
                                setTimeout(() => {
                                    finalScoreDisplay.textContent = score;
                                    gameOverScreen.style.display = 'block';
                                }, 500);
                            }
                        }
                        mario.style.top = (birdPosition - jumpHeight) + 'px';
                    }, 20);
                }
            }, 20);
        }
        
        // Game loop
        function gameLoop() {
            if (!gameRunning) return;
            
            // Update bird position
            birdVelocity += gravity;
            birdPosition += birdVelocity;
            bird.style.top = birdPosition + 'px';
            
            // Check for collisions with ground or ceiling
            if (birdPosition >= gameHeight - birdHeight || birdPosition <= 0) {
                endGame();
                return;
            }
            
            // Update pipes
            for (let i = 0; i < pipes.length; i++) {
                const pipe = pipes[i];
                pipe.x -= pipeSpeed;
                pipe.element.style.left = pipe.x + 'px';
                
                // Check for collisions with pipes
                if (
                    (pipe.className.includes('pipe-top') && 
                    birdPosition < pipe.height && 
                    pipe.x < 50 + birdWidth && 
                    pipe.x + 60 > 50)
                    ||
                    (pipe.className.includes('pipe-bottom') && 
                    birdPosition + birdHeight > gameHeight - pipe.height && 
                    pipe.x < 50 + birdWidth && 
                    pipe.x + 60 > 50)
                ) {
                    endGame();
                    return;
                }
                
                // Check if bird passed a pipe (only check top pipes to avoid double counting)
                if (pipe.className.includes('pipe-top') && !pipe.passed && pipe.x + 60 < 50) {
                    pipe.passed = true;
                    score++;
                    scoreDisplay.textContent = score;
                }
                
                // Remove pipes that are off screen
                if (pipe.x + 60 < 0) {
                    pipe.element.remove();
                    pipes.splice(i, 1);
                    i--;
                }
            }
            
            gameLoopId = requestAnimationFrame(gameLoop);
        }
        
        // End game function
        function endGame() {
            gameRunning = false;
            cancelAnimationFrame(gameLoopId);
            clearInterval(pipeGenerationInterval);
            
            // Trigger Mario animation
            marioJumpOnBird();
        }
    </script>
</body>
</html>
```

## New Features Added:

1. **Mario Character**: I've added a simple Mario sprite using SVG data (so no external images needed)
2. **Death Animation**: When the game ends:
   - Mario runs in from the right side of the screen
   - Jumps on the bird
   - Squashes the bird (changes its shape and color)
   - Then shows the game over screen

3. **Visual Effects**:
   - The bird turns red and gets squashed when Mario jumps on it
   - Smooth animations for Mario's movement

The game mechanics remain the same, but now there's a fun visual punishment when you lose! The Mario sprite is basic but recognizable, and the jumping animation adds some personality to the game over sequence.

You can copy this entire code into an HTML file and open it in any modern browser to play. No external dependencies are needed.
