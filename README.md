<!DOCTYPE html>
<html lang="zh-HK">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
  <title>超級市場小幫手：幫媽媽買食物</title>
  <style>
    :root {
      --bg-color: #F7F5EB; /* 護眼米黃底色，減少白底眩光 */
      --card-bg: #FFFFFF;
      --text-main: #1F2937; /* 高對比深色文字 (對比度 > 7:1) */
      --accent-green: #2E7D32;
      --accent-blue: #1565C0;
      --border-color: #D1D5DB;
      --highlight-color: #FFF9C4;
    }

    * {
      box-sizing: border-box;
      -webkit-tap-highlight-color: transparent;
    }

    body {
      margin: 0;
      padding: 16px;
      background-color: var(--bg-color);
      /* 標準無裝飾黑體，避免字形干擾 */
      font-family: "Noto Sans TC", "PingFang HK", "Microsoft JhengHei", "微軟正黑體", sans-serif;
      color: var(--text-main);
      user-select: none;
    }

    /* 頂部控制列 */
    header {
      display: flex;
      justify-content: space-between;
      align-items: center;
      margin-bottom: 16px;
      padding-bottom: 12px;
      border-bottom: 2px solid var(--border-color);
    }

    h1 {
      font-size: 26px;
      margin: 0;
      letter-spacing: 0.1em;
    }

    .scaffold-toggle {
      display: flex;
      align-items: center;
      gap: 10px;
      font-size: 18px;
      font-weight: bold;
    }

    .scaffold-toggle button {
      padding: 8px 16px;
      font-size: 16px;
      border: 2px solid var(--accent-blue);
      border-radius: 20px;
      background: white;
      color: var(--accent-blue);
      cursor: pointer;
    }

    .scaffold-toggle button.active {
      background: var(--accent-blue);
      color: white;
    }

    /* 主遊戲區域 */
    .game-container {
      display: grid;
      grid-template-columns: 340px 1fr;
      gap: 20px;
      height: calc(100vh - 170px);
    }

    /* 左側清單面版 */
    .shopping-list-panel {
      background: var(--card-bg);
      border-radius: 18px;
      padding: 20px;
      border: 2px solid var(--border-color);
      display: flex;
      flex-direction: column;
      box-shadow: 0 4px 6px rgba(0, 0, 0, 0.05);
    }

    .shopping-list-panel h2 {
      font-size: 24px;
      margin-top: 0;
      text-align: center;
      border-bottom: 2px dashed var(--border-color);
      padding-bottom: 12px;
    }

    .task-list {
      list-style: none;
      padding: 0;
      margin: 0;
      display: flex;
      flex-direction: column;
      gap: 14px;
      flex: 1;
    }

    .task-item {
      display: flex;
      align-items: center;
      justify-content: space-between;
      padding: 10px 14px;
      background: #FAFAFA;
      border: 2px solid #E5E7EB;
      border-radius: 12px;
      transition: all 0.2s ease;
    }

    .task-item.completed {
      background: #E8F5E9;
      border-color: var(--accent-green);
    }

    /* 關鍵字體樣式：加大字距，降低視覺擁擠 */
    .vocab-container {
      display: flex;
      align-items: center;
      gap: 10px;
    }

    .vocab-hint-img {
      width: 32px;
      height: 32px;
      opacity: 0.65;
      display: inline-block;
    }

    .body-mode-advanced .vocab-hint-img {
      display: none;
    }

    .vocab-text {
      font-size: 40px; /* 大字體防擠迫 */
      font-weight: 700;
      letter-spacing: 0.15em;
      line-height: 1.2;
    }

    .audio-btn {
      background: #EEF2FF;
      border: 2px solid #C7D2FE;
      font-size: 24px;
      border-radius: 50%;
      width: 48px;
      height: 48px;
      display: flex;
      align-items: center;
      justify-content: center;
      cursor: pointer;
    }

    .status-icon {
      font-size: 26px;
      width: 32px;
      text-align: center;
    }

    /* 右側主要貨架區 */
    .store-area {
      display: flex;
      flex-direction: column;
      gap: 16px;
    }

    .supermarket-shelf {
      background: var(--card-bg);
      border-radius: 18px;
      border: 2px solid var(--border-color);
      padding: 24px;
      flex: 1;
      display: grid;
      grid-template-columns: repeat(4, 1fr);
      grid-gap: 20px;
      align-content: start;
      box-shadow: 0 4px 6px rgba(0, 0, 0, 0.05);
    }

    .item-card {
      background: #FFFFFF;
      border: 3px solid #E5E7EB;
      border-radius: 16px;
      padding: 16px;
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
      cursor: pointer;
      position: relative;
      transition: transform 0.15s ease, border-color 0.2s ease;
      touch-action: manipulation;
    }

    .item-card:active {
      transform: scale(0.96);
    }

    .item-card.selected {
      border-color: var(--accent-green);
      background-color: #F0FDF4;
      opacity: 0.35;
      pointer-events: none;
    }

    .item-card svg {
      width: 80px;
      height: 80px;
      filter: drop-shadow(0 2px 3px rgba(0,0,0,0.1));
    }

    /* 底部購物車放置區 */
    .shopping-cart-bar {
      height: 90px;
      background: #FFFBEB;
      border: 3px dashed #F59E0B;
      border-radius: 18px;
      display: flex;
      align-items: center;
      justify-content: center;
      font-size: 24px;
      font-weight: bold;
      color: #B45309;
      gap: 16px;
    }

    /* 提示訊息回饋框（溫和正面提示） */
    .feedback-toast {
      position: fixed;
      bottom: 24px;
      left: 50%;
      transform: translateX(-50%);
      background: rgba(31, 41, 55, 0.92);
      color: #FFFFFF;
      padding: 14px 28px;
      border-radius: 30px;
      font-size: 20px;
      letter-spacing: 0.08em;
      opacity: 0;
      pointer-events: none;
      transition: opacity 0.3s ease;
      z-index: 99;
    }

    .feedback-toast.show {
      opacity: 1;
    }
  </style>
