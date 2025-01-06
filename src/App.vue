<template>
  <div class="min-h-screen bg-gray-100 dark:bg-gray-900 py-8">
    <div class="max-w-4xl mx-auto px-4">
      <h1 class="text-4xl font-bold text-gray-900 dark:text-white mb-8 text-center">Address Book</h1>
      
      <!-- Add Contact Form -->
      <div class="bg-white dark:bg-gray-800 rounded-lg shadow-md p-6 mb-6">
        <h2 class="text-2xl font-semibold text-gray-900 dark:text-white mb-4">Add New Contact</h2>
        <form @submit.prevent="addContact" class="space-y-4">
          <input 
            v-model="newContact.name" 
            placeholder="Name" 
            required
            class="w-full px-4 py-2 rounded-md border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-700 text-gray-900 dark:text-white focus:ring-2 focus:ring-blue-500 focus:border-transparent"
          >
          <input 
            v-model="newContact.email" 
            type="email" 
            placeholder="Email"
            class="w-full px-4 py-2 rounded-md border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-700 text-gray-900 dark:text-white focus:ring-2 focus:ring-blue-500 focus:border-transparent"
          >
          <input 
            v-model="newContact.phone" 
            placeholder="Phone"
            class="w-full px-4 py-2 rounded-md border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-700 text-gray-900 dark:text-white focus:ring-2 focus:ring-blue-500 focus:border-transparent"
          >
          <textarea 
            v-model="newContact.address" 
            placeholder="Address"
            class="w-full px-4 py-2 rounded-md border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-700 text-gray-900 dark:text-white focus:ring-2 focus:ring-blue-500 focus:border-transparent"
          ></textarea>
          <button 
            type="submit"
            class="w-full bg-blue-500 hover:bg-blue-600 text-white font-semibold py-2 px-4 rounded-md transition-colors duration-200"
          >
            Add Contact
          </button>
        </form>
      </div>

      <!-- Search -->
      <input 
        v-model="searchQuery" 
        placeholder="Search contacts..."
        class="w-full px-4 py-2 rounded-md border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-700 text-gray-900 dark:text-white focus:ring-2 focus:ring-blue-500 focus:border-transparent mb-6"
      >

      <!-- Contacts List -->
      <div class="space-y-4">
        <div 
          v-for="(contact, index) in filteredContacts" 
          :key="index"
          class="bg-white dark:bg-gray-800 rounded-lg shadow-md p-6"
        >
          <div class="flex justify-between items-start">
            <div class="space-y-2">
              <h3 class="text-xl font-semibold text-gray-900 dark:text-white">{{ contact.name }}</h3>
              <p class="text-gray-600 dark:text-gray-300">Email: {{ contact.email }}</p>
              <p class="text-gray-600 dark:text-gray-300">Phone: {{ contact.phone }}</p>
              <p class="text-gray-600 dark:text-gray-300">Address: {{ contact.address }}</p>
            </div>
            <div class="space-x-2">
              <button 
                @click="editContact(index)"
                class="bg-yellow-500 hover:bg-yellow-600 text-white font-semibold py-1 px-3 rounded-md transition-colors duration-200"
              >
                Edit
              </button>
              <button 
                @click="deleteContact(index)"
                class="bg-red-500 hover:bg-red-600 text-white font-semibold py-1 px-3 rounded-md transition-colors duration-200"
              >
                Delete
              </button>
            </div>
          </div>
        </div>
      </div>

      <!-- Edit Modal -->
      <div 
        v-if="showEditModal" 
        class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center p-4"
      >
        <div class="bg-white dark:bg-gray-800 rounded-lg shadow-lg max-w-md w-full p-6">
          <h2 class="text-2xl font-semibold text-gray-900 dark:text-white mb-4">Edit Contact</h2>
          <form @submit.prevent="updateContact" class="space-y-4">
            <input 
              v-model="editingContact.name" 
              placeholder="Name" 
              required
              class="w-full px-4 py-2 rounded-md border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-700 text-gray-900 dark:text-white focus:ring-2 focus:ring-blue-500 focus:border-transparent"
            >
            <input 
              v-model="editingContact.email" 
              type="email" 
              placeholder="Email"
              class="w-full px-4 py-2 rounded-md border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-700 text-gray-900 dark:text-white focus:ring-2 focus:ring-blue-500 focus:border-transparent"
            >
            <input 
              v-model="editingContact.phone" 
              placeholder="Phone"
              class="w-full px-4 py-2 rounded-md border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-700 text-gray-900 dark:text-white focus:ring-2 focus:ring-blue-500 focus:border-transparent"
            >
            <textarea 
              v-model="editingContact.address" 
              placeholder="Address"
              class="w-full px-4 py-2 rounded-md border border-gray-300 dark:border-gray-600 bg-white dark:bg-gray-700 text-gray-900 dark:text-white focus:ring-2 focus:ring-blue-500 focus:border-transparent"
            ></textarea>
            <div class="flex justify-end space-x-2">
              <button 
                type="button" 
                @click="showEditModal = false"
                class="bg-gray-500 hover:bg-gray-600 text-white font-semibold py-2 px-4 rounded-md transition-colors duration-200"
              >
                Cancel
              </button>
              <button 
                type="submit"
                class="bg-blue-500 hover:bg-blue-600 text-white font-semibold py-2 px-4 rounded-md transition-colors duration-200"
              >
                Save
              </button>
            </div>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue'

const contacts = ref([])
const searchQuery = ref('')
const showEditModal = ref(false)
const editingIndex = ref(null)

const newContact = ref({
  name: '',
  email: '',
  phone: '',
  address: ''
})

const editingContact = ref({
  name: '',
  email: '',
  phone: '',
  address: ''
})

const filteredContacts = computed(() => {
  const query = searchQuery.value.toLowerCase()
  return contacts.value.filter(contact => 
    contact.name.toLowerCase().includes(query) ||
    contact.email.toLowerCase().includes(query) ||
    contact.phone.toLowerCase().includes(query) ||
    contact.address.toLowerCase().includes(query)
  )
})

const addContact = () => {
  contacts.value.push({...newContact.value})
  newContact.value = {
    name: '',
    email: '',
    phone: '',
    address: ''
  }
  saveToLocalStorage()
}

const deleteContact = (index) => {
  contacts.value.splice(index, 1)
  saveToLocalStorage()
}

const editContact = (index) => {
  editingIndex.value = index
  editingContact.value = {...contacts.value[index]}
  showEditModal.value = true
}

const updateContact = () => {
  contacts.value[editingIndex.value] = {...editingContact.value}
  showEditModal.value = false
  saveToLocalStorage()
}

const saveToLocalStorage = () => {
  localStorage.setItem('addressBook', JSON.stringify(contacts.value))
}

const loadFromLocalStorage = () => {
  const saved = localStorage.getItem('addressBook')
  if (saved) {
    contacts.value = JSON.parse(saved)
  }
}

onMounted(() => {
  loadFromLocalStorage()
})
</script>