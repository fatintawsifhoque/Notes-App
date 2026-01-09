<template>
  <section class="min-h-screen w-screen bg-linear-to-br from-sky-100 to-indigo-50 p-4">
    <header class="pt-6 text-center">
      <h1 class="text-3xl md:text-4xl font-bold text-gray-800">My Notes</h1>
      <p class="text-gray-700 mt-2 text-lg">Write, save, and organize your thoughts</p>
    </header>

    <!-- Add Note Form -->
    <div class="max-w-2xl mx-auto mt-8">
      <div class="flex flex-col gap-4">
        <input
          v-model="title"
          type="text"
          placeholder="Note Title"
          class="w-full px-4 py-3 border-2 border-green-600 rounded-xl focus:outline-none focus:ring-2 focus:ring-green-500"
        />
        <textarea
          v-model="content"
          placeholder="Write your note here..."
          rows="4"
          class="w-full px-4 py-3 border-2 border-green-600 rounded-xl focus:outline-none focus:ring-2 focus:ring-green-500 resize-none"
        ></textarea>
        <button @click="add" class="add self-center mt-2 px-6 py-2">Add Note</button>
      </div>
    </div>

    <!-- Notes List -->
    <div class="max-w-5xl mx-auto mt-12">
      <div v-if="notes.length === 0" class="text-center text-gray-500 py-10">
        No notes yet. Add one above!
      </div>

      <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-3 xl:grid-cols-4 gap-5 border border-[#450693] p-4 rounded-lg">
        <div
          v-for="note in notes"
          :key="note.id"
          class="note-card border border-gray-300 rounded-xl p-4 bg-white shadow-sm transition-all duration-300 hover:shadow-md flex flex-col"
        >
          <h2 class="font-bold text-lg text-gray-800 mb-2 overflow-hidden">{{ note.title || 'Untitled' }}</h2>
          
          <div class="text-gray-600 text-sm leading-relaxed overflow-hidden">
            {{ note.content }}
          </div>

          <div class="mt-3 text-xs text-gray-400">
            Saved: {{ formatTime(note.timestamp) }}
          </div>

          <button
            @click="del(note.id)"
            class="mt-2 text-xs bg-red-100 text-red-700 px-2 py-1 rounded hover:bg-red-200 transition self-start"
          >
            Delete
          </button>
        </div>
      </div>
    </div>

    <!-- Signature -->
    <footer class="mt-16 text-center pb-6">
      <p class="text-gray-600">Developed by</p>
      <a
        href="https://github.com/fatintawsifhoque"
        target="_blank"
        rel="noopener noreferrer"
        class="inline-block text-2xl font-bold text-indigo-800 hover:text-indigo-600 transition transform hover:scale-105"
      >
        Fatin Tawsif Hoque
      </a>
    </footer>
  </section>
</template>

<script setup>
import { ref, onMounted } from 'vue'

let id = 1
const title = ref('')
const content = ref('')
const notes = ref([])

onMounted(() => {
  const savedNotes = localStorage.getItem('notes')
  if (savedNotes) {
    notes.value = JSON.parse(savedNotes)

    const maxId = notes.value.reduce((max, note) => Math.max(max, note.id), 0)
    id = maxId + 1
  }
})

const saveToLocalStorage = () => {
  localStorage.setItem('notes', JSON.stringify(notes.value))
}

const formatTime = (timestamp) => {
  const date = new Date(timestamp)
  return date.toLocaleString('en-US', {
    month: 'short',
    day: 'numeric',
    hour: '2-digit',
    minute: '2-digit'
  })
}

const add = () => {
  if (!title.value.trim() && !content.value.trim()) return
  notes.value.push({
    id: id,
    title: title.value.trim(),
    content: content.value.trim(),
    timestamp: Date.now()
  })
  id++
  title.value = ''
  content.value = ''
  saveToLocalStorage()
}

const del = (noteId) => {
  notes.value = notes.value.filter(note => note.id !== noteId)
  saveToLocalStorage()
}
</script>

<style scoped>
.add {
  background-image: linear-gradient(to right, #6441A5 0%, #2a0845 51%, #6441A5 100%);
  color: white;
  border-radius: 12px;
  padding: 12px 36px;
  font-weight: 600;
  text-transform: uppercase;
  letter-spacing: 0.5px;
  cursor: pointer;
  transition: all 0.4s ease;
  background-size: 200% auto;
}

.add:hover {
  background-position: right center;
  transform: translateY(-2px);
  box-shadow: 0 4px 12px rgba(100, 65, 165, 0.3);
}

.note-card {
  opacity: 0;
  animation: fadeInUp 0.5s forwards;
}

@keyframes fadeInUp {
  from {
    opacity: 0;
    transform: translateY(20px);
  }
  to {
    opacity: 1;
    transform: translateY(0);
  }
}
</style>