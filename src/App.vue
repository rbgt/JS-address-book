<template>
  <div class="container mx-auto p-4">
    <h1 class="text-2xl font-bold mb-4">Address Book</h1>
    
    <!-- Add Contact Form -->
    <div class="bg-white p-4 rounded shadow mb-4">
      <h2 class="text-xl mb-2">Add New Contact</h2>
      <form @submit.prevent="addContact" class="space-y-2">
        <input v-model="newContact.name" placeholder="Name" required
               class="w-full p-2 border rounded">
        <input v-model="newContact.email" type="email" placeholder="Email"
               class="w-full p-2 border rounded">
        <input v-model="newContact.phone" placeholder="Phone"
               class="w-full p-2 border rounded">
        <textarea v-model="newContact.address" placeholder="Address"
                  class="w-full p-2 border rounded"></textarea>
        <button type="submit" 
                class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600">
          Add Contact
        </button>
      </form>
    </div>

    <!-- Search -->
    <input v-model="searchQuery" placeholder="Search contacts..."
           class="w-full p-2 border rounded mb-4">

    <!-- Contacts List -->
    <div class="space-y-4">
      <div v-for="(contact, index) in filteredContacts" :key="index"
           class="bg-white p-4 rounded shadow">
        <div class="flex justify-between">
          <div>
            <h3 class="font-bold">{{ contact.name }}</h3>
            <p>Email: {{ contact.email }}</p>
            <p>Phone: {{ contact.phone }}</p>
            <p>Address: {{ contact.address }}</p>
          </div>
          <div class="space-x-2">
            <button @click="editContact(index)"
                    class="bg-yellow-500 text-white px-3 py-1 rounded hover:bg-yellow-600">
              Edit
            </button>
            <button @click="deleteContact(index)"
                    class="bg-red-500 text-white px-3 py-1 rounded hover:bg-red-600">
              Delete
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- Edit Modal -->
    <div v-if="showEditModal" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center">
      <div class="bg-white p-4 rounded max-w-md w-full">
        <h2 class="text-xl mb-2">Edit Contact</h2>
        <form @submit.prevent="updateContact" class="space-y-2">
          <input v-model="editingContact.name" placeholder="Name" required
                 class="w-full p-2 border rounded">
          <input v-model="editingContact.email" type="email" placeholder="Email"
                 class="w-full p-2 border rounded">
          <input v-model="editingContact.phone" placeholder="Phone"
                 class="w-full p-2 border rounded">
          <textarea v-model="editingContact.address" placeholder="Address"
                    class="w-full p-2 border rounded"></textarea>
          <div class="flex justify-end space-x-2">
            <button type="button" @click="showEditModal = false"
                    class="bg-gray-500 text-white px-4 py-2 rounded hover:bg-gray-600">
              Cancel
            </button>
            <button type="submit"
                    class="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600">
              Save
            </button>
          </div>
        </form>
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