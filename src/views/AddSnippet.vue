<template>
  <div class="p-6 max-w-xl mx-auto">
    <h1 class="text-2xl font-bold mb-4">Add New Snippet</h1>

    <!-- Text Input -->
    <textarea
      v-model="text"
      placeholder="What did you learn today?"
      class="w-full h-32 p-3 border rounded mb-4 focus:outline-none focus:ring"
    />

    <!-- Tags Input -->
    <div class="mb-4">
      <label class="block mb-1 font-medium">Tags (press Enter to add)</label>
      <input
        v-model="tagInput"
        @keydown.enter.prevent="addTag"
        placeholder="e.g. Excel, Frontend"
        class="w-full p-2 border rounded focus:outline-none focus:ring"
      />
      <div class="mt-2 flex flex-wrap gap-2">
        <span
          v-for="(tag, index) in tags"
          :key="index"
          class="px-3 py-1 bg-blue-100 text-blue-800 text-sm rounded-full"
        >
          {{ tag }}
          <button @click="removeTag(index)" class="ml-2 text-red-500 font-bold">&times;</button>
        </span>
      </div>
    </div>

    <!-- Save Button -->
    <button
      @click="saveSnippet"
      :disabled="!text"
      class="bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700 disabled:opacity-50"
    >
      Save Snippet
    </button>

    <!-- Confirmation -->
    <p v-if="saved" class="text-green-600 mt-4">✅ Skill Sparked!</p>
  </div>
</template>

<script setup>
import { ref } from 'vue'
import { db } from '../firebase/firebase.js'
import { collection, addDoc, serverTimestamp } from 'firebase/firestore'

const text = ref('')
const tagInput = ref('')
const tags = ref([])
const saved = ref(false)

function addTag() {
  const newTag = tagInput.value.trim()
  if (newTag && !tags.value.includes(newTag)) {
    tags.value.push(newTag)
  }
  tagInput.value = ''
}

function removeTag(index) {
  tags.value.splice(index, 1)
}

async function saveSnippet() {
  try {
    await addDoc(collection(db, 'snippets'), {
      text: text.value,
      tags: tags.value,
      timestamp: serverTimestamp(),
    })
    text.value = ''
    tags.value = []
    saved.value = true
    setTimeout(() => (saved.value = false), 3000)
  } catch (err) {
    console.error('Error saving snippet:', err)
  }
}
</script>
