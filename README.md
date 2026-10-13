<!-- 核心介面佈局建議 -->
<div class="game-container">
  <!-- 左側：購物清單（高對比、大字體、語音支援） -->
  <aside class="shopping-list-panel">
    <h2>媽媽的購物單</h2>
    <ul class="task-list">
      <!-- 每個項目包含大字繁體中文、發音按鈕及完成勾選框 -->
      <li class="task-item" data-target="蘋果">
        <span class="vocab-text">蘋 果</span>
        <button class="audio-btn" aria-label="聆聽廣東話讀音">🔊</button>
        <span class="status-icon">⬜</span>
      </li>
      <!-- 其餘4個詞彙依次排列 -->
    </ul>
  </aside>

  <!-- 右側：超級市場貨架區（實物照片、無反光白底/柔和底） -->
  <main class="supermarket-shelf">
    <!-- 目標物（5件） + 干擾物（3-4件，例如：牙刷、波鞋、玩具車、香蕉） -->
    <div class="item-card" data-name="蘋果">
      <img src="apple_real.jpg" alt="紅色蘋果實物照" />
    </div>
    <!-- 點擊或拖曳至購物車 -->
  </main>
  
  <div class="shopping-cart-dropzone">
    🛒 購物車（將食物放進這裡）
  </div>
</div>
