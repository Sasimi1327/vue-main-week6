<template>
  <div>
    <nav class="p-3">
      <span class="fs-3">前台頁面</span>
      <router-link class="fs-3 ms-2 text-decoration-none" to="/products">商品列表</router-link>
      <router-link class="fs-3 ms-2 text-decoration-none" to="/cart">購物車</router-link>
      <router-link class="fs-3 ms-2 text-decoration-none" to="/admin/products">去後台</router-link>
    </nav>
    <hr>
    <RouterView></RouterView>
  </div>
</template>

<script>

import { RouterView } from 'vue-router'

export default {
  components: {
    RouterView
  }
}
</script>
