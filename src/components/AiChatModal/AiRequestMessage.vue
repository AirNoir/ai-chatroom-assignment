<script setup lang="ts">
const props = defineProps({
  message: {
    type: Object,
    required: true,
  },
})
</script>

<template>
  <div class="ai-request-message-container">
    <div class="ai-request-message">
      {{ message.content }}
    </div>
  </div>
</template>

<style lang="scss" scoped>
.ai-request-message-container {
  display: flex;
  flex-direction: row;
  justify-content: flex-end;
  align-items: flex-start;
  gap: 10px;

  .ai-request-message {
    display: flex;
    background-color: $teal-100;
    color: #0d082c;
    font-size: 16px;
    padding: 10px 16px;
    border-radius: 10px 0px 10px 10px;
    max-width: 600px;
  }
}
</style>
