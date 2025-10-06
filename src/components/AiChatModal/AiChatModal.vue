<template>
  <transition name="fade">
    <div v-if="isModalOpen" class="ai-chat-modal">
      <div class="modal-header">
        <div class="modal-header-title-text">
          <div class="modal-logo-section">
            <img
              class="modal-logo-section-logo"
              src="~assets/images/logo.png"
              alt="Nitra logo"
            />
            <div class="modal-logo-section-title text-bold">Nitra AI</div>
            <img
              class="modal-logo-section-magic-stick"
              src="~assets/images/nitra-magic-stick.png"
              alt="Nitra magic stick"
            />
          </div>
          <div>Hi there, How can we help?</div>
        </div>
        <q-icon
          class="modal-header-title-icon"
          name="close"
          @click="isModalOpen = false"
        />
      </div>

      <AiChatContent
        :messageList="messageList"
        @suggestedQuestionClick="handleSuggestedQuestionClick"
      />

      <AiChatModalFooter
        :isLoading="isLoading"
        @sendMessage="handleSendMessage"
      />
    </div>
  </transition>
</template>

<script setup lang="ts">
import { ref } from 'vue'
import AiChatContent from './AiChatContent.vue'
import AiChatModalFooter from './AiChatModalFooter.vue'
import { MESSAGE_MOCK_MAP } from '../../mock/messages.js'

const isLoading = ref(false)
const messageList = ref<
  {
    role: 'user' | 'assistant'
    content: string
    timestamp: string
    isThinking?: boolean
    isTyping?: boolean
  }[]
>([
  {
    role: 'assistant',
    content: 'Welcome to Nitra AI!',
    timestamp: new Date().toISOString(),
  },
])

const isModalOpen = defineModel('isModalOpen')

const handleSendMessage = async (content: string) => {
  // 設置 loading 狀態
  isLoading.value = true

  messageList.value.push({
    role: 'user',
    content,
    timestamp: new Date().toISOString(),
  })

  // 添加 "Thinking..." 消息
  const thinkingMessage = {
    role: 'assistant' as const,
    content: 'Thinking...',
    timestamp: new Date().toISOString(),
    isThinking: true,
  }
  messageList.value.push(thinkingMessage)

  // 等待 10 秒
  await new Promise((resolve) => setTimeout(resolve, 10000))

  // 移除 thinking 消息
  const thinkingIndex = messageList.value.findIndex((msg) => msg.isThinking)
  if (thinkingIndex !== -1) {
    messageList.value.splice(thinkingIndex, 1)
  }

  // 檢查是否有對應的 mock 回應
  const mockResponse = MESSAGE_MOCK_MAP[content.trim()]

  if (mockResponse) {
    // 隨機決定成功或失敗 (4:1 比例，80% 成功)
    const isSuccess = Math.random() < 0.8

    if (isSuccess) {
      // 成功：添加帶有打字效果的消息
      const aiMessage = {
        role: 'assistant' as const,
        content: '',
        timestamp: new Date().toISOString(),
        isTyping: true,
        fullContent: mockResponse.message.content,
      }
      messageList.value.push(aiMessage)

      // 開始打字效果後，更新消息狀態
      setTimeout(
        () => {
          const messageIndex = messageList.value.findIndex(
            (msg) => msg.timestamp === aiMessage.timestamp,
          )
          if (messageIndex !== -1) {
            messageList.value[messageIndex].isTyping = false
            messageList.value[messageIndex].content =
              mockResponse.message.content
          }
        },
        mockResponse.message.content.length * 30 + 1000,
      ) // 根據內容長度計算打字時間 (30ms per character + 1s buffer)
    } else {
      // 失敗：直接顯示錯誤消息
      messageList.value.push({
        role: 'assistant' as const,
        content: 'Sorry I encountered an error, please try again later.',
        timestamp: new Date().toISOString(),
      })
    }
  } else {
    // 沒有對應的 mock 回應
    messageList.value.push({
      role: 'assistant' as const,
      content: "Sorry, I don't have information about that topic.",
      timestamp: new Date().toISOString(),
    })
  }

  // 重置 loading 狀態
  isLoading.value = false
}

// 處理建議問題點擊
const handleSuggestedQuestionClick = (question: string) => {
  // 直接調用 handleSendMessage 來處理建議問題
  handleSendMessage(question)
}
</script>

<style lang="scss" scoped>
.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.2s;
}
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

.ai-chat-modal {
  position: fixed;
  bottom: 82px;
  right: 30px;
  background-color: white;
  border-radius: 8px;
  z-index: 1000;
  box-shadow: 0 20px 60px 0 rgba($teal-700, 0.25);
  width: 780px;
  height: 1082px; // figma 591px, but video shows 1082px
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  align-items: center;
  border: 1px solid #e0e0e0;

  .modal-header {
    background-color: $teal-700;
    width: 100%;
    height: 102px;
    display: flex;
    justify-content: flex-start;
    align-items: center;
    border-radius: 8px 8px 0 0;
    color: white;
    padding: 18px 20px;
  }

  .modal-logo-section {
    display: flex;
    justify-content: flex-start;
    align-items: center;
    gap: 16px;

    .modal-logo-section-logo {
      width: 46px;
    }

    .modal-logo-section-title {
      font-size: 30px;
    }

    .modal-logo-section-magic-stick {
      width: 24px;
      height: 24px;
    }
  }

  .modal-header-title-text {
    display: flex;
    flex-direction: column;
    justify-content: flex-start;
    align-items: flex-start;
    font-size: 16px;
  }

  .modal-header-title-icon {
    font-size: 24px;
    cursor: pointer;
    position: absolute;
    right: 20px;
    top: 18px;
  }
}
</style>
