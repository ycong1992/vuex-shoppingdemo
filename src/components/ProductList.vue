<template>
  <ul>
    <li
      v-for="product in products"
      :key="product.id">
      <p>型号：{{ product.title }}&nbsp;&nbsp;&nbsp;售价：{{ product.price }}</p>
      库存&nbsp;{{ product.inventory }}&nbsp;
      <input v-model="numbers[product.id]" type="number" style="width:40px;" min="0" :max="product.inventory" @input="handleNumberChange(product)"/>&nbsp;&nbsp;&nbsp;
      <button
        :disabled="!product.inventory"
        @click="addProductToCart(product)">
        加入购物车
      </button>
    </li>
  </ul>
</template>

<script>
import { mapState } from 'vuex'

export default {
  data () {
    return {
      numbers: {}
    }
  },
  watch: {
    products: {
      handler (results) {
        results.forEach(product => {
          if (this.numbers[product.id] === undefined) {
            this.$set(this.numbers, product.id, product.inventory > 0 ? 1 : 0);
          }
        })
      },
      immediate: true
    }
  },
  computed: mapState({
    products: state => state.products.all,
  }),
  // computed: {
  //   products(){
  //     return this.$store.state.products.all
  //   }
  // },
  // methods: mapActions('cart', [
  //   'addProductToCart'
  // ]),
  methods: {
    addProductToCart(product){
      if (this.numbers[product.id] == 0 ) {
        alert("商品数量为空");
        return;
      } else if (this.numbers[product.id] > product.inventory) {
        alert("商品数量超过库存");
        return; 
      }
      this.$store.dispatch('cart/addProductToCart', {product, number: this.numbers[product.id]});
      this.numbers[product.id] = product.inventory > 0 ? 1 : 0;
    },
    handleNumberChange(product) {
      // Mark：不确定这里这样处理会不会影响效率
      if (this.numbers[product.id] > product.inventory) {
        this.numbers[product.id] = product.inventory;
      } else if (!this.numbers[product.id]) {
        this.numbers[product.id] = 0;
      } else {
        this.numbers[product.id] = parseInt(this.numbers[product.id]);
      }
    }
  },
  created () {
    this.$store.dispatch('products/getAllProducts')
  }
}
</script>