</head>
<body class="body-mode-easy">

  <header>
    <h1>🛒 超級市場小幫手：幫媽媽買食物</h1>
    <div class="scaffold-toggle">
      <span>難度模式：</span>
      <button id="btnEasy" class="active" onclick="setMode('easy')">初階（圖字提示）</button>
      <button id="btnAdvanced" onclick="setMode('advanced')">進階（純文字認讀）</button>
    </div>
  </header>

  <div class="game-container">
    <!-- 左側：購物清單 -->
    <aside class="shopping-list-panel">
      <h2>媽媽的購物單</h2>
      <ul class="task-list" id="taskList">
        <!-- JS 動態生成任務項目 -->
      </ul>
    </aside>

    <!-- 右側：貨架與購物車 -->
    <section class="store-area">
      <main class="supermarket-shelf" id="shelf">
        <!-- JS 動態生成貨架物品（5件目標 + 3件干擾） -->
      </main>

      <div class="shopping-cart-bar">
        <span>🛒 購物車</span>
        <span id="cartCount">(已放入 0 / 5 件)</span>
      </div>
    </section>
  </div>

  <div id="feedbackToast" class="feedback-toast"></div>

  <script>
    // 1. 目標物品與干擾物品定義 (使用簡約去背 SVG，兼具清晰度與輪廓識別)
    const itemsData = [
      {
        id: "apple",
        name: "蘋果",
        isTarget: true,
        speech: "蘋 果",
        svg: `<svg viewBox="0 0 64 64"><circle cx="32" cy="36" r="22" fill="#E53935"/><path d="M32 14 Q34 6 38 4" stroke="#5D4037" stroke-width="3" fill="none" stroke-linecap="round"/><path d="M34 10 Q44 6 42 16 Z" fill="#4CAF50"/></svg>`
      },
      {
        id: "egg",
        name: "雞蛋",
        isTarget: true,
        speech: "雞 蛋",
        svg: `<svg viewBox="0 0 64 64"><path d="M32 8 C20 8 14 26 14 40 C14 52 22 58 32 58 C42 58 50 52 50 40 C50 26 44 8 32 8 Z" fill="#D7CCC8" stroke="#A1887F" stroke-width="2"/></svg>`
      },
      {
        id: "bread",
        name: "麵包",
        isTarget: true,
        speech: "麵 包",
        svg: `<svg viewBox="0 0 64 64"><rect x="14" y="22" width="36" height="30" rx="4" fill="#FFE082"/><path d="M10 24 C10 14 20 12 32 12 C44 12 54 14 54 24 Z" fill="#FFB74D"/></svg>`
      },
      {
        id: "milk",
        name: "牛奶",
        isTarget: true,
        speech: "牛 奶",
        svg: `<svg viewBox="0 0 64 64"><polygon points="20,24 44,24 40,12 24,12" fill="#90CAF9"/><rect x="20" y="24" width="24" height="32" rx="2" fill="#E3F2FD" stroke="#1E88E5" stroke-width="2"/><text x="24" y="44" font-size="12" fill="#1565C0" font-weight="bold">MILK</text></svg>`
      },
      {
        id: "watermelon",
        name: "西瓜",
        isTarget: true,
        speech: "西 瓜",
        svg: `<svg viewBox="0 0 64 64"><path d="M12 20 A24 24 0 0 0 52 20 Z" fill="#E53935" stroke="#2E7D32" stroke-width="4"/><circle cx="26" cy="26" r="2" fill="#212121"/><circle cx="38" cy="26" r="2" fill="#212121"/><circle cx="32" cy="32" r="2" fill="#212121"/></svg>`
      },
      // 干擾項目（3件：牙刷、波鞋、玩具車）
      {
        id: "toothbrush",
        name: "牙刷",
        isTarget: false,
        speech: "這是牙刷，不是食物喔，再找找看！",
        svg: `<svg viewBox="0 0 64 64"><rect x="30" y="10" width="4" height="44" rx="2" fill="#4DD0E1"/><rect x="26" y="8" width="12" height="10" rx="2" fill="#FFFFFF" stroke="#00BCD4" stroke-width="1.5"/></svg>`
      },
      {
        id: "shoe",
        name: "波鞋",
        isTarget: false,
        speech: "這是波鞋，不是食物喔，再找找看！",
        svg: `<svg viewBox="0 0 64 64"><path d="M10 40 L16 26 L30 26 L38 34 L54 36 L54 44 L10 44 Z" fill="#AB47BC"/></svg>`
      },
      {
        id: "car",
        name: "玩具車",
        isTarget: false,
        speech: "這是玩具車，不是食物喔，再找找看！",
        svg: `<svg viewBox="0 0 64 64"><rect x="12" y="24" width="40" height="16" rx="4" fill="#FF7043"/><circle cx="22" cy="44" r="6" fill="#424242"/><circle cx="42" cy="44" r="6" fill="#424242"/></svg>`
      }
    ];

    let collectedCount = 0;
    const targetItems = itemsData.filter(i => i.isTarget);

    // 2. 廣東話語音朗讀 (Web Speech API)
    function speakCantonese(text) {
      if ('speechSynthesis' in window) {
        window.speechSynthesis.cancel(); // 停止上一段避免重疊干擾
        const utterance = new SpeechSynthesisUtterance(text);
        utterance.lang = 'zh-HK'; // 指定廣東話
        utterance.rate = 0.85;    // 減慢語速，適合幼童聽辨
        utterance.pitch = 1.05;   // 語調親和
        window.speechSynthesis.speak(utterance);
      }
    }

    // 3. 介面初始化
    function initUI() {
      const taskList = document.getElementById('taskList');
      const shelf = document.getElementById('shelf');

      // 渲染購物單
      taskList.innerHTML = targetItems.map(item => `
        <li class="task-item" id="task-${item.id}">
          <div class="vocab-container">
            <span class="vocab-hint-img">${item.svg}</span>
            <span class="vocab-text">${item.name}</span>
          </div>
          <button class="audio-btn" onclick="speakCantonese('${item.speech}')" aria-label="聆聽${item.name}">🔊</button>
          <span class="status-icon" id="status-${item.id}">⬜</span>
        </li>
      `).join('');

      // 隨機打散貨架物件（8件）
      const shuffled = [...itemsData].sort(() => Math.random() - 0.5);

      // 渲染貨架
      shelf.innerHTML = shuffled.map(item => `
        <div class="item-card" id="card-${item.id}" onclick="handleItemClick('${item.id}')">
          ${item.svg}
        </div>
      `).join('');
    }

    // 4. 點選與正向回饋機制
    function handleItemClick(itemId) {
      const item = itemsData.find(i => i.id === itemId);
      const card = document.getElementById(`card-${itemId}`);

      if (item.isTarget) {
        // 選對目標食物
        const taskRow = document.getElementById(`task-${item.id}`);
        const statusIcon = document.getElementById(`status-${item.id}`);

        if (!taskRow.classList.contains('completed')) {
          taskRow.classList.add('completed');
          statusIcon.innerText = "✅";
          card.classList.add('selected');
          
          collectedCount++;
          document.getElementById('cartCount').innerText = `(已放入 ${collectedCount} / 5 件)`;

          speakCantonese(`好叻！買咗${item.name}！`);
          showToast(`太棒了！找到「${item.name}」了！`);

          if (collectedCount === 5) {
            setTimeout(() => {
              speakCantonese("全部買齊喇！媽媽讚你真係超級小幫手！");
              showToast("🎉 全部買齊了！完成任務！");
            }, 1200);
          }
        }
      } else {
        // 點中干擾項目：柔和溫暖語音回饋，不扣分、不設懲罰
        speakCantonese(item.speech);
        showToast(item.speech);
      }
    }

    // 溫和回饋小提示
    function showToast(msg) {
      const toast = document.getElementById('feedbackToast');
      toast.innerText = msg;
      toast.classList.add('show');
      setTimeout(() => {
        toast.classList.remove('show');
      }, 2500);
    }

    // 5. 鷹架調節：切換「初階（圖字模式）」與「進階（純文字模式）」
    function setMode(mode) {
      const btnEasy = document.getElementById('btnEasy');
      const btnAdv = document.getElementById('btnAdvanced');

      if (mode === 'easy') {
        document.body.className = 'body-mode-easy';
        btnEasy.classList.add('active');
        btnAdv.classList.remove('active');
      } else {
        document.body.className = 'body-mode-advanced';
        btnAdv.classList.add('active');
        btnEasy.classList.remove('active');
      }
    }

    // 頁面載入執行
    window.addEventListener('DOMContentLoaded', initUI);
  </script>
</body>
</html>
