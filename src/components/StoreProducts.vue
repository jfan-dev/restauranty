<script setup lang="ts">
import { ref, onMounted } from 'vue'
import { useRoute } from 'vue-router'
import { Auth } from '@/auth'

interface Product {
  id: number
  title: string
  price: string
  enabled: boolean
}

interface Pagination {
  current: number
  per_page: number
  pages: number
  count: number
  previous: number | null
  next: number | null
}

const route = useRoute()
const storeId = route.params.id
const storeName = ref<string>('')
const products = ref<Product[]>([])
const disabledProducts = ref<Product[]>([])
const pagination = ref<Pagination | null>(null)
const disabledPagination = ref<Pagination | null>(null)
const auth = new Auth(true)

const fetchProducts = async (page: number | null = 1) => {
  if (!page) return
  try {
    const token = auth.getToken()
    const response = await fetch(`http://localhost:3000/stores/${storeId}/products?page=${page}`, {
      headers: {
        'Accept': 'application/json',
        'X-API-KEY': import.meta.env.VITE_X_API_KEY,
        'Authorization': `Bearer ${token}`
      }
    })
    if (response.ok) {
      const data = await response.json()
      products.value = data.result.products
      pagination.value = data.result.pagination || null
      storeName.value = data.result.store.name
      if (data.result.disabled_products) {
        disabledProducts.value = data.result.disabled_products
        disabledPagination.value = data.result.disabled_pagination || null
      }
    } else {
      console.error('Failed to fetch products')
    }
  } catch (error) {
    console.error('Error fetching products:', error)
  }
}

const fetchDisabledProducts = async (page: number | null = 1) => {
  if (!page) return
  try {
    const token = auth.getToken()
    const response = await fetch(`http://localhost:3000/stores/${storeId}/products?disabled_page=${page}`, {
      headers: {
        'Accept': 'application/json',
        'X-API-KEY': import.meta.env.VITE_X_API_KEY,
        'Authorization': `Bearer ${token}`
      }
    })
    if (response.ok) {
      const data = await response.json()
      disabledProducts.value = data.result.disabled_products
      disabledPagination.value = data.result.disabled_pagination || null
    } else {
      console.error('Failed to fetch disabled products')
    }
  } catch (error) {
    console.error('Error fetching disabled products:', error)
  }
}

const confirmDeleteProduct = (productId: number) => {
  if (confirm("Are you sure you want to delete this product?")) {
    deleteProduct(productId)
  }
}

const deleteProduct = async (productId: number) => {
  try {
    const token = auth.getToken()
    const response = await fetch(`http://localhost:3000/stores/${storeId}/products/${productId}`, {
      method: 'DELETE',
      headers: {
        'Accept': 'application/json',
        'X-API-KEY': import.meta.env.VITE_X_API_KEY,
        'Authorization': `Bearer ${token}`
      }
    })
    if (response.ok) {
      fetchProducts(pagination.value?.current)
      fetchDisabledProducts(disabledPagination.value?.current)
    } else {
      console.error('Failed to delete product')
    }
  } catch (error) {
    console.error('Error deleting product:', error)
  }
}

const changeProductState = async (product: Product) => {
  try {
    const token = auth.getToken()
    const response = await fetch(`http://localhost:3000/stores/${storeId}/products/${product.id}`, {
      method: 'PATCH',
      headers: {
        'Accept': 'application/json',
        'Content-Type': 'application/json',
        'X-API-KEY': import.meta.env.VITE_X_API_KEY,
        'Authorization': `Bearer ${token}`
      },
      body: JSON.stringify({ product: { enabled: product.enabled } })
    })
    if (response.ok) {
      fetchProducts(pagination.value?.current)
      fetchDisabledProducts(disabledPagination.value?.current)
    } else {
      console.error('Failed to change product state')
      fetchProducts(pagination.value?.current)
      fetchDisabledProducts(disabledPagination.value?.current)
    }
  } catch (error) {
    console.error('Error changing product state:', error)
    fetchProducts(pagination.value?.current)
    fetchDisabledProducts(disabledPagination.value?.current)
  }
}

onMounted(() => {
  fetchProducts()
})
</script>

<template>
  <div>
    <h1>Products for Store {{ storeName }}</h1>
    <h2>Enabled Products</h2>
    <ul>
      <li v-for="product in products" :key="product.id">
        {{ product.title }} - {{ product.price }}
        <select v-model="product.enabled" @change="changeProductState(product)">
          <option :value="true">Enabled</option>
          <option :value="false">Disabled</option>
        </select>
        <button @click="confirmDeleteProduct(product.id)">Delete</button>
      </li>
    </ul>
    <div v-if="pagination">
      <button @click="fetchProducts(pagination.previous)" :disabled="!pagination.previous">Previous</button>
      <button @click="fetchProducts(pagination.next)" :disabled="!pagination.next">Next</button>
    </div>
    <h2>Disabled Products</h2>
    <ul>
      <li v-for="product in disabledProducts" :key="product.id">
        {{ product.title }} - {{ product.price }}
        <select v-model="product.enabled" @change="changeProductState(product)">
          <option :value="true">Enabled</option>
          <option :value="false">Disabled</option>
        </select>
        <button @click="confirmDeleteProduct(product.id)">Delete</button>
      </li>
    </ul>
    <div v-if="disabledPagination">
      <button @click="fetchDisabledProducts(disabledPagination.previous)" :disabled="!disabledPagination.previous">Previous</button>
      <button @click="fetchDisabledProducts(disabledPagination.next)" :disabled="!disabledPagination.next">Next</button>
    </div>
  </div>
</template>

<style scoped>
ul {
  list-style-type: none;
  padding: 0;
}
li {
  margin: 0.5rem 0;
  display: flex;
  justify-content: space-between;
}
button {
  margin: 5px;
}
select {
  margin-right: 10px;
}
</style>
