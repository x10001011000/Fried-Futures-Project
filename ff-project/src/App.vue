<script setup lang="ts">
import { ref, onMounted } from 'vue'
import type { Ref } from 'vue'

interface Message {
  id: number;
  author: string;
  text: string;
}

interface Page {
  id: string;
  name: string;
}

const currentPage: Ref<string> = ref('info')
const newMessage: Ref<string> = ref('')
const messages: Ref<Message[]> = ref([
  { id: 1, author: 'Бот', text: 'Привет! Спроси что-нибудь о финансовой математике' }
])

const requestTimes: Ref<number[]> = ref([])
const isBlocked: Ref<boolean> = ref(false)

const pages: Page[] = [
  { id: 'info', name: 'Информация' },
  { id: 'articles', name: 'Статьи' },
  { id: 'assistent', name: 'Ассистент' },
  { id: 'trade-box', name: 'Trade box' },
  { id: 'internships', name: 'Стажировки' }
]

const sendMessage = (): void => {
  if (isBlocked.value) return
  if (!newMessage.value.trim()) return
  
  const now = Date.now()
  
  requestTimes.value = requestTimes.value.filter(
    time => now - time < 60000
  )
  
  if (requestTimes.value.length >= 10) {
    isBlocked.value = true
    setTimeout(() => {
      isBlocked.value = false
    }, 60000)
    return
  }
  
  requestTimes.value.push(now)
  
  messages.value.push({
    id: now,
    author: 'Вы',
    text: newMessage.value
  })
  
  newMessage.value = ''
  
  setTimeout(() => {
    messages.value.push({
      id: now + 1,
      author: 'Бот',
      text: 'Ошибка соединения'
    })
  }, 300)
}

onMounted(() => {
  const flickerElements = document.querySelectorAll<HTMLElement>('.flicker')
  
  setInterval(() => {
    flickerElements.forEach(el => {
      if (Math.random() > 0.7) {
        el.style.opacity = (Math.random() * 0.2 + 0.8).toString()
      }
    })
  }, 300)
})
</script>

<template>
  <div id="app">
    <div class="header-container">
      <div class="gif-container">
        <div class="gif-placeholder">
          <img src="./assets/11605171740224560.gif" alt="Гифка">
        </div>
      </div>
      
      <h1 class="flicker">Fried Futures</h1>
      
      <div class="gif-container">
        <div class="gif-placeholder">
          <img src="./assets/Cubic_pyramid_rotating.gif" alt="Гифка1">
        </div>
      </div>
    </div>
    
    <nav>
      <div class="nav-container">
        <button 
          v-for="page in pages" 
          :key="page.id"
          class="nav-button"
          :class="{ current: currentPage === page.id }"
          @click="currentPage = page.id"
        >
          {{ page.name }}
        </button>
      </div>
    </nav>
    
    <main>
      <section v-if="currentPage === 'info'">
        <h2>Информация о проекте</h2>
        <p>Fried Futures - команда энтузиастов в области финансового моделирования.</p>
        <p>Это обучающий проект команды, который может быть полезен начинающим и практикующим квантам.</p>
        <p>Здесь Вы сможете улучшить свои знания - почитать статьи в разделе "Статьи", задать каверзные вопросы "Ассистенту", а также попрактиковаться в разделе Trade Box.</p>
      </section>
      
      <section v-if="currentPage === 'articles'">
        <h2>Статьи</h2>
      </section>
      
      <section v-if="currentPage === 'assistent'">
        <h2>Спроси!</h2>
        <div class="chat-app">
          <div class="messages-area">
            <div v-for="msg in messages" :key="msg.id" class="message">
              <strong>{{ msg.author }}:</strong> {{ msg.text }}
            </div>
          </div>
          
          <div class="input-area">
            <input 
              v-model="newMessage" 
              @keyup.enter="sendMessage"
              placeholder="Введите сообщение..."
              :disabled="isBlocked"
            >
            <button 
              @click="sendMessage"
              :disabled="isBlocked"
            >
              Отправить
            </button>
          </div>
        </div>
      </section>
      
      <section v-if="currentPage === 'trade-box'">
        <h2>Страница в разработке</h2>
      </section>
      
      <section v-if="currentPage === 'internships'">
        <h2>Актуальных стажировок нет</h2>
      </section>
      
      <div class="tech-border"></div>
    </main>
    
    <footer>
      <div class="contacts">Контакты</div>
      <a href="https://t.me/fried_future" class="tg-link">Telegram</a>
    </footer>
  </div>
</template>

<style scoped></style>