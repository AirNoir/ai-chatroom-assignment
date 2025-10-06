<script setup>
import AiChatModal from 'components/AiChatModal'
import { ref } from 'vue'

const isModalOpen = ref(false)

const toggleModal = () => {
  isModalOpen.value = !isModalOpen.value
}
</script>

<template>
  <q-page class="flex flex-center">
    <AiChatModal v-model:isModalOpen="isModalOpen" />
    <footer class="footer">
      <q-btn @click="toggleModal" class="btn-chat" color="#fb7429" rounded>
       <img class="magic-stick-logo" src="~assets/images/magic-stick.png" alt="Nitra AI" ></img>
        <div class="btn-chat-text">Ask Nitra AI</div>
      </q-btn>
    </footer>
  </q-page>
</template>

<style scoped>
.footer {
  position: fixed;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 78px;
  display: flex;
  justify-content: flex-end;
  padding-right: 20px;
  padding-top: 12px;
}

.btn-chat {
  width: 137px;
  height: 36px;
  background-color: #fb7429;
  color: #fff;

  .magic-stick-logo {
    width: 16px;
    height: 16px;
    margin-right: 8px;
  }

  .btn-chat-text {
    font-size: 14px;
    font-weight: 600;
    letter-spacing: 0.005em;
    text-transform: none;
  }
}
</style>
