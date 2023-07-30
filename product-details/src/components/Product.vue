<script setup></script>

<template>
  <div class="container">
    <div class="header">
      <div class="cart">My Cart ({{ cart.length }})</div>
    </div>

    <div class="flex">
      <div class="flex-one">
        <img
          v-if="product && product.imageURL"
          :src="product.imageURL"
          alt=""
        />
      </div>

      <div class="flex-one">
        <p v-if="product && product.title && typeof product.title === 'string'">
          {{ product.title }}
        </p>
        <p v-else>Loading</p>

        <p v-if="product && product.price && typeof product.price === 'number'">
          ${{ product.price.toFixed(2) }}
        </p>
        <p v-else>Loading</p>

        <p
          v-if="
            product &&
            product.description &&
            typeof product.description === 'string'
          "
        >
          {{ product.description }}
        </p>
        <p v-else>Loading</p>

        <span class="star">SIZE</span>
        <span v-if="selectedSize">{{
          this.product.sizeOptions[this.selectedSize - 1].label
        }}</span>

        <div
          v-if="
            product &&
            product.sizeOptions &&
            typeof (product.sizeOptions === 'object')
          "
          class="flex"
        >
          <div
            class="size-box-parent"
            v-for="size in product.sizeOptions"
            :key="size.id"
          >
            <div
              @click="handleSizeClick(size.id)"
              :class="selectedSize === size.id ? 'selected' : ''"
              class="size-box unselectable"
            >
              {{ size.label }}
            </div>
          </div>
        </div>
        <p v-else>Loading</p>

        <div @click="addToCart" class="add-to-cart unselectable" v-if="product">
          ADD TO CART
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "Product",

  data() {
    return {
      product: null,
      selectedSize: null,
      cart: [],
    };
  },
  methods: {
    addToCart() {
      if (!this.selectedSize) {
        alert("Please select a size");
        return;
      }
      this.cart.push(this.product.sizeOptions[this.selectedSize - 1]);
      console.log(this.cart);
      console.log("Add to cart");
    },
    handleSizeClick(sizeId) {
      if (this.selectedSize && this.selectedSize === sizeId) {
        this.selectedSize = null;
        return;
      }
      this.selectedSize = sizeId;
    },
  },
  async created() {
    await fetch(
      "https://3sb655pz3a.execute-api.ap-southeast-2.amazonaws.com/live/product"
    )
      .then((response) => response.json())
      .then((data) => (this.product = data));
  },
};
</script>
<style scoped>
.container {
  margin: 0 auto;
  max-width: 1440px;
}
.header {
  background-color: var(--header-background);
  height: 30px;
  width: 100%;
  display: flex;
  align-items: center;
}
.cart {
  margin-left: auto;
  margin-right: 14%;
  width: fit-content;
  opacity: 0.7;
  font-size: 13px;
  transition: opacity 0.2s ease-in;
}
.cart:hover {
  cursor: pointer;
  opacity: 1;
}
.star::after {
  content: "*";
  color: var(--required-star);
  margin-right: 6px;
}
.size-box-parent:nth-child(2n) {
  margin: 0px 4px;
}
.size-box {
  height: 40px;
  width: 40px;
  display: flex;
  justify-content: center;
  align-items: center;
  cursor: pointer;
  border: 1px solid var(--border-light-grey);
}

.add-to-cart {
  width: fit-content;
  padding: 8px 14px;
  border: 2px var(--border-dark-grey) solid;
  transition: color, background-color 0.2s ease-in-out;
}
.add-to-cart:hover {
  cursor: pointer;
  background-color: var(--border-dark-grey);
  color: rgb(245, 245, 245);
}

p {
  color: var(--font-color-dark);
}
.light-grey-border {
  border: 1px solid var(--border-light-grey);
}

.dark-grey-border {
  border: 2px solid var(--border-dark-grey);
}
.selected {
  background-color: var(--border-dark-grey);
  color: rgb(245, 245, 245);
}
</style>
