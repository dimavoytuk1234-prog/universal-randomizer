<!DOCTYPE html>
<html lang="uk">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Універсальний Рандомайзер</title>
  <style>
    :root {
      --primary: #4f46e5;
      --primary-hover: #4338ca;
      --bg-color: #f3f4f6;
      --card-bg: #ffffff;
      --text: #1f2937;
      --text-muted: #6b7280;
      --border: #e5e7eb;
      --result-bg: #f9fafb;
      --radius: 12px;
    }

    /* Темна тема */
    body.dark-mode {
      --bg-color: #0f172a;
      --card-bg: #1e293b;
      --text: #f8fafc;
      --text-muted: #94a3b8;
      --border: #334155;
      --result-bg: #0f172a;
    }

    * {
      box-sizing: border-box;
      margin: 0;
      padding: 0;
      font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
    }

    body {
      background-color: var(--bg-color);
      color: var(--text);
      display: flex;
      justify-content: center;
      align-items: center;
      min-height: 100vh;
      padding: 20px;
      transition: background-color 0.3s, color 0.3s;
    }

    .app-card {
      background-color: var(--card-bg);
      border-radius: var(--radius);
      box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
      width: 100%;
      max-width: 450px;
      padding: 28px;
      transition: background-color 0.3s;
    }

    .app-header {
      margin-bottom: 20px;
    }

    .header-top {
      display: flex;
      justify-content: space-between;
      align-items: center;
    }

    .app-header h1 {
      font-size: 24px;
      color: var(--primary);
    }

    .app-header p {
      font-size: 14px;
      color: var(--text-muted);
      margin-top: 4px;
    }

    /* Кнопка перемикання теми (SVG іконки) */
    .theme-btn {
      background: none;
      border: 1px solid var(--border);
      border-radius: 8px;
      width: 36px;
      height: 36px;
      cursor: pointer;
      display: flex;
      justify-content: center;
      align-items: center;
      transition: all 0.2s;
      color: var(--text);
    }

    .theme-btn:hover {
      background-color: var(--border);
    }

    /* Навігація вкладок */
    .tab-nav {
      display: flex;
      border-bottom: 2px solid var(--border);
      margin-bottom: 20px;
    }

    .tab-btn {
      flex: 1;
      padding: 10px;
      border: none;
      background: none;
      font-weight: 600;
      color: var(--text-muted);
      cursor: pointer;
      transition: all 0.2s ease;
    }

    .tab-btn.active {
      color: var(--primary);
      border-bottom: 3px solid var(--primary);
      margin-bottom: -2px;
    }

    .tab-content {
      display: none;
    }

    .tab-content.active {
      display: block;
    }

    .input-grid {
      display: flex;
      gap: 12px;
    }

    .field-group {
      margin-bottom: 16px;
      flex: 1;
    }

    label {
      display: block;
      font-size: 13px;
      font-weight: 600;
      margin-bottom: 6px;
    }

    input[type="number"],
    textarea {
      width: 100%;
      padding: 10px 12px;
      border: 1px solid var(--border);
      border-radius: 8px;
      font-size: 15px;
      background-color: var(--card-bg);
      color: var(--text);
      outline: none;
      transition: border-color 0.2s;
    }

    input[type="number"]:focus,
    textarea:focus {
      border-color: var(--primary);
    }

    textarea {
      min-height: 80px;
      resize: vertical;
    }

    /* Кнопки */
    .btn-primary {
      width: 100%;
      padding: 12px;
      background-color: var(--primary);
      color: white;
      border: none;
      border-radius: 8px;
      font-size: 16px;
      font-weight: 600;
      cursor: pointer;
      transition: background-color 0.2s;
    }

    .btn-primary:hover {
      background-color: var(--primary-hover);
    }

    .btn-primary:disabled {
      opacity: 0.6;
      cursor: not-allowed;
    }

    /* Колесо фортуни */
    .wheel-wrapper {
      position: relative;
      display: flex;
      justify-content: center;
      align-items: center;
      margin: 15px 0;
    }

    .wheel-arrow {
      position: absolute;
      top: -10px;
      font-size: 24px;
      color: #ef4444;
      z-index: 10;
      text-shadow: 0 2px 4px rgba(0,0,0,0.3);
    }

    #wheel-canvas {
      border-radius: 50%;
      box-shadow: 0 4px 12px rgba(0,0,0,0.15);
    }

    /* Блок результату */
    .result-box {
      margin-top: 24px;
      padding: 16px;
      background-color: var(--result-bg);
      border: 2px dashed var(--border);
      border-radius: var(--radius);
      text-align: center;
    }

    .result-label {
      font-size: 12px;
      color: var(--text-muted);
      text-transform: uppercase;
      letter-spacing: 0.5px;
    }

    .result-text {
      font-size: 24px;
      font-weight: 700;
      color: var(--primary);
      margin-top: 4px;
      word-break: break-word;
    }

    /* Анімація результату */
    .pop-animation {
      animation: pop 0.25s ease-out;
    }

    @keyframes pop {
      0% { transform: scale(0.85); opacity: 0.5; }
      100% { transform: scale(1); opacity: 1; }
    }
  </style>
