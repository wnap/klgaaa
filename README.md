<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8">
  <title>Calculator</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <style>
    body {
      margin: 0;
      background: #000;
      font-family: -apple-system, BlinkMacSystemFont, system-ui, sans-serif;
      display: flex;
      justify-content: center;
      align-items: center;
      height: 100vh;
      color: #fff;
    }

    /* Скрыть заголовок GitHub Pages */
    header, .site-header, .page-title { display: none !important; }
  </style>
</head>


    .calc {
      width: 320px;
      background: #111;
      padding: 20px;
      border-radius: 30px;
      box-shadow: 0 20px 40px rgba(0,0,0,0.6);
    }

    .display {
      background: #1c1c1e;
      padding: 20px;
      border-radius: 20px;
      text-align: right;
      font-size: 40px;
      margin-bottom: 20px;
      min-height: 60px;
    }

    .keys {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 10px;
    }

    button {
      border: none;
      border-radius: 20px;
      padding: 18px 0;
      font-size: 22px;
      background: #2c2c2e;
      color: #fff;
      cursor: pointer;
      transition: 0.1s;
    }

    button:active {
      transform: scale(0.97);
    }

    .op {
      background: #ff9f0a;
      color: #000;
    }

    .zero {
      grid-column: span 2;
      text-align: left;
      padding-left: 25px;
    }
  </style>
</head>
<body>

<div class="calc">
  <div id="display" class="display">0</div>

  <div class="keys">
    <button onclick="clearAll()">AC</button>
    <button onclick="toggleSign()">±</button>
    <button onclick="percent()">%</button>
    <button class="op" onclick="setOp('/')">÷</button>

    <button onclick="digit(7)">7</button>
    <button onclick="digit(8)">8</button>
    <button onclick="digit(9)">9</button>
    <button class="op" onclick="setOp('*')">×</button>

    <button onclick="digit(4)">4</button>
    <button onclick="digit(5)">5</button>
    <button onclick="digit(6)">6</button>
    <button class="op" onclick="setOp('-')">−</button>

    <button onclick="digit(1)">1</button>
    <button onclick="digit(2)">2</button>
    <button onclick="digit(3)">3</button>
    <button class="op" onclick="setOp('+')">+</button>

    <button class="zero" onclick="digit(0)">0</button>
    <button onclick="dot()">.</button>
    <button class="op" onclick="equal()">=</button>
  </div>
</div>

<script>
  let current = "0";
  let prev = null;
  let op = null;

  const display = document.getElementById("display");

  function update() {
    display.textContent = current;
  }

  function digit(n) {
    if (current === "0") current = String(n);
    else current += n;
    update();
  }

  function dot() {
    if (!current.includes(".")) {
      current += ".";
      update();
    }
  }

  function clearAll() {
    current = "0";
    prev = null;
    op = null;
    update();
  }

  function toggleSign() {
    if (current !== "0") {
      current = current.startsWith("-") ? current.slice(1) : "-" + current;
      update();
    }
  }

  function percent() {
    current = String(parseFloat(current) / 100);
    update();
  }

  function setOp(o) {
    prev = current;
    current = "0";
    op = o;
  }

  function equal() {
    if (!op || prev === null) return;
    const a = parseFloat(prev);
    const b = parseFloat(current);
    let r = 0;

    if (op === "+") r = a + b;
    if (op === "-") r = a - b;
    if (op === "*") r = a * b;
    if (op === "/") r = b === 0 ? "Error" : a / b;

    current = String(r);
    prev = null;
    op = null;
    update();
  }
</script>

</body>
</html>
