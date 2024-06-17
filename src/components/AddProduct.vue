<script setup lang="ts">
import { ref } from 'vue';
import { useRoute, useRouter } from 'vue-router';
import { Auth } from '@/auth';

const title = ref('');
const price = ref(0);
const route = useRoute();
const router = useRouter();
const storeId = route.params.id;
const auth = new Auth(true);
const errors = ref<string[]>([]);

const addProduct = async () => {
  try {
    const token = auth.getToken();
    const response = await fetch(`http://localhost:3000/stores/${storeId}/products`, {
      method: 'POST',
      headers: {
        'Accept': 'application/json',
        'Content-Type': 'application/json',
        'X-API-KEY': import.meta.env.VITE_X_API_KEY,
        'Authorization': `Bearer ${token}`
      },
      body: JSON.stringify({ product: { title: title.value, price: price.value } })
    });

    if (response.ok) {
      router.push({ name: 'product-list', params: { id: storeId } });
    } else {
      console.error('Failed to add product');
    }
  } catch (error) {
    console.error('Error adding product:', error);
  }
};
</script>

<template>
  <div>
    <h1>Add New Product</h1>
    <form @submit.prevent="addProduct">
      <div>
        <label for="title">Product Name:</label>
        <input type="text" v-model="title" required />
      </div>
      <div>
        <label for="price">Price:</label>
        <input type="number" v-model="price" required />
      </div>
      <button type="submit">Add Product</button>
    </form>
    <div v-if="errors.length">
      <ul>
        <li v-for="error in errors" :key="error">{{ error }}</li>
      </ul>
    </div>
  </div>
</template>

<style scoped>
form {
  display: flex;
  flex-direction: column;
  width: 300px;
}
div {
  margin-bottom: 1rem;
}
button {
  align-self: flex-start;
}
</style>