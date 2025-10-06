<script setup lang="ts">
import { ref, watch, nextTick } from 'vue'
import AiResponseMessage from './AiResponseMessage.vue'
import AiRequestMessage from './AiRequestMessage.vue'

const props = defineProps({
  messageList: {
    type: Array<{
      role: 'user' | 'assistant'
      content: string
      timestamp: string
    }>,
    required: true,
  },
})

const emit = defineEmits<{
  suggestedQuestionClick: [question: string]
}>()

// 處理建議問題點擊
const handleSuggestedQuestionClick = (question: string) => {
  emit('suggestedQuestionClick', question)
}

const chatContentRef = ref<HTMLElement>()

// 滾動到底部的函數
const scrollToBottom = () => {
  if (chatContentRef.value) {
    chatContentRef.value.scrollTop = chatContentRef.value.scrollHeight
  }
}

// 監聽消息列表變化，自動滾動到底部
watch(
  () => props.messageList,
  () => {
    nextTick(() => {
      scrollToBottom()
    })
  },
  { deep: true },
)
</script>

<template>
  <div ref="chatContentRef" class="ai-chat-content">
    <div v-for="message in messageList" :key="message.timestamp">
      <AiResponseMessage
        v-if="message.role === 'assistant'"
        :message="message"
        @suggestedQuestionClick="handleSuggestedQuestionClick"
      />
      <AiRequestMessage v-if="message.role === 'user'" :message="message" />
    </div>
  </div>
</template>

<style lang="scss" scoped>
.ai-chat-content {
  width: 100%;
  height: 100%;
  overflow-y: auto;
  padding: 30px 20px 30px; // 添加底部 padding
  display: flex;
  flex-direction: column;
  gap: 16px;
  scroll-behavior: smooth; // 平滑滾動效果
}
</style>
