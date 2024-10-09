<script setup>
import { ref, onMounted } from 'vue';
const showModal = ref(false);
const newNote = ref('');
const errorMessage = ref('');
const notes = ref([]);
function getRandomColor() {
  return 'hsl(' + Math.random() * 360 + ', 100%, 75%)';
}
const addNote = () => {
  if (newNote.value.length < 2) {
    return (errorMessage.value = 'Note needs more than 2 characters');
  }
  notes.value.push({
    id: Math.floor(Math.random() * 1000000),
    text: newNote.value,
    date: new Date(),
    backgroundColor: getRandomColor(),
  });
  showModal.value = false;
  newNote.value = '';
  errorMessage.value = '';
  localStorage.setItem('notes', JSON.stringify(notes.value));
};
onMounted(() => {
  const savedNotes = localStorage.getItem('notes');
  if (savedNotes) {
    notes.value = JSON.parse(savedNotes).map(note => ({
      ...note,
      date: new Date(note.date),
    }));
  }
});
const removeNote = (id) => {
  notes.value = notes.value.filter(note => note.id !== id);
  localStorage.setItem('notes', JSON.stringify(notes.value));
};
</script>

<template>
  <main>
    <div v-if="showModal" class="overlay">
      <div class="modal">
        <textarea v-model.trim="newNote" name="note" id="note" cols="30" rows="10"></textarea>
        <p v-if="errorMessage">{{ errorMessage }}</p>
        <button @click="addNote">Add Note</button>
        <button @click="showModal = false" class="close">Close</button>
      </div>
    </div>
    <div class="container">
      <header>
        <h1>Notes</h1>
        <button @click="showModal = true">+</button>
      </header>
      <div class="card-container">
        <div v-for="note in notes" :key="note.id" class="card" :style="{ backgroundColor: note.backgroundColor }">
          <p class="main-text">{{ note.text }}</p>
          <p class="date">{{ note.date.toLocaleDateString('en-US') }}</p>
          <button @click="removeNote(note.id)" class="remove-button">Remove</button>
        </div>
      </div>
    </div>
  </main>
</template>

<style scoped>
main {
  height: 100vh;
  width: 100vw;
  position: relative;
  margin: 0;
  padding: 0;
}
.container {
  max-width: 800px;
  padding: 10px;
  margin: 0 auto;
}
header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 20px;
}
h1 {
  font-weight: bold;
  margin-bottom: 25px;
  font-size: 60px;
}
header button {
  border: none;
  width: 50px;
  height: 50px;
  cursor: pointer;
  background-color: rgb(21, 20, 20);
  border-radius: 50%;
  color: white;
  font-size: 24px;
  display: flex;
  justify-content: center;
  align-items: center;
}
.card-container {
  display: flex;
  flex-wrap: wrap;
}
.card {
  width: 225px;
  height: 225px;
  background-color: rgb(237, 182, 44);
  padding: 10px;
  border-radius: 15px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  margin-right: 20px;
  margin-bottom: 20px;
}
.date {
  font-size: 12.5px;
  font-weight: bold;
}
.overlay {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background-color: rgba(0, 0, 0, 0.77);
  z-index: 10;
  display: flex;
  align-items: center;
  justify-content: center;
}
.modal {
  width: 400px;
  background-color: white;
  border-radius: 10px;
  padding: 30px;
  position: relative;
  display: flex;
  flex-direction: column;
}
.modal button {
  padding: 10px 20px;
  font-size: 20px;
  width: 100%;
  background-color: blueviolet;
  border: none;
  color: white;
  cursor: pointer;
  margin-top: 15px;
}
.modal .close {
  background-color: red;
  margin-top: 7px;
}
.modal p {
  color: red;
}
.remove-button {
  background-color: #ff4d4d;
  color: white;
  border: none;
  border-radius: 12px;
  padding: 8px 12px;
  cursor: pointer;
  margin-top: 10px;
  font-size: 14px;
  transition: background-color 0.3s, transform 0.2s;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
}
.remove-button:hover {
  background-color: #ff1a1a;
  transform: translateY(-2px);
}
</style>
