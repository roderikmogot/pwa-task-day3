<template>
  <div>
    <h1>{{ title }}</h1>
    <ul>
      <li v-for="item in items" :key="item.id">{{ item.username }}</li>
    </ul>
    <button @click="sendData">Send Data</button>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';

// Define reactive variables
const title = ref('Fetching Data Example');
const items = ref([]);

// Define a function to send a POST request with application/x-www-form-urlencoded content type
const sendData = async () => {
  try {
    const postData = new URLSearchParams();
    postData.append('name', 'Nathaniel 1');
    postData.append('avatar', 'https://cloudflare-ipfs.com/ipfs/Qmd3W5DuhgHirLHGVixi6V76LhCkZUz6pnFt5AJBiyvHye/avatar/301.jpg');

    const response = await fetch('https://62f27b1e18493ca21f34bb7d.mockapi.io/article', {
      method: 'POST',
      headers: {
        'Content-Type': 'application/x-www-form-urlencoded',
      },
      body: postData,
    });

    if (response.ok) {
      console.log('Data sent successfully');
    } else {
      console.error('Failed to send data');
    }
  } catch (error) {
    console.error('An error occurred:', error);
  }
};

// Call fetchData when the component is mounted
onMounted(sendData);
</script>