<template>
  <h1 class="text-center">這是前台商品列表</h1>
  <table class="table">
    <thead>
      <tr>
        <th class="text-center">商品名稱</th>
        <th class="text-center">商品圖示</th>
        <th class="text-center">連結</th>
      </tr>
    </thead>
    <tbody>
      <tr v-for="prodcut in products" :key="prodcut.id" class="align-middle">
        <td>{{ prodcut.title }}</td>
        <td><img :src="prodcut.imageUrl" class="object-fit-contain border rounded" width="200" height="200" alt=""></td>
        <td>
          <RouterLink class="btn btn-outline-primary" :to="`/product/${prodcut.id}`">商品明細</RouterLink>
          <button type="button"
          @click="addToCart(prodcut)"
          class="btn btn-outline-danger">加入購物車</button>
        </td>
      </tr>
    </tbody>
  </table>
</template>

<script>
import Swal from 'sweetalert2/dist/sweetalert2.js'
import 'sweetalert2/src/sweetalert2.scss'

import { RouterLink } from 'vue-router'
const { VITE_APP_URL, VITE_APP_PATH } = import.meta.env

export default {
  data () {
    return {
      products: []
    }
  },
  components: {
    RouterLink
  },
  methods: {
    getProducts () {
      const url = `${VITE_APP_URL}/api/${VITE_APP_PATH}/products/all`
      this.$http.get(url)
        .then(res => {
          this.products = res.data.products
        })
        .catch(err => {
          Swal.fire({
            icon: 'error',
            title: `錯誤 ${err.response.status}`,
            text: err.response.data.message,
            confirmButtonText: 'OK'
          })
        })
    },
    addToCart (product) {
      const url = `${VITE_APP_URL}/api/${VITE_APP_PATH}/cart`
      const data = {
        product_id: product.id,
        qty: 1
      }
      this.$http.post(url, { data })
        .then(res => {
          Swal.fire({
            icon: 'success',
            title: res.data.message,
            text: `${res.data.data.product.title} 加入購物車成功`,
            confirmButtonText: 'OK'
          })
        })
        .catch(err => {
          Swal.fire({
            icon: 'error',
            title: `錯誤 ${err.response.status}`,
            text: err.response.data.message,
            confirmButtonText: 'OK'
          })
        })
    }
  },
  mounted () {
    this.getProducts()
  }
}
</script>
