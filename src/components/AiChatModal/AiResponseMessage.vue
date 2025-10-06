<script setup lang="ts">
import { ref, onMounted, watch, nextTick } from 'vue'

const props = defineProps({
  message: {
    type: Object,
    required: true,
  },
})

const displayedContent = ref('')
const thinkingDots = ref('')
const mainContent = ref('')
const suggestedQuestion = ref('')
const showSuggestedQuestion = ref(false)

const emit = defineEmits<{
  suggestedQuestionClick: [question: string]
}>()

// 處理建議問題點擊
const handleSuggestedQuestionClick = () => {
  if (suggestedQuestion.value) {
    emit('suggestedQuestionClick', suggestedQuestion.value)
  }
}

// Thinking... 動態效果
const animateThinking = () => {
  let dotCount = 0
  const interval = setInterval(() => {
    dotCount = (dotCount + 1) % 4
    thinkingDots.value = '.'.repeat(dotCount)

    // 如果消息不再是 thinking 狀態，停止動畫
    if (!props.message.isThinking) {
      clearInterval(interval)
    }
  }, 300)
}

// 解析內容，分離主要內容和建議問題
const parseContent = (content: string) => {
  const suggestedQuestionRegex = /Suggested? Question:\s*(.+)$/i
  const match = content.match(suggestedQuestionRegex)

  if (match) {
    const mainContentText = content.replace(suggestedQuestionRegex, '').trim()
    const suggestedQuestionText = match[1].trim()

    return {
      mainContent: mainContentText,
      suggestedQuestion: suggestedQuestionText,
    }
  }

  return {
    mainContent: content,
    suggestedQuestion: '',
  }
}

// 逐字渲染效果
const typewriterEffect = async (content: string) => {
  displayedContent.value = ''
  showSuggestedQuestion.value = false
  let currentIndex = 0

  // 解析內容
  const parsed = parseContent(content)
  mainContent.value = parsed.mainContent
  suggestedQuestion.value = parsed.suggestedQuestion

  // 逐字添加主要內容
  while (currentIndex < parsed.mainContent.length) {
    displayedContent.value = parsed.mainContent.substring(0, currentIndex + 1)
    currentIndex++
    await new Promise((resolve) => setTimeout(resolve, 10)) // 每個字符間隔 10ms
  }

  // 主要內容完成後，顯示建議問題
  if (parsed.suggestedQuestion) {
    showSuggestedQuestion.value = true
  }
}

// 渲染 HTML 內容
const renderContent = (content: string) => {
  return content
    .replace(
      /\[([^\]]+)\]\(([^)]+)\)/g,
      '<a href="$2" target="_blank" rel="noopener noreferrer" class="message-link">$1</a>',
    )
    .replace(/\*\*([^*]+)\*\*/g, '<strong>$1</strong>')
    .replace(/\n/g, '<br>')
}

// 監聽消息變化
watch(
  () => props.message,
  (newMessage) => {
    if (newMessage.isThinking) {
      animateThinking()
    } else if (newMessage.isTyping && newMessage.fullContent) {
      typewriterEffect(newMessage.fullContent)
    } else {
      // 解析靜態內容
      const parsed = parseContent(newMessage.content)
      mainContent.value = parsed.mainContent
      suggestedQuestion.value = parsed.suggestedQuestion
      displayedContent.value = parsed.mainContent
      showSuggestedQuestion.value = !!parsed.suggestedQuestion
    }
  },
  { immediate: true },
)

onMounted(() => {
  if (props.message.isThinking) {
    animateThinking()
  } else if (props.message.isTyping && props.message.fullContent) {
    typewriterEffect(props.message.fullContent)
  } else {
    // 解析靜態內容
    const parsed = parseContent(props.message.content)
    mainContent.value = parsed.mainContent
    suggestedQuestion.value = parsed.suggestedQuestion
    displayedContent.value = parsed.mainContent
    showSuggestedQuestion.value = !!parsed.suggestedQuestion
  }
})
</script>

<template>
  <div class="ai-response-message-container">
    <div class="ai-response-message-container-logo">
      <img src="~assets/images/logo.png" alt="AI response message" />
    </div>

    <div class="ai-response-message-content">
      <div class="ai-response-message">
        <div v-if="message.isThinking" class="thinking-message">
          Thinking{{ thinkingDots }}
        </div>
        <div
          v-else-if="message.isTyping"
          v-html="renderContent(displayedContent)"
        ></div>
        <div v-else v-html="renderContent(displayedContent)"></div>
      </div>

      <!-- Suggested Question 區塊 -->
      <div
        v-if="showSuggestedQuestion && !message.isThinking"
        class="ai-response-message suggested-question-message"
        @click="handleSuggestedQuestionClick"
      >
        {{ suggestedQuestion }}
      </div>
    </div>
  </div>
</template>

<style lang="scss" scoped>
.ai-response-message-container {
  display: flex;
  flex-direction: row;
  align-items: flex-start;
  gap: 10px;

  .ai-response-message-container-logo {
    min-width: 24px;
    min-height: 24px;
    background-color: $teal-700;
    border-radius: 50%;
    display: flex;
    justify-content: center;
    align-items: center;

    img {
      width: 16px;
      height: 8px;
    }
  }

  .ai-response-message-content {
    display: flex;
    flex-direction: column;
    gap: 8px;
    max-width: 600px;
  }

  .ai-response-message {
    display: flex;
    background-color: $gray-0;
    color: #0d082c;
    font-size: 16px;
    padding: 10px 16px;
    border-radius: 0px 10px 10px 10px;

    .thinking-message {
      opacity: 0.7;
      min-width: 80px; // 固定最小寬度避免忽大忽小
    }

    :deep(.message-link) {
      color: $teal-700;
      text-decoration: underline;

      &:hover {
        color: $teal-800;
      }
    }

    :deep(strong) {
      font-weight: bold;
    }
  }

  .suggested-question-message {
    cursor: pointer;
    transition: opacity 0.2s ease;

    &:hover {
      opacity: 0.8;
    }
  }
}
</style>
