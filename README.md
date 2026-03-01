<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>3D Revolving Holi</title>
    <style>
        body {
            margin: 0;
            overflow: hidden;
            background-color: #050505;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            perspective: 1000px; /* Gives the 3D depth */
        }

        .holi-wrapper {
            cursor: pointer;
            animation: revolve 5s linear infinite;
            transform-style: preserve-3d;
        }

        .holi-text {
            font-family: 'Arial Black', sans-serif;
            font-size: 15vw; /* Extremely Big Letters */
            font-weight: 900;
            letter-spacing: 10px;
            background: linear-gradient(45deg, #f06, #f90, #00f, #0f0, #f06);
            background-size: 400% 400%;
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
            animation: gradientShift 3s ease infinite;
            filter: drop-shadow(0 0 20px rgba(255, 255, 255, 0.2));
        }

        /* 3D Rotation Animation */
        @keyframes revolve {
            0% { transform: rotateY(0deg); }
            100% { transform: rotateY(360deg); }
        }

        /* Shimmering Gradient Animation */
        @keyframes gradientShift {
            0% { background-position: 0% 50%; }
            50% { background-position: 100% 50%; }
            100% { background-position: 0% 50%; }
        }

        canvas {
            position: absolute;
            top: 0;
            left: 0;
            pointer-events: none;
        }
    </style>
</head>
<body>

    <div class="holi-wrapper" id="trigger">
        <div class="holi-text">HOLI</div>
    </div>

    <canvas id="canvas"></canvas>

    <script>
        const canvas = document.getElementById('canvas');
        const ctx = canvas.getContext('2d');
        const trigger = document.getElementById('trigger');

        let particles = [];
        canvas.width = window.innerWidth;
        canvas.height = window.innerHeight;

        class Powder {
            constructor() {
                this.x = Math.random() * canvas.width;
                this.y = -20; // Start above screen
                this.size = Math.random() * 8 + 2;
                this.speedY = Math.random() * 5 + 2;
                this.speedX = Math.random() * 2 - 1;
                const colors = ['#FF1493', '#00BFFF', '#ADFF2F', '#FFD700', '#FF4500', '#FF00FF'];
                this.color = colors[Math.floor(Math.random() * colors.length)];
                this.opacity = 1;
            }

            update() {
                this.y += this.speedY;
                this.x += this.speedX;
                // Slowly fade as they reach the bottom
                if (this.y > canvas.height * 0.7) {
                    this.opacity -= 0.02;
                }
            }

            draw() {
                ctx.globalAlpha = this.opacity;
                ctx.fillStyle = this.color;
                ctx.beginPath();
                // Draw irregular shapes to look like powder/gulaal
                ctx.arc(this.x, this.y, this.size, 0, Math.PI * 2);
                ctx.fill();
            }
        }

        function startRain() {
            // Add 100 particles per tap
            for (let i = 0; i < 100; i++) {
                setTimeout(() => {
                    particles.push(new Powder());
                }, i * 10); 
            }
        }

        function animate() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            for (let i = 0; i < particles.length; i++) {
                particles[i].update();
                particles[i].draw();
                if (particles[i].opacity <= 0 || particles[i].y > canvas.height) {
                    particles.splice(i, 1);
                    i--;
                }
            }
            requestAnimationFrame(animate);
        }

        // Listen for tap/click
        trigger.addEventListener('mousedown', startRain);
        trigger.addEventListener('touchstart', (e) => {
            e.preventDefault();
            startRain();
        });

        window.addEventListener('resize', () => {
            canvas.width = window.innerWidth;
            canvas.height = window.innerHeight;
        });

        animate();
    </script>
</body>
</html>
