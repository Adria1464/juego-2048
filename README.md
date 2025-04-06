<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8" />
  <title>2048 Juego Touch con Puntuación</title>
  <style>
    body {
      font-family: sans-serif;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      height: 100vh;
      background: #faf8ef;
      margin: 0;
    }
    #score {
      font-size: 24px;
      margin-bottom: 10px;
    }
    #game {
      display: grid;
      grid-template: repeat(4, 80px) / repeat(4, 80px);
      gap: 8px;
      touch-action: none;
    }
    .tile {
      width: 80px;
      height: 80px;
      background: #cdc1b4;
      font-size: 24px;
      font-weight: bold;
      color: #776e65;
      display: flex;
      justify-content: center;
      align-items: center;
      border-radius: 5px;
    }
  </style>
</head>
<body>
  <div id="score">Puntuación: 0</div>
  <div id="game"></div>
  <script>
    const game = document.getElementById("game");
    const scoreDisplay = document.getElementById("score");
    let grid = Array(4).fill().map(() => Array(4).fill(0));
    let score = 0;

    function drawGrid() {
      game.innerHTML = '';
      for (let row of grid) {
        for (let val of row) {
          const tile = document.createElement("div");
          tile.className = "tile";
          tile.textContent = val !== 0 ? val : '';
          tile.style.background = val ? "#eee4da" : "#cdc1b4";
          game.appendChild(tile);
        }
      }
      scoreDisplay.textContent = `Puntuación: ${score}`;
    }

    function addRandom() {
      let options = [];
      for (let i = 0; i < 4; i++)
        for (let j = 0; j < 4; j++)
          if (grid[i][j] === 0) options.push([i, j]);
      if (options.length > 0) {
        let [i, j] = options[Math.floor(Math.random() * options.length)];
        grid[i][j] = Math.random() < 0.9 ? 2 : 4;
      }
    }

    function slide(row) {
      row = row.filter(val => val);
      for (let i = 0; i < row.length - 1; i++) {
        if (row[i] === row[i + 1]) {
          row[i] *= 2;
          score += row[i];
          row[i + 1] = 0;
        }
      }
      return row.filter(val => val).concat(Array(4 - row.filter(val => val).length).fill(0));
    }

    function rotateGrid() {
      let newGrid = Array(4).fill().map(() => Array(4).fill(0));
      for (let i = 0; i < 4; i++)
        for (let j = 0; j < 4; j++)
          newGrid[i][j] = grid[3 - j][i];
      return newGrid;
    }

    function moveLeft() {
      for (let i = 0; i < 4; i++) {
        grid[i] = slide(grid[i]);
      }
    }

    function move(direction) {
      if (direction === "left") moveLeft();
      if (direction === "right") {
        grid = grid.map(r => r.reverse());
        moveLeft();
        grid = grid.map(r => r.reverse());
      }
      if (direction === "up") {
        grid = rotateGrid();
        moveLeft();
        grid = rotateGrid(); grid = rotateGrid(); grid = rotateGrid();
      }
      if (direction === "down") {
        grid = rotateGrid(); grid = rotateGrid(); grid = rotateGrid();
        moveLeft();
        grid = rotateGrid();
      }
      addRandom();
      drawGrid();
    }

    document.addEventListener("keydown", e => {
      if (["ArrowLeft", "ArrowRight", "ArrowUp", "ArrowDown"].includes(e.key)) {
        move(e.key.replace("Arrow", "").toLowerCase());
      }
    });

    // TOUCH CONTROL
    let startX, startY;
    game.addEventListener("touchstart", e => {
      const touch = e.touches[0];
      startX = touch.clientX;
      startY = touch.clientY;
    });

    game.addEventListener("touchend", e => {
      const touch = e.changedTouches[0];
      const dx = touch.clientX - startX;
      const dy = touch.clientY - startY;
      if (Math.abs(dx) > Math.abs(dy)) {
        move(dx > 0 ? "right" : "left");
      } else {
        move(dy > 0 ? "down" : "up");
      }
    });

    addRandom();
    addRandom();
    drawGrid();
  </script>
</body>
</html>