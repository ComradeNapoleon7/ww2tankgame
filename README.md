# ww2tankgame

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>WWII Tank Game</title>
  <style>
    html, body {
      margin: 0;
      padding: 0;
      overflow: hidden;
      background: #2b2b2b;
    }
    #gameCanvas {
      display: block;
      margin: 0 auto;
      background-color: #3e3e3e;
    }
    #loginBox {
      position: absolute;
      top: 30%;
      left: 50%;
      transform: translate(-50%, -50%);
      background: #111;
      padding: 20px;
      color: white;
      border-radius: 10px;
      display: flex;
      flex-direction: column;
      gap: 10px;
      font-family: sans-serif;
    }
    #loginBox input, #loginBox button {
      padding: 8px;
    }
  </style>
</head>
<body>
  <canvas id="gameCanvas" width="1280" height="720"></canvas>

  <div id="loginBox">
    <h2>Tank Login</h2>
    <input id="username" placeholder="Username">
    <input id="password" placeholder="Password" type="password">
    <button onclick="login()">Login</button>
    <button onclick="guest()">Play as Guest</button>
  </div>

  <script>
    const canvas = document.getElementById('gameCanvas');
    const ctx = canvas.getContext('2d');
    const loginBox = document.getElementById('loginBox');
    let isFullscreen = false;

    let player = {
      x: 500, y: 500, w: 40, h: 60, speed: 2,
      color: "green", unlocked: false,
    };

    let camera = { x: 0, y: 0, follow: true };

    const keys = {};

    const obstacles = [
      { x: 700, y: 500, w: 50, h: 50, type: "rock" },
      { x: 1000, y: 300, w: 50, h: 50, type: "bush" }
    ];

    function login() {
      const u = document.getElementById("username").value;
      const p = document.getElementById("password").value;
      if (u === "ComradeNapoleon" && p === "extremelysecurepassword") {
        player.unlocked = true;
        alert("Admin access granted");
      }
      loginBox.style.display = "none";
    }

    function guest() {
      loginBox.style.display = "none";
    }

    window.addEventListener("keydown", e => {
      keys[e.key] = true;
      if (e.key === 'c') camera.follow = true;
      if (e.key === 'f') toggleFullscreen();
    });

    window.addEventListener("keyup", e => {
      keys[e.key] = false;
    });

    function toggleFullscreen() {
      if (!isFullscreen) {
        if (canvas.requestFullscreen) canvas.requestFullscreen();
        isFullscreen = true;
      } else {
        document.exitFullscreen();
        isFullscreen = false;
      }
    }

    function drawTank(tank) {
      ctx.fillStyle = tank.color;
      ctx.fillRect(tank.x - camera.x, tank.y - camera.y, tank.w, tank.h);
    }

    function drawObstacles() {
      for (let o of obstacles) {
        ctx.fillStyle = o.type === "rock" ? "#444" : "#0a0";
        ctx.fillRect(o.x - camera.x, o.y - camera.y, o.w, o.h);
      }
    }

    function update() {
      if (keys['w']) player.y -= player.speed;
      if (keys['s']) player.y += player.speed;
      if (keys['a']) player.x -= player.speed;
      if (keys['d']) player.x += player.speed;

      if (!camera.follow) {
        if (keys['ArrowUp']) camera.y -= 5;
        if (keys['ArrowDown']) camera.y += 5;
        if (keys['ArrowLeft']) camera.x -= 5;
        if (keys['ArrowRight']) camera.x += 5;
      } else {
        camera.x = player.x - canvas.width / 2 + player.w / 2;
        camera.y = player.y - canvas.height / 2 + player.h / 2;
      }
    }

    function render() {
      ctx.clearRect(0, 0, canvas.width, canvas.height);
      drawObstacles();
      drawTank(player);
      requestAnimationFrame(loop);
    }

    function loop() {
      update();
      render();
    }

    loop();
  </script>
</body>
</html>
