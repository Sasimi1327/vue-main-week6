<template>
  <div>
    <nav class="p-3">
      <span class="fs-3">後台管理</span>
      <router-link class="fs-3 ms-2 text-decoration-none" to="/admin/products">商品管理列表</router-link>
      <router-link class="fs-3 ms-2 text-decoration-none" to="/admin/orders">訂單管理列表</router-link>
      <router-link class="fs-3 ms-2 text-decoration-none" to="/">回前台</router-link>
      <a href="#" class="fs-3 ms-2 text-decoration-none" @click.prevent="logout">登出</a>
    </nav>
    <hr>
    <RouterView></RouterView>
  </div>
</template>

<script>

import Swal from 'sweetalert2/dist/sweetalert2.js'
import 'sweetalert2/src/sweetalert2.scss'
import { RouterView } from 'vue-router'

const { VITE_APP_URL } = import.meta.env

export default {
  components: {
    RouterView
  },
  methods: {
    logout () {
      // 將 cookies 給清空並導回登入頁
      document.cookie = `hexToken=; expires=${new Date()};`
      this.$router.push('/login')
    },
    getTokenFromCookie () {
      const token = document.cookie.replace(
        /(?:(?:^|.*;\s*)hexToken\s*=\s*([^;]*).*$)|^.*$/,
        '$1'
      )
      this.$http.defaults.headers.common.Authorization = token
    },
    chekcAuth () {
      const url = `${VITE_APP_URL}/api/user/check`
      // 只有在"重整頁面"或者是"網址輸入"後台頁面時(含前台導頁至後台)，會啟動偵測驗證，
      // 其餘後台頁面間的轉換，在前端路由下，只會切換<RouterView>的部分而已
      this.$http
        .post(url)
        .then((res) => {

        })
        .catch((err) => {
          // 失敗，回去登入頁
          Swal.fire({
            icon: 'error',
            title: `錯誤 ${err.response.status}`,
            text: err.response.data.message,
            confirmButtonText: 'Got It!'
          }).then((result) => {
            // 驗證失敗，退回燈入頁
            this.$router.push('/login')
          })
        })
    }
  },
  mounted () {
    // 取 token，並放置到 axios 的 headers 中
    this.getTokenFromCookie()
    // 確認是否登入(檢查 cookie)
    this.chekcAuth()
  }
}
</script>
