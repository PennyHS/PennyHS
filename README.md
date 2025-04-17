# 👋 Hi there! I'm Penny

Software Engineer with  professional experience, passionate about building elegant solutions to complex problems. My journey spans full-stack development, cloud architecture, and data analytics.

## 💻 Technical Expertise

- **Languages**: JavaScript, Python, Java, C#, SQL
- **Frontend**: React, HTML/CSS, ASP.NET MVC
- **Cloud**: AWS (S3, SageMaker), Azure
- **ML/AI**: Regression, Classification, Clustering, CNN, Reinforcement Learning
- **Tools**: Visual Studio, Jira, Git, pandas, numpy, scikit-learn

## 🚀 What I Do

I specialize in creating responsive web applications with a focus on user experience and code quality. My approach combines technical expertise with collaborative problem-solving to deliver maintainable solutions that make an impact.

## 🌱 Always Learning

Constantly exploring emerging technologies and expanding my skill set. Currently diving deeper into cloud architecture patterns and advanced frontend frameworks.

## 🤝 Let's Connect

Open to networking, collaboration, and helping others on their coding journey. Feel free to reach out for discussions on software development, data analytics, or tech in general!
<!---
PennyHS/PennyHS is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Snake Game</title>
    <style>
        body {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
            margin: 0;
            background-color: #f0f0f0;
            font-family: Arial, sans-serif;
        }
        
        h1 {
            color: #333;
            margin-bottom: 10px;
        }
        
        #game-container {
            position: relative;
        }
        
        #game-canvas {
            border: 3px solid #333;
            background-color: #222;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.3);
        }
        
        #score-display {
            position: absolute;
            top: 10px;
            left: 10px;
            color: white;
            font-size: 16px;
            font-weight: bold;
        }
        
        #game-over {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            background-color: rgba(0, 0, 0, 0.7);
            color: white;
            padding: 20px;
            border-radius: 5px;
            text-align: center;
            display: none;
        }
        
        #restart-btn {
            background-color: #4CAF50;
            border: none;
            color: white;
            padding: 10px 20px;
            text-align: center;
            text-decoration: none;
            display: inline-block;
            font-size: 16px;
            margin-top: 10px;
            cursor: pointer;
            border-radius: 4px;
        }
        
        #restart-btn:hover {
            background-color: #45a049;
        }
        
        #controls {
            margin-top: 20px;
            text-align: center;
            color: #555;
        }
    </style>
</head>
<body>
    <h1>Snake Game</h1>
    <div id="game-container">
        <canvas id="game-canvas" width="400" height="400"></canvas>
        <div id="score-display">Score: 0</div>
        <div id="game-over">
            <h2>Game Over!</h2>
            <p id="final-score">Your score: 0</p>
            <button id="restart-btn">Play Again</button>
        </div>
    </div>
    <div id="controls">
        <p>Use arrow keys or WASD to control the snake</p>
    </div>

    <script>
        // Game settings
        const GRID_SIZE = 20;
        const GAME_SPEED = 100; // milliseconds
        const canvas = document.getElementById('game-canvas');
        const ctx = canvas.getContext('2d');
        const scoreDisplay = document.getElementById('score-display');
        const gameOverScreen = document.getElementById('game-over');
        const finalScoreDisplay = document.getElementById('final-score');
        const restartBtn = document.getElementById('restart-btn');
        
        // Game state
        let snake = [];
        let food = {};
        let direction = 'right';
        let nextDirection = 'right';
        let score = 0;
        let gameRunning = false;
        let gameLoop;
        
        // Initialize game
        function initGame() {
            // Reset game state
            snake = [
                {x: 5, y: 10},
                {x: 4, y: 10},
                {x: 3, y: 10}
            ];
            direction = 'right';
            nextDirection = 'right';
            score = 0;
            scoreDisplay.textContent = `Score: ${score}`;
            gameOverScreen.style.display = 'none';
            
            // Create first food
            createFood();
            
            // Start game loop
            if (gameLoop) clearInterval(gameLoop);
            gameRunning = true;
            gameLoop = setInterval(updateGame, GAME_SPEED);
        }
        
        // Create food at random position
        function createFood() {
            const maxX = canvas.width / GRID_SIZE - 1;
            const maxY = canvas.height / GRID_SIZE - 1;
            
            // Generate a position that's not occupied by the snake
            let foodPosition;
            do {
                foodPosition = {
                    x: Math.floor(Math.random() * maxX) + 1,
                    y: Math.floor(Math.random() * maxY) + 1
                };
            } while (snake.some(segment => segment.x === foodPosition.x && segment.y === foodPosition.y));
            
            food = foodPosition;
        }
        
        // Update game state
        function updateGame() {
            if (!gameRunning) return;
            
            // Update direction
            direction = nextDirection;
            
            // Calculate new head position
            const head = {...snake[0]};
            switch (direction) {
                case 'up':
                    head.y -= 1;
                    break;
                case 'down':
                    head.y += 1;
                    break;
                case 'left':
                    head.x -= 1;
                    break;
                case 'right':
                    head.x += 1;
                    break;
            }
            
            // Check if game over (wall collision or self collision)
            if (
                head.x < 0 || head.x >= canvas.width / GRID_SIZE ||
                head.y < 0 || head.y >= canvas.height / GRID_SIZE ||
                snake.some(segment => segment.x === head.x && segment.y === head.y)
            ) {
                gameOver();
                return;
            }
            
            // Add new head
            snake.unshift(head);
            
            // Check if food eaten
            if (head.x === food.x && head.y === food.y) {
                // Increase score
                score += 10;
                scoreDisplay.textContent = `Score: ${score}`;
                
                // Create new food
                createFood();
            } else {
                // Remove tail
                snake.pop();
            }
            
            // Draw everything
            draw();
        }
        
        // Draw the game
        function draw() {
            // Clear canvas
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            // Draw snake
            snake.forEach((segment, index) => {
                ctx.fillStyle = index === 0 ? '#4CAF50' : '#2E7D32';  // Head is lighter green
                ctx.fillRect(segment.x * GRID_SIZE, segment.y * GRID_SIZE, GRID_SIZE - 1, GRID_SIZE - 1);
            });
            
            // Draw food
            ctx.fillStyle = '#FF5722';
            ctx.fillRect(food.x * GRID_SIZE, food.y * GRID_SIZE, GRID_SIZE - 1, GRID_SIZE - 1);
        }
        
        // Game over
        function gameOver() {
            gameRunning = false;
            clearInterval(gameLoop);
            finalScoreDisplay.textContent = `Your score: ${score}`;
            gameOverScreen.style.display = 'block';
        }
        
        // Handle keyboard input
        document.addEventListener('keydown', (e) => {
            switch (e.key) {
                case 'ArrowUp':
                case 'w':
                case 'W':
                    if (direction !== 'down') nextDirection = 'up';
                    break;
                case 'ArrowDown':
                case 's':
                case 'S':
                    if (direction !== 'up') nextDirection = 'down';
                    break;
                case 'ArrowLeft':
                case 'a':
                case 'A':
                    if (direction !== 'right') nextDirection = 'left';
                    break;
                case 'ArrowRight':
                case 'd':
                case 'D':
                    if (direction !== 'left') nextDirection = 'right';
                    break;
            }
        });
        
        // Restart button
        restartBtn.addEventListener('click', initGame);
        
        // Start the game when page loads
        window.onload = initGame;
    </script>
</body>
</html>
