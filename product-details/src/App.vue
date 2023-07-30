<template>
  <Product />
</template>

<script>
import Product from "./components/Product.vue";

export default {
  name: "App",
  components: {
    Product,
  },
  data() {
    return {
      title: "Merlin's T-Shirts",
    };
  },
  mounted() {
    document.title = this.title;
  },
};
</script>