</head>
<body>

  <div class="app-card">
    <header class="app-header">
      <div class="header-top">
        <h1>🎲 Рандомайзер</h1>
        <button id="theme-toggle" class="theme-btn" aria-label="Змінити тему"></button>
      </div>
      <p>Оберіть потрібний режим для генерації</p>
    </header>

    <!-- Навігація вкладок -->
    <nav class="tab-nav" aria-label="Режими генерації">
      <button class="tab-btn active" data-tab="numbers">Числа</button>
      <button class="tab-btn" data-tab="list">Список</button>
      <button class="tab-btn" data-tab="wheel">Колесо</button>
    </nav>

    <!-- Вкладка 1: Генератор чисел -->
    <section id="tab-numbers" class="tab-content active">
      <div class="input-grid">
        <div class="field-group">
          <label for="num-min">Мінімум:</label>
          <input type="number" id="num-min" value="1">
        </div>
        <div class="field-group">
          <label for="num-max">Максимум:</label>
          <input type="number" id="num-max" value="100">
        </div>
      </div>
      <div class="field-group">
        <label for="num-count">Кількість значень:</label>
        <input type="number" id="num-count" value="1" min="1" max="50">
      </div>
      <button id="btn-gen-numbers" class="btn-primary">Згенерувати</button>
    </section>

    <!-- Вкладка 2: Вибір зі списку -->
    <section id="tab-list" class="tab-content">
      <div class="field-group">
        <label for="list-input">Варіанти (кожен з нового рядка):</label>
        <textarea id="list-input" placeholder="Варіант 1&#10;Варіант 2&#10;Варіант 3"></textarea>
      </div>
      <button id="btn-gen-list" class="btn-primary">Обрати варіант</button>
    </section>

    <!-- Вкладка 3: Колесо фортуни -->
    <section id="tab-wheel" class="tab-content">
      <div class="field-group">
        <label for="wheel-input">Варіанти для колеса (з нового рядка):</label>
        <textarea id="wheel-input" placeholder="Так&#10;Ні&#10;Можливо&#10;Спробуй ще раз">Так
