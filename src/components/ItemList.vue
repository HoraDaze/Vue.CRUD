<template>
  <div>
    <h2>Items</h2>
    <form @submit.prevent="addItem">
      <input v-model="newItem.name" placeholder="Name" required>
      <input v-model="newItem.description" placeholder="Description" required>
      <button type="submit">Add Item</button>
    </form>
    <ul>
      <li v-for="item in items" :key="item.id">
        {{ item.name }} - {{ item.description }}
        <button @click="editItem(item)">Edit</button>
        <button @click="deleteItem(item.id)">Delete</button>
      </li>
    </ul>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      items: [],
      newItem: { name: '', description: '' },
    };
  },
  mounted() {
    this.fetchItems();
  },
  methods: {
    async fetchItems() {
  try {
    const response = await axios.get('http://localhost:3000/items');
    this.items = response.data;
  } catch (error) {
    console.error('Error fetching items:', error);
    if (error.response) {
      console.error('Response data:', error.response.data);
      console.error('Response status:', error.response.status);
    }
  }
},
    async addItem() {
      await axios.post('http://localhost:3000/items', this.newItem);
      this.newItem = { name: '', description: '' };
      this.fetchItems();
    },
    async editItem(item) {
      const updatedItem = { ...item };
      const name = prompt('Enter new name', item.name);
      const description = prompt('Enter new description', item.description);
      if (name && description) {
        updatedItem.name = name;
        updatedItem.description = description;
        await axios.put(`http://localhost:3000/items/${item.id}`, updatedItem);
        this.fetchItems();
      }
    },
    async deleteItem(id) {
      if (confirm('Are you sure you want to delete this item?')) {
        await axios.delete(`http://localhost:3000/items/${id}`);
        this.fetchItems();
      }
    },
  },
};
</script>