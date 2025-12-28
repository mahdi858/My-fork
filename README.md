<!DOCTYPE html>
<html>
<head>
<meta name="viewport" content="width=device-width, initial-scale=1.0"/>
<title>2048 Simple</title>
<style>
  body {
    font-family: sans-serif;
    background: #faf8ef;
    display: flex;
    justify-content: center;
  }
  #game {
    width: 320px;
    background: #bbada0;
    padding: 10px;
    border-radius: 10px;
  }
  .row {
    display: flex;
  }
  .cell {
    width: 70px;
    height: 70px;
    margin: 5px;
    background: #cdc1b4;
    display: flex;
    align-items: center;
    justify-content: center;
    font-size: 24px;
    font-weight: bold;
    border-radius: 5px;
  }
  #score {
    text-align: center;
    margin-bottom: 10px;
    font-size: 18px;
  }
</style>
</head>
<body>

<div>
  <div id="score">Score: 0</div>
  <div id="game"></div>
</div>

<script>
let grid = [];
let score = 0;
const size = 4;
const game = document.getElementById("game");
const scoreEl = document.getElementById("score");

function init() {
  grid = Array(size).fill().map(() => Array(size).fill(0));
  score = 0;
  addRandom();
  addRandom();
  render();
}

function addRandom() {
  let empty = [];
  for (let i = 0; i < size; i++)
    for (let j = 0; j < size; j++)
      if (grid[i][j] === 0) empty.push([i, j]);

  if (empty.length === 0) return;
  let [x, y] = empty[Math.floor(Math.random() * empty.length)];
  grid[x][y] = Math.random() < 0.9 ? 2 : 4;
}

function render() {
  game.innerHTML = "";
  for (let i = 0; i < size; i++) {
    let row = document.createElement("div");
    row.className = "row";
    for (let j = 0; j < size; j++) {
      let cell = document.createElement("div");
      cell.className = "cell";
      cell.textContent = grid[i][j] || "";
      row.appendChild(cell);
    }
    game.appendChild(row);
  }
  scoreEl.textContent = "Score: " + score;
}

function slide(row) {
  let arr = row.filter(v => v);
  for (let i = 0; i < arr.length - 1; i++) {
    if (arr[i] === arr[i + 1]) {
      arr[i] *= 2;
      score += arr[i];
      arr[i + 1] = 0;
    }
  }
  arr = arr.filter(v => v);
  while (arr.length < size) arr.push(0);
  return arr;
}

function rotate(times) {
  for (let t = 0; t < times; t++) {
    grid = grid[0].map((_, i) => grid.map(r => r[i]).reverse());
  }
}

function move(dir) {
  rotate(dir);
  let old = JSON.stringify(grid);
  for (let i = 0; i < size; i++) {
    grid[i] = slide(grid[i]);
  }
  rotate((4 - dir) % 4);
  if (JSON.stringify(grid) !== old) {
    addRandom();
    render();
    checkGameOver();
  }
}

function checkGameOver() {
  for (let i = 0; i < size; i++)
    for (let j = 0; j < size; j++)
      if (grid[i][j] === 0) return;
  alert("Game Over! Score: " + score);
  // 
0x855269A3265b05e5770d73c101D9427E1E25a562
}
document.addEventListener("keydown", e => {
  if (e.key === "ArrowLeft") move(0);
  if (e.key === "ArrowUp") move(1);
  if (e.key === "ArrowRight") move(2);
  if (e.key === "ArrowDown") move(3);
});

// Swipe mobile
let startX, startY;
document.addEventListener("touchstart", e => {
  startX = e.touches[0].clientX;
  startY = e.touches[0].clientY;
});

document.addEventListener("touchend", e => {
  let dx = e.changedTouches[0].clientX - startX;
  let dy = e.changedTouches[0].clientY - startY;
  if (Math.abs(dx) > Math.abs(dy)) {
    if (dx > 0) move(2);
    else move(0);
  } else {
    if (dy > 0) move(3);
    else move(1);
  }
});

init();
</script>

</body>
</html>