Ні
Можливо
Спробуй ще раз</textarea>
      </div>

      <div class="wheel-wrapper">
        <div class="wheel-arrow">▼</div>
        <canvas id="wheel-canvas" width="280" height="280"></canvas>
      </div>

      <button id="btn-spin-wheel" class="btn-primary">Крутити колесо</button>
    </section>

    <!-- Блок результату -->
    <div class="result-box">
      <span class="result-label">Результат:</span>
      <div id="result-display" class="result-text">?</div>
    </div>
  </div>

  <script>
    document.addEventListener('DOMContentLoaded', () => {
      // --- Елементи вкладок та теми ---
      const tabButtons = document.querySelectorAll('.tab-btn');
      const tabContents = document.querySelectorAll('.tab-content');
      const resultDisplay = document.getElementById('result-display');
      const themeToggle = document.getElementById('theme-toggle');

      // SVG-іконки у стилі ChatGPT
      const sunIcon = `<svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><circle cx="12" cy="12" r="4"/><path d="M12 2v2"/><path d="M12 20v2"/><path d="m4.93 4.93 1.41 1.41"/><path d="m17.66 17.66 1.41 1.41"/><path d="M2 12h2"/><path d="M20 12h2"/><path d="m6.34 17.66-1.41 1.41"/><path d="m19.07 4.93-1.41 1.41"/></svg>`;
      const moonIcon = `<svg xmlns="http://www.w3.org/2000/svg" width="20" height="20" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"><path d="M12 3a6 6 0 0 0 9 9 9 9 0 1 1-9-9Z"/></svg>`;

      // Перевірка та встановлення збереженої теми
      const savedTheme = localStorage.getItem('theme');
      if (savedTheme === 'dark') {
        document.body.classList.add('dark-mode');
        themeToggle.innerHTML = sunIcon;
      } else {
        themeToggle.innerHTML = moonIcon;
      }

      // Перемикання теми
      themeToggle.addEventListener('click', () => {
        document.body.classList.toggle('dark-mode');
        const isDark = document.body.classList.contains('dark-mode');
        themeToggle.innerHTML = isDark ? sunIcon : moonIcon;
        localStorage.setItem('theme', isDark ? 'dark' : 'light');
      });

      // Перемикання вкладок
      tabButtons.forEach(btn => {
        btn.addEventListener('click', () => {
          const tabTarget = btn.dataset.tab;

          tabButtons.forEach(b => b.classList.remove('active'));
          tabContents.forEach(c => c.classList.remove('active'));

          btn.classList.add('active');
          document.getElementById(`tab-${tabTarget}`).classList.add('active');

          updateResult('?');
          if (tabTarget === 'wheel') drawWheel();
        });
      });

      function updateResult(value) {
        resultDisplay.textContent = value;
        resultDisplay.classList.remove('pop-animation');
        void resultDisplay.offsetWidth;
        resultDisplay.classList.add('pop-animation');
      }

      // --- 1. Генератор чисел ---
      document.getElementById('btn-gen-numbers').addEventListener('click', () => {
        const min = parseInt(document.getElementById('num-min').value, 10);
        const max = parseInt(document.getElementById('num-max').value, 10);
        const count = parseInt(document.getElementById('num-count').value, 10);

        if (isNaN(min) || isNaN(max) || isNaN(count)) return updateResult('Заповніть усі поля');
        if (min > max) return updateResult('Мін. має бути меншим за Макс.');

        const results = [];
        for (let i = 0; i < count; i++) {
          results.push(Math.floor(Math.random() * (max - min + 1)) + min);
        }
        updateResult(results.join(', '));
      });

      // --- 2. Вибір зі списку ---
      document.getElementById('btn-gen-list').addEventListener('click', () => {
        const rawText = document.getElementById('list-input').value;
        const items = rawText.split('\n').map(i => i.trim()).filter(i => i.length > 0);

        if (items.length === 0) return updateResult('Список порожній');
        const randomIndex = Math.floor(Math.random() * items.length);
        updateResult(items[randomIndex]);
      });

      // --- 3. Колесо фортуни ---
      const canvas = document.getElementById('wheel-canvas');
      const ctx = canvas.getContext('2d');
      const wheelInput = document.getElementById('wheel-input');
      const spinBtn = document.getElementById('btn-spin-wheel');

      let currentAngle = 0;
      let isSpinning = false;

      const colors = [
        '#4f46e5', '#10b981', '#f59e0b', '#ef4444', 
        '#8b5cf6', '#ec4899', '#06b6d4', '#84cc16'
      ];

      function getWheelItems() {
        return wheelInput.value
          .split(/[\n,]+/)
          .map(item => item.trim())
          .filter(item => item.length > 0);
      }

      function drawWheel() {
        const items = getWheelItems();
        const numOptions = items.length;
        const centerX = canvas.width / 2;
        const centerY = canvas.height / 2;
        const radius = centerX - 10;

        ctx.clearRect(0, 0, canvas.width, canvas.height);

        if (numOptions === 0) {
          ctx.beginPath();
          ctx.arc(centerX, centerY, radius, 0, 2 * Math.PI);
          ctx.fillStyle = '#cbd5e1';
          ctx.fill();
          ctx.fillStyle = '#475569';
          ctx.font = '14px sans-serif';
          ctx.textAlign = 'center';
          ctx.fillText('Додайте варіанти', centerX, centerY);
          return;
        }

        const arcSize = (2 * Math.PI) / numOptions;

        items.forEach((item, i) => {
          const angle = currentAngle + i * arcSize;

          ctx.beginPath();
          ctx.fillStyle = colors[i % colors.length];
          ctx.moveTo(centerX, centerY);
          ctx.arc(centerX, centerY, radius, angle, angle + arcSize);
          ctx.lineTo(centerX, centerY);
          ctx.fill();
          ctx.strokeStyle = '#ffffff';
          ctx.lineWidth = 2;
          ctx.stroke();

          ctx.save();
          ctx.translate(centerX, centerY);
          ctx.rotate(angle + arcSize / 2);
          ctx.textAlign = 'right';
          ctx.fillStyle = '#ffffff';
          ctx.font = 'bold 13px sans-serif';
          const textToDraw = item.length > 12 ? item.substring(0, 10) + '...' : item;
          ctx.fillText(textToDraw, radius - 15, 5);
          ctx.restore();
        });

        // Центр колеса
        ctx.beginPath();
        ctx.arc(centerX, centerY, 18, 0, 2 * Math.PI);
        ctx.fillStyle = '#ffffff';
        ctx.fill();
        ctx.stroke();
      }

      wheelInput.addEventListener('input', drawWheel);

      spinBtn.addEventListener('click', () => {
        const items = getWheelItems();
        if (items.length < 2) return updateResult('Додайте мін. 2 варіанти');
        if (isSpinning) return;

        isSpinning = true;
        spinBtn.disabled = true;

        const spinAngle = Math.floor(Math.random() * 360) + 1440;
        const duration = 3000;
        const startAngle = currentAngle;
        const startTime = performance.now();

        function animate(currentTime) {
          const elapsed = currentTime - startTime;
          const progress = Math.min(elapsed / duration, 1);

          const easeOut = 1 - Math.pow(1 - progress, 3);
          currentAngle = startAngle + (spinAngle * Math.PI / 180) * easeOut;

          drawWheel();

          if (progress < 1) {
            requestAnimationFrame(animate);
          } else {
            isSpinning = false;
            spinBtn.disabled = false;

            const numOptions = items.length;
            const arcSize = (2 * Math.PI) / numOptions;

            let normalizedAngle = (1.5 * Math.PI - (currentAngle % (2 * Math.PI))) % (2 * Math.PI);
            if (normalizedAngle < 0) normalizedAngle += 2 * Math.PI;

            const winningIndex = Math.floor(normalizedAngle / arcSize);
            updateResult(items[winningIndex]);
          }
        }

        requestAnimationFrame(animate);
      });

      // Початкове малювання колеса
      drawWheel();
    });
  </script>
</body>
</html>
