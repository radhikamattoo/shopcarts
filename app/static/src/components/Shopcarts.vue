<template>
  <div>
    <create-shopcart
      v-bind="$attrs"
      @created='loadCarts' />

    <list-shopcarts
      v-bind="$attrs"
      :carts='carts'
      :loading='loading'
      @filter='loadCarts'
      @pruned='loadCarts'
      v-on="$listeners" />
  </div>
</template>

<script>
import axios from 'axios';
import CreateShopcart from './CreateShopcart';
import ListShopcarts from './ListShopcarts';

export default {
  name: 'Shopcarts',

  components: { CreateShopcart, ListShopcarts },

  data() {
    return {
      carts: [],
      loading: false
    }
  },

  mounted() {
    this.loadCarts();
  },

  methods: {
    loadCarts(productId) {
      this.loading = true;
      const pid = productId || null;

      axios.get('/shopcarts', { params: { pid } })
        .then(response => {
          this.carts = response.data;
          this.loading = false;
        });
    }
  }
}
</script>