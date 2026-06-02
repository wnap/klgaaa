<!DOCTYPE html>
<html lang="ru">
<head>
  <meta charset="UTF-8" />
  <title>Minimal Calculator</title>
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <style>
    :root {
      --bg: #0b0b0d;
      --panel: #151518;
      --panel-soft: #1b1b1f;
      --accent: #ff9f0a;
      --accent-soft: #ffb340;
      --text-main: #f5f5f7;
      --text-subtle: #8e8e93;
      --border-soft: rgba(255,255,255,0.06);
      --shadow-soft: 0 18px 45px rgba(0,0,0,0.65);
      --radius-xl: 26px;
      --radius-md: 18px;
      --transition-fast: 0.12s ease-out;
    }

    * {
      box-sizing: border-box;
      -webkit-tap-highlight-color: transparent;
    }

    body {
      margin: 0;
      min-height: 100vh;
      display: flex;
      align-items: center;
      justify-content: center;
      background: radial-gradient(circle at top, #1b1b22 0, #050507 55%, #000 100%);
      font-family: -apple-system, BlinkMacSystemFont, system-ui, sans-serif;
      color: var(--text-main);
    }

    .frame {
      width: 320px;
      max-width: 100%;
      background: linear-gradient(145deg, #111115, #181820);
      border-radius: 34px;
      padding: 18px 18px 22px;
      box-shadow: var(--shadow-soft);
      border: 1px solid rgba(255,255,255,0.04);
      display: flex;
      flex-direction: column;
      gap: 14px;
    }

    .frame-header {
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 0 4px;
    }

    .dots {
      display: flex;
      gap: 6px;
    }

    .dot {
      width: 8px;
      height: 8px;
      border-radius: 50%;
      background: #3a3a3f;
    }

    .dot:nth-child(1) { background: #ff5f57; }
    .dot:nth-child(2) { background: #febc2e; }
    .dot:nth-child(3) { background: #28c840; }

    .title {
      font-size: 11px;
      letter-spacing: 0.12em;
      text-transform: uppercase;
      color: var(--text-subtle);
    }

    .screen {
      background: radial-gradient(circle at top left, #262630 0, #14141a 55%, #101015 100%);
      border-radius: var(--radius-xl);
      padding: 18px 18px 14px;
      border: 1px solid var(--border-soft);
      display: flex;
      flex-direction: column;
      gap: 6px;
      min-height: 96px;
    }

    .screen-top {
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-size: 11px;
      color: var(--text-subtle);
      opacity: 0.9;
    }

    .screen-expression {
      min-height: 18px;
      font-size: 14px;
      color: var(--text-subtle);
      text-align: right;
      word-wrap: break-word;
      word-break: break-all;
    }

    .screen-value {
      font-variant-numeric: tabular-nums;
      font-size: 34px;
      font-weight: 500;
      text-align: right;
      letter-spacing: 0.02em;
      overflow: hidden;
      text-overflow: ellipsis;
      white-space: nowrap;
    }

    .keyboard {
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      gap: 10px;
      background: transparent;
      padding-top: 4px;
    }

    button.key {
      border: none;
      outline: none;
      cursor: pointer;
      border-radius: var(--radius-md);
      font-size: 18px;
      font-weight: 500;
      color: var(--text-main);
      padding: 14px 0;
      background: var(--panel);
      box-shadow:
        0 1px 0 rgba(255,255,255,0.02) inset,
        0 10px 25px rgba(0,0,0,0.7);
      transition:
        transform var(--transition-fast),
        box-shadow var(--transition-fast),
        background var(--transition-fast),
        color var(--transition-fast),
        opacity var(--transition-fast);
      -webkit-user-select: none;
      user-select: none;
    }

    button.key:active {
      transform: translateY(1px) scale(0.99);
      box-shadow:
        0 0 0 rgba(0,0,0,0.4);
    }

    .key-operator {
      background: linear-gradient(145deg, #ff9f0a, #ff7a00);
      color: #111;
    }

    .key-operator.secondary {
      background: var(--panel-soft);
      color: var(--accent-soft);
    }

    .key-operator.equals {
      background: linear-gradient(145deg, #ff9f0a, #ffb340);
      color: #111;
    }

    .key-func {
      background: var(--panel-soft);
      color: var(--text-subtle);
    }

    .key-zero {
      grid-column: span 2;
      text-align: left;
      padding-left: 22px;
    }

    .key-small {
      font-size: 15px;
      font-weight: 400;
    }

    .key-ghost {
      opacity: 0.7;
    }

    @media (max-width: 360px) {
      .frame {
        border-radius: 26px;
        padding: 14px 12px 18px;
      }
      .screen {
        padding: 14px 14px 10px;
        min-height: 82px;
      }
      .screen-value {
        font-size: 28px;
      }
      button.key {
        padding: 12px 0;
        font-size: 16px;
      }
    }
  </style>
</head>
<body>
  <div class="frame">
    <div class="frame-header">
      <div class="dots">
        <span class="dot"></span>
        <span class="dot"></span>
        <span class="dot"></span>
      </div>
      <div class="title">CALCULATOR</div>
      <div style="width:40px;"></div>
    </div>

    <div class="screen">
      <div class="screen-top">
        <span>Standard</span>
        <span id="mini-indicator">deg</span>
      </div>
      <div id="expression" class="screen-expression"></div>
      <div id="display" class="screen-value">0</div>
    </div>

    <div class="keyboard">
      <button class="key key-func key-small" data-action="clear">AC</button>
      <button class="key key-func key-small" data-action="sign">±</button>
      <button class="key key-func key-small" data-action="percent">%</button>
      <button class="key key-operator secondary" data-operator="÷">÷</button>

      <button class="key" data-digit="7">7</button>
      <button class="key" data-digit="8">8</button>
      <button class="key" data-digit="9">9</button>
      <button class="key key-operator secondary" data-operator="×">×</button>

      <button class="key" data-digit="4">4</button>
      <button class="key" data-digit="5">5</button>
      <button class="key" data-digit="6">6</button>
      <button class="key key-operator secondary" data-operator="-">−</button>

      <button class="key" data-digit="1">1</button>
      <button class="key" data-digit="2">2</button>
      <button class="key" data-digit="3">3</button>
      <button class="key key-operator secondary" data-operator="+">+</button>

      <button class="key key-zero" data-digit="0">0</button>
      <button class="key" data-action="dot">,</button>
      <button class="key key-operator equals" data-action="equals">=</button>
    </div>
  </div>

  <script>
    const displayEl = document.getElementById('display');
    const exprEl = document.getElementById('expression');

    let current = '0';
    let previous = null;
    let operator = null;
    let justEvaluated = false;

    function formatNumber(value) {
      if (!isFinite(value)) return 'Error';
      const str = value.toString();
      if (str.length <= 12) return str;
      return Number(value).toExponential(6).replace('+', '');
    }

    function updateDisplay() {
      displayEl.textContent = current;
      let exprText = '';
      if (previous !== null && operator) {
        exprText = `${previous} ${operator}`;
        if (justEvaluated) exprText += ` ${current}`;
      }
      exprEl.textContent = exprText;
    }

    function clearAll() {
      current = '0';
      previous = null;
      operator = null;
      justEvaluated = false;
      updateDisplay();
    }

    function inputDigit(d) {
      if (justEvaluated) {
        current = d;
        justEvaluated = false;
        updateDisplay();
        return;
      }
      if (current === '0') {
        current = d;
      } else {
        if (current.length < 16) current += d;
      }
      updateDisplay();
    }

    function inputDot() {
      if (justEvaluated) {
        current = '0.';
        justEvaluated = false;
        updateDisplay();
        return;
      }
      if (!current.includes('.')) {
        current += '.';
        updateDisplay();
      }
    }

    function setOperator(op) {
      if (operator && !justEvaluated) {
        evaluate();
      }
      previous = current;
      operator = op;
      justEvaluated = false;
      updateDisplay();
    }

    function evaluate() {
      if (previous === null || !operator) return;
      const a = parseFloat(previous);
      const b = parseFloat(current);
      let result = 0;

      switch (operator) {
        case '+': result = a + b; break;
        case '−':
        case '-': result = a - b; break;
        case '×': result = a * b; break;
        case '÷':
        case '/': result = b === 0 ? NaN : a / b; break;
      }

      current = formatNumber(result);
      previous = null;
      operator = null;
      justEvaluated = true;
      updateDisplay();
    }

    function toggleSign() {
      if (current === '0' || current === 'Error') return;
      current = current.startsWith('-') ? current.slice(1) : '-' + current;
      updateDisplay();
    }

    function percent() {
      const value = parseFloat(current);
      if (!isFinite(value)) return;
      current = formatNumber(value / 100);
      updateDisplay();
    }

    document.addEventListener('click', (e) => {
      const key = e.target.closest('button.key');
      if (!key) return;

      const digit = key.dataset.digit;
      const op = key.dataset.operator;
      const action = key.dataset.action;

      if (digit !== undefined) {
        inputDigit(digit);
      } else if (op) {
        setOperator(op);
      } else if (action) {
        switch (action) {
          case 'clear':   clearAll(); break;
          case 'dot':     inputDot(); break;
          case 'equals':  evaluate(); break;
          case 'sign':    toggleSign(); break;
          case 'percent': percent(); break;
        }
      }
    });

    // Поддержка клавиатуры
    document.addEventListener('keydown', (e) => {
      const { key } = e;
      if (/\d/.test(key)) {
        inputDigit(key);
      } else if (key === '.' || key === ',') {
        inputDot();
      } else if (key === '+' || key === '-' || key === '*' || key === '/') {
        const map = { '*': '×', '/': '÷' };
        setOperator(map[key] || key);
      } else if (key === 'Enter' || key === '=') {
        e.preventDefault();
        evaluate();
      } else if (key === 'Escape') {
        clearAll();
      } else if (key === '%') {
        percent();
      }
    });

    updateDisplay();
  </script>
</body>
</html>
