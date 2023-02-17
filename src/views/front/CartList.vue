<template>
  <Loading :active="isLoading"
              :can-cancel="true"
              :is-full-page="fullPage"
              ></Loading>
  <h1 class="text-center">這是前台購物車列表</h1>
  <h3 class="text-center" v-if="!cart?.carts?.length">目前購物車沒有商品</h3>
  <table v-else class="table align-middle">
    <thead>
      <tr>
        <th></th>
        <th class="text-center">品名</th>
        <th class="text-center" style="width: 150px">數量/單位</th>
        <th class="text-center">單價</th>
        <th class="text-center">小計</th>
      </tr>
    </thead>
    <tbody>
      <template v-if="cart.carts">
        <tr v-for="item in cart.carts" :key="item.id">
          <td>
            <button type="button" class="btn btn-outline-danger btn-sm"
              :disabled="item.id === loadingItem"
              @click="deleteCartItem(item)">
              <i v-if="item.id === loadingItem" class="fas fa-spinner fa-pulse"></i>
              x
            </button>
          </td>
          <td>
            {{ item.product.title }}
          </td>
          <td>
            <div class="input-group input-group-sm">
              <!-- 數量不要給用戶手填 -->
              <select name="" id="" class="form-select" v-model="item.qty"
                :disabled="item.id === loadingItem"
                @change="updateCartItem(item)">
                <option :value="i" v-for="i in 20" :key="i + '123456'">{{ i }}</option>
              </select>
            </div>
          </td>
          <td class="text-end">
            {{ item.product.price }}
          </td>
          <td class="text-end">
            {{ item.total }}
          </td>
        </tr>
      </template>
    </tbody>
    <tfoot>
      <tr>
        <td colspan="4" class="text-end">總計</td>
        <td class="text-end">{{ cart.total }}</td>
      </tr>
      <tr>
        <td colspan="4" class="text-end text-success">折扣價</td>
        <td class="text-end text-success">{{ cart.final_total }}</td>
      </tr>
    </tfoot>
  </table>
</template>

<script>
import Swal from 'sweetalert2/dist/sweetalert2.js'
import 'sweetalert2/src/sweetalert2.scss'

import Loading from 'vue-loading-overlay'
import 'vue-loading-overlay/dist/css/index.css'

const { VITE_APP_URL, VITE_APP_PATH } = import.meta.env

export default {
  data () {
    return {
      isLoading: false,
      cart: {},
      selectProductId: '',
      loadingItem: '' // 存 id
    }
  },
  methods: {
    getCarts () {
      this.isLoading = true
      this.$http.get(`${VITE_APP_URL}/api/${VITE_APP_PATH}/cart`)
        .then(res => {
          this.cart = res.data.data
          this.isLoading = false
        })
        .catch(err => {
          Swal.fire({
            icon: 'error',
            title: `錯誤 ${err.status}`,
            text: err.data.message,
            confirmButtonText: 'OK'
          })
        })
    },
    // product_id: 產品的 ID
    // id: 購物車的 ID
    updateCartItem (item) {
      const data = {
        product_id: item.product_id,
        qty: item.qty
      }
      this.loadingItem = item.id
      this.$http.put(`${VITE_APP_URL}/api/${VITE_APP_PATH}/cart/${item.id}`, { data })
        .then(res => {
          this.getCarts()
          this.loadingItem = ''
          Swal.fire({
            icon: 'success',
            title: `${res.status === 200 ? '成功' : '失敗'}`,
            text: res.data.message,
            confirmButtonText: 'OK'
          })
          // alert(res.data.message);
        })
        .catch(err => {
          Swal.fire({
            icon: 'error',
            title: `錯誤 ${err.status}`,
            text: err.data.message,
            confirmButtonText: 'OK'
          })
        })
    },
    deleteCartItem (item) {
      this.loadingItem = item.id
      this.$http.delete(`${VITE_APP_URL}/api/${VITE_APP_PATH}/cart/${item.id}`)
        .then(res => {
          this.getCarts()
          this.loadingItem = ''
          Swal.fire({
            icon: 'success',
            title: `${res.status === 200 ? '成功' : '失敗'}`,
            text: res.data.message,
            confirmButtonText: 'OK'
          })
          // alert(res.data.message);
        })
        .catch(err => {
          Swal.fire({
            icon: 'error',
            title: `錯誤 ${err.status}`,
            text: err.data.message,
            confirmButtonText: 'OK'
          })
        })
    }
  },
  components: {
    Loading
  },
  mounted () {
    this.getCarts()
  }
}
</script>
