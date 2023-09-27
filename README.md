<!DOCTYPE html>
<html lang="ru">
<head>
<meta charset="UTF-8">
<title>NFT Playground</title>
<style>
    body {
        margin: 0;
        overflow: hidden;
        font-family: 'Arial', sans-serif;
    }
    canvas {
        display: block;
        background-color: #e9ecef;
    }
    #score {
        position: absolute;
        top: 10px;
        left: 10px;
        font-size: 20px;
    }
</style>
</head>
<body>
<div id="score">Очки: 0</div>
<canvas id="gameCanvas"></canvas>
<script>
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');
    const scoreElement = document.getElementById('score');

    let fallingCubes = [];
    let score = 0;

    const collector = {
        x: 50,
        y: 0,
        width: 100, 
        height: 30,
        speed: 5
    };

    function resizeCanvas() {
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;
    }

    function createFallingCube() {
        const size = 60; 
        const x = Math.random() * (canvas.width - size);
        fallingCubes.push({ x, y: 0, size });
    }

    function checkCollision(rect1, rect2) {
        return rect1.x < rect2.x + rect2.size &&
               rect1.x + rect1.width > rect2.x &&
               rect1.y < rect2.y + rect2.size &&
               rect1.y + rect1.height > rect2.y;
    }

    function gameLoop() {
        ctx.clearRect(0, 0, canvas.width, canvas.height);

        if (Math.random() < 0.05) createFallingCube();

        fallingCubes.forEach((fallingCube, index) => {
            fallingCube.y += 2;
            ctx.fillStyle = 'red';
            ctx.fillRect(fallingCube.x, fallingCube.y, fallingCube.size, fallingCube.size);
            ctx.fillStyle = 'white';
            ctx.fillText('NFT', fallingCube.x + 10, fallingCube.y + 20);

            if (fallingCube.y > canvas.height || checkCollision(collector, fallingCube)) {
                if (checkCollision(collector, fallingCube)) score++;
                scoreElement.textContent = 'Очки: ' + score;
                fallingCubes.splice(index, 1);
            }
        });

        ctx.fillStyle = 'black';
        ctx.fillRect(collector.x, canvas.height - collector.height, collector.width, collector.height);
        ctx.fillStyle = 'white';
        ctx.fillText('Collector', collector.x + 10, canvas.height - 10);

        requestAnimationFrame(gameLoop);
    }

    window.addEventListener('keydown', function(e) {
        if(e.code === 'ArrowRight' && collector.x + collector.width < canvas.width) collector.x += collector.speed;
        if(e.code === 'ArrowLeft' && collector.x > 0) collector.x -= collector.speed;
    });

    canvas.addEventListener('click', function(e) {
        const canvasRect = canvas.getBoundingClientRect();
        const clickX = e.clientX - canvasRect.left;
        if (clickX < canvas.width / 2 && collector.x > 0) collector.x -= collector.speed;
        if (clickX >= canvas.width / 2 && collector.x + collector.width < canvas.width) collector.x += collector.speed;
    });

    window.addEventListener('resize', resizeCanvas);
    resizeCanvas();
    ctx.font = '18px Arial';
    gameLoop();
</script>
</body>
</html>
