<template>
  <div class="modal-footer">
    <input
      v-model="message"
      type="text"
      placeholder="Say something..."
      @keyup.enter="sendMessage"
    />
    <div class="modal-footer-icon-section">
      <img
        class="cursor-pointer"
        style="width: 24px; height: 24px"
        src="~assets/images/icon-attach-file.png"
        alt="Attach file"
      />
      <div
        class="send-icon"
        :class="{ loading: isLoading }"
        @click="sendMessage"
      >
        <q-icon
          v-if="!isLoading"
          name="arrow_forward_ios"
          style="width: 24px; height: 24px"
          color="white"
        />
        <q-icon
          v-else
          name="refresh"
          style="width: 30px; height: 30px"
          color="white"
          class="spinning"
        />
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref } from 'vue'

const props = defineProps({
  isLoading: {
    type: Boolean,
    default: false,
  },
})

const message = ref('')

const emit = defineEmits<{
  sendMessage: [content: string]
}>()

const sendMessage = () => {
  if (message.value.trim() && !props.isLoading) {
    emit('sendMessage', message.value.trim())
    message.value = ''
  }
}
</script>

<style lang="scss" scoped>
.modal-footer {
  width: 100%;
  height: 68px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-top: 1px solid #e0e0e0;
  padding: 16px 20px;

  input {
    width: 100%;
    height: 100%;
    border: none;
    outline: none;
    font-size: 16px;
    color: #0d082c;
    opacity: 0.6;
  }

  .modal-footer-icon-section {
    display: flex;
    justify-content: space-between;
    align-items: center;
    gap: 16px;

    .send-icon {
      width: 36px;
      height: 36px;
      background-color: $teal-700;
      border-radius: 50%;
      display: flex;
      justify-content: center;
      align-items: center;
      cursor: pointer;
      transition: background-color 0.3s ease;

      &.loading {
        background-color: $gray-600;
        cursor: not-allowed;
      }

      .spinning {
        animation: spin 1s linear infinite;
      }
    }
  }
}

@keyframes spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}
</style>
