<template>
  <h1>這是前台商品明細</h1>
  <h2>{{ product.title }}</h2>
  <img :src="product.imageUrl" class="img-fluid" alt="">
</template>

<script>

import Swal from 'sweetalert2/dist/sweetalert2.js'
import 'sweetalert2/src/sweetalert2.scss'
const { VITE_APP_URL, VITE_APP_PATH } = import.meta.env

export default {
  data () {
    return {
      product: {}
    }
  },
  methods: {
    getProduct () {
      const { id } = this.$route.params
      const url = `${VITE_APP_URL}/api/${VITE_APP_PATH}/product/${id}`

      this.$http.get(url)
        .then(res => {
          this.product = res.data.product
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
    this.getProduct()
  }
}
</script>
