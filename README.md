# AI Chatroom Assignment

這是一個基於 Quasar Framework 的 AI 聊天室應用程序，模擬真實的 AI 對話體驗。

## 功能特色

### 🤖 AI 對話模擬
- **Thinking 動畫**：發送訊息後顯示 "Thinking..." 並有動態點點效果
- **10 秒延遲**：模擬真實 AI 處理時間
- **逐字渲染**：AI 回應以打字機效果逐字顯示
- **隨機失敗機制**：80% 成功率，20% 失敗率模擬真實情況

### 💬 智能回應系統
- **Mock 資料內容**：基於預設問題提供相應回答
- **Suggested Questions**：AI 回應後顯示建議問題供用戶點擊
- **Markdown 支援**：支援連結和粗體格式渲染

### 🎨 用戶體驗
- **自動滾動**：新訊息自動滾動到底部
- **Loading 狀態**：發送按鈕顯示旋轉動畫和狀態變化
- **響應式設計**：適配不同螢幕尺寸

## 實現說明

### input key 機制
由於 assignment 並沒有明確指出 input 的 key 是要完全相同或是部分相同，目前採用**完全相同**的方式處理。用戶輸入必須與 `src/mock/messages.js` 中的 key 完全一致才會觸發對應回應。

### 失敗率模擬
為了模擬真實的 AI 服務情況，系統設計了 4:1 的成功失敗比例：
- **80% 機率**：成功回應 mock 資料中的內容
- **20% 機率**：顯示錯誤訊息 "Sorry I encountered an error, please try again later."

### 可測試的問題
以下問題可以觸發 AI 回應：
- "Can you help me compare gloves products from different vendors?"
- "What are the most popular ultrasound gel products purchased by practices like mine?"
- "What product has the lowest price available for antibiotic ointments?"
- "How do prices for surgical scissors compare across vendors?"
- "Can you find cheaper substitutes for my masks purchases?"

## 技術架構

### 前端框架
- **Quasar Framework**: Vue 3 + TypeScript
- **SCSS**: 樣式預處理器
- **組件化設計**: 模組化的聊天室組件

### 核心組件
- `AiChatModal.vue`: 主聊天室容器，處理訊息狀態管理
- `AiChatContent.vue`: 訊息列表容器，處理滾動和事件傳遞
- `AiResponseMessage.vue`: AI 回應訊息組件，包含逐字渲染和建議問題
- `AiRequestMessage.vue`: 用戶訊息組件
- `AiChatModalFooter.vue`: 輸入框和發送按鈕，包含 loading 狀態

### 資料結構
```typescript
interface Message {
  role: 'user' | 'assistant'
  content: string
  timestamp: string
  isThinking?: boolean
  isTyping?: boolean
  fullContent?: string
}
```

## 開發說明

### 關鍵實現細節
1. **訊息匹配**: 使用完全匹配策略檢查 `MESSAGE_MOCK_MAP`
2. **狀態管理**: 使用 Vue 3 Composition API 管理聊天狀態
3. **異步處理**: 使用 Promise 和 setTimeout 模擬網路延遲
4. **事件傳遞**: 使用 emit/props 在組件間傳遞事件

### 樣式設計
- 遵循 Material Design 原則
- 使用 Quasar 的色彩系統 ($teal-700, $gray-0 等)
- 響應式佈局適配移動端和桌面端

## 安裝與運行

### Install the dependencies
```bash
yarn
# or
npm install
```

### Start the app in development mode (hot-code reloading, error reporting, etc.)
```bash
quasar dev
```


### Build the app for production
```bash
quasar build
```

### Customize the configuration
See [Configuring quasar.config.js](https://v2.quasar.dev/quasar-cli-vite/quasar-config-js).
