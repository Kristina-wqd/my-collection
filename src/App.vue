<template>
  <div class="container mt-5">
    <h1 class="text-center mb-4">Моя коллекция книг</h1>
    
    <!-- Форма добавления -->
    <div class="row">
      <div class="col-md-8 offset-md-2">
        <form @submit.prevent="addBook" class="card p-4 mb-4">
          <div class="mb-3">
            <label class="form-label">Название книги</label>
            <input 
              v-model="newBook.title" 
              type="text" 
              class="form-control"
              placeholder="Введите название"
              required
            >
          </div>
          
          <div class="mb-3">
            <label class="form-label">Описание</label>
            <textarea 
              v-model="newBook.description" 
              class="form-control"
              rows="3"
              placeholder="Введите описание книги"
            ></textarea>
          </div>
          
          <div class="mb-3">
            <label class="form-label">URL изображения</label>
            <input 
              v-model="newBook.image" 
              type="url" 
              class="form-control"
              placeholder="https://example.com/image.jpg"
            >
          </div>
          
          <button type="submit" class="btn btn-primary">Добавить книгу</button>
        </form>
      </div>
    </div>

    <!-- Сортировка -->
    <div class="row mb-4">
      <div class="col-md-4">
        <select class="form-select" v-model="sortBy">
          <option value="title">Сортировать по названию</option>
          <option value="id">Сортировать по дате добавления</option>
        </select>
      </div>
    </div>

    <!-- Список книг -->
    <div class="row">
      <div 
        v-for="book in sortedBooks" 
        :key="book.id" 
        class="col-md-4 mb-4"
      >
        <div class="card h-100">
          <!-- ИСПРАВЛЕННЫЙ БЛОК С ИЗОБРАЖЕНИЕМ -->
          <div class="text-center bg-light" style="height: 200px; display: flex; align-items: center; justify-content: center;">
            <img 
              v-if="book.image && book.image !== 'https://example.com/image.jpg'" 
              :src="book.image" 
              @error="handleImageError"
              style="max-width: 100%; max-height: 100%; object-fit: contain;"
              :alt="book.title"
            >
            <div v-else style="font-size: 80px;">📚</div>
          </div>
          
          <div class="card-body">
            <h5 class="card-title">{{ book.title }}</h5>
            <p class="card-text">{{ book.description || 'Нет описания' }}</p>
            <button 
              @click="removeBook(book.id)" 
              class="btn btn-danger btn-sm"
            >
              Удалить
            </button>
          </div>
          <div class="card-footer text-muted">
            Добавлено: {{ new Date(book.id).toLocaleDateString() }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'

// Состояние приложения
const books = ref([
  {
    id: 1,
    title: 'Пример книги',
    description: 'Это пример описания книги',
    image: '' // Пустая строка вместо ссылки
  }
])

const sortBy = ref('id')

// Форма добавления
const newBook = ref({
  title: '',
  description: '',
  image: ''
})

// Сортировка книг
const sortedBooks = computed(() => {
  return [...books.value].sort((a, b) => {
    if (sortBy.value === 'title') {
      return a.title.localeCompare(b.title)
    } else {
      return b.id - a.id
    }
  })
})

// Обработка ошибок загрузки изображений
const handleImageError = (event) => {
  event.target.style.display = 'none'
  event.target.parentElement.innerHTML = '<div style="font-size: 80px;">📚</div>'
}

// Добавление книги
const addBook = () => {
  if (!newBook.value.title) return
  
  books.value.push({
    id: Date.now(),
    title: newBook.value.title,
    description: newBook.value.description,
    image: newBook.value.image || '' // Пустая строка, если нет URL
  })
  
  // Очистка формы
  newBook.value = {
    title: '',
    description: '',
    image: ''
  }
}

// Удаление книги
const removeBook = (id) => {
  books.value = books.value.filter(book => book.id !== id)
}
</script>

<style>
.card {
  transition: transform 0.2s;
}
.card:hover {
  transform: translateY(-5px);
  box-shadow: 0 4px 15px rgba(0,0,0,0.1);
}
</style>