# Jogo
<!DOCTYPE html>
<html lang="pt-br">

<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Missão Inteiros: Elevador Abissal</title>
  <style>
    :root {
      --bg-color: #050a14;
      --ocean-blue: #0077be;
      --pos-color: #2ecc71;
      --neg-color: #e74c3c;
      --spec-color: #9b59b6;
    }

    body {
      font-family: 'Courier New', Courier, monospace;
      background-color: var(--bg-color);
      color: white;
      display: flex;
      flex-direction: column;
      align-items: center;
      margin: 0;
      padding: 20px;
    }

    /* O Cenário do Jogo [cite: 92] */
    #game-view {
      position: relative;
      width: 300px;
      height: 500px;
      background: linear-gradient(#4facfe 0%, #001a33 100%);
      border: 5px solid #333;
      border-radius: 10px;
      overflow: hidden;
      box-shadow: 0 0 50px rgba(0, 0, 0, 0.5);
    }

    /* Nível do Mar e Reta Numérica [cite: 101, 144] */
    .grid-line {
      position: absolute;
      width: 100%;
      height: 1px;
      background: rgba(255, 255, 255, 0.2);
      display: flex;
      align-items: center;
    }

    .grid-line span {
      font-size: 10px;
      margin-left: 5px;
      background: rgba(0, 0, 0, 0.5);
    }

    #zero-level {
      height: 3px;
      background: white;
      z-index: 5;
    }

    /* O Submarino */
    #submarine {
      position: absolute;
      left: 50%;
      bottom: 50%;
      /* Começa no Zero */
      transform: translate(-50%, 50%);
      transition: all 0.8s cubic-bezier(0.4, 0, 0.2, 1);
      font-size: 40px;
      z-index: 10;
      filter: drop-shadow(0 0 10px gold);
    }

    /* Interface de Cartas [cite: 34, 1, 70] */
    #ui-panel {
      margin-top: 20px;
      text-align: center;
      width: 100%;
      max-width: 600px;
    }

    .stats {
      font-size: 1.5rem;
      margin-bottom: 15px;
      padding: 10px;
      background: #111;
      border-radius: 5px;
      border: 1px solid #444;
    }

    .card-group {
      display: grid;
      grid-template-columns: repeat(auto-fit, minmax(100px, 1fr));
      gap: 10px;
    }

    button {
      padding: 15px 5px;
      border: none;
      border-radius: 8px;
      color: white;
      font-weight: bold;
      cursor: pointer;
      transition: transform 0.2s;
      font-size: 0.9rem;
    }

    button:active {
      transform: scale(0.95);
    }

    .btn-pos {
      background-color: var(--pos-color);
    }

    .btn-neg {
      background-color: var(--neg-color);
    }

    .btn-spec {
      background-color: var(--spec-color);
    }

    .log {
      margin-top: 15px;
      font-size: 0.9rem;
      color: #aaa;
      height: 40px;
    }
  </style>
</head>

<body>

  <h1>MISSÃO: INTEIROS</h1>

  <div id="game-view">
    <div id="submarine">🚢</div>
  </div>

  <div id="ui-panel">
    <div class="stats">Profundidade: <span id="depth-display">0</span>m</div>

    <div class="card-group">
      <button class="btn-pos" onclick="updateDepth(10)">ENERGIA +10</button>
      <button class="btn-pos" onclick="updateDepth(5)">ENERGIA +5</button>

      <button class="btn-neg" onclick="updateDepth(-5)">DESCER -5</button>
      <button class="btn-neg" onclick="updateDepth(-10)">DESCER -10</button>

      <button class="btn-spec" onclick="applySpecial('oposto')">OPOSTO -(X)</button>
      <button class="btn-spec" onclick="applySpecial('modulo')">MÓDULO |X|</button>
      <button class="btn-spec" onclick="resetGame()">ZERA TUDO</button>
    </div>

    <div class="log" id="game-log">Aguardando comandos do capitão...</div>
  </div>

  <script>
    let currentDepth = 0;
    const sub = document.getElementById('submarine');
    const display = document.getElementById('depth-display');
    const log = document.getElementById('game-log');
    const gameView = document.getElementById('game-view');
    // Criar a régua (Reta Numérica) dinamicamente [cite: 144]
    for (let i = -20; i <= 20; i += 5) {
      const line = document.createElement('div');
      line.className = 'grid-line';
      if (i === 0) line.id = 'zero-level';
      // Posicionamento: de 0% a 100% do container
      line.style.bottom = ((i + 20) * 2.5) + "%";
      line.innerHTML = `<span>${i}m</span>`;
      gameView.appendChild(line);
    }

    function updateDepth(value) {
      currentDepth += value;
      render();
      log.innerText = `Você moveu ${value}m.`;
    }

    function applySpecial(type) {
      if (type === 'oposto') {
        currentDepth = -currentDepth; // [cite: 181]
        log.innerText = "Sinal invertido! Oposto aplicado.";
      } else if (type === 'modulo') {
        currentDepth = Math.abs(currentDepth); // [cite: 71, 133]
        log.innerText = "Módulo aplicado: Você voltou para a superfície!";
      }
      render();
    }

    function resetGame() {
      currentDepth = 0; // [cite: 73]
      render();
      log.innerText = "Sistema reiniciado no nível zero.";
    }

    function render() {
      // Limites de segurança do submarino
      if (currentDepth > 20) currentDepth = 20;
      if (currentDepth < -20) currentDepth = -20;
      display.innerText = currentDepth;
      // O cálculo (currentDepth + 20) * 2.5 mapeia -20/20 para 0%/100%
      const visualPosition = (currentDepth + 20) * 2.5;
      sub.style.bottom = visualPosition + "%";
      // Cor do display para reforçar positivo/negativo
      display.style.color = currentDepth >= 0 ? "var(--pos-color)" : "var(--neg-color)";
    }
  </script>
</body>

</html>
