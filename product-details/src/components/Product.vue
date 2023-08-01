<script setup></script>

<template>
  <div class="header relative border-box">
    <div
      @click="toggleCartDropdown"
      :class="showCartDropdown ? 'cart-selected' : ''"
      class="cart unselectable"
    >
      <span v-if="isWindowWidthGreaterThan470">My Cart</span
      ><span v-else
        ><img class="icon" src="/images/cart.png" alt="cart logo"
      /></span>
      &nbsp;( {{ cart.length }} )
    </div>

    <div v-if="showCartDropdown" class="cart-dropdown unselectable">
      <div
        class="cart-item"
        v-for="(item, index) in processedCart"
        :key="index"
      >
        <div class="flex">
          <div class="img-wrapper-small">
            <img v-if="product && product.imageURL" :src="product.imageURL" />
          </div>

          <div>
            <div class="dark-grey-font very-small-margin-bottom">
              {{ product.title }}
            </div>
            <div class="very-small-margin-bottom">
              <span class="item-quantity">{{ item.quantity }}x </span>
              <span class="bold">${{ product.price.toFixed(2) }}</span>
            </div>

            <div>Size: {{ item.label }}</div>
          </div>
        </div>
      </div>
    </div>
  </div>

  <div v-if="!product" style="opacity: 0.2">LOADING</div>
  <div class="container">
    <div class="flex justify-center product-parent">
      <div class="unselectable img-wrapper left-side">
        <img
          v-if="product && product.imageURL"
          :src="product.imageURL"
          alt=""
        />
      </div>

      <div class="right-side">
        <p
          class="dark-grey-font medium-size unselectable small-margin-bottom"
          v-if="product && product.title && typeof product.title === 'string'"
        >
          {{ product.title }}
        </p>

        <p
          class="dark-grey-font bold unselectable small-margin-bottom"
          v-if="product && product.price && typeof product.price === 'number'"
        >
          ${{ product.price.toFixed(2) }}
        </p>

        <p
          class="unselectable medium-margin-bottom"
          v-if="
            product &&
            product.description &&
            typeof product.description === 'string'
          "
        >
          {{ product.description }}
        </p>

        <span
          v-if="product"
          class="star light-grey-font unselectable small-margin-bottom"
          >SIZE</span
        >
        <span v-if="selectedSize" class="bold unselectable">{{
          this.product.sizeOptions[this.selectedSize - 1].label
        }}</span>

        <div
          v-if="
            product &&
            product.sizeOptions &&
            typeof (product.sizeOptions === 'object')
          "
          class="flex relative small-margin-bottom"
        >
          <div
            class="size-box-parent"
            v-for="size in product.sizeOptions"
            :key="size.id"
          >
            <p
              @click="handleSizeClick(size.id)"
              :class="selectedSize === size.id ? 'selected' : ''"
              class="size-box unselectable border-box"
            >
              {{ size.label }}
            </p>
          </div>
          <div v-if="showErrorMsg" class="error-message">
            <span class="message">Please Select A Size</span>
          </div>
        </div>

        <div
          @click="addToCart"
          class="add-to-cart unselectable semibold"
          v-if="product"
        >
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
      windowWidth: window.innerWidth,
      showCartDropdown: false,
      product: null,
      selectedSize: null,
      cart: [],
      showErrorMsg: false,
    };
  },
  computed: {
    processedCart() {
      let processed = [];
      for (const key in this.cart) {
        if (this.cart.hasOwnProperty(key)) {
          const item = this.cart[key];
          const existingIndex = processed.findIndex((el) => el.id === item.id);
          if (existingIndex === -1) {
            processed.push({ ...item, quantity: 1 });
          } else {
            processed[existingIndex].quantity++;
          }
        }
      }
      return processed.reverse();
    },
    isWindowWidthGreaterThan470() {
      return this.windowWidth > 470;
    },
  },
  methods: {
    toggleCartDropdown() {
      if (!this.cart.length && !this.showCartDropdown) return;
      this.showCartDropdown = !this.showCartDropdown;
    },
    addToCart() {
      if (!this.selectedSize) {
        this.showErrorMsg = true;
        return;
      } else if (this.showErrorMsg) this.showErrorMsg = false;

      this.cart.push(this.product.sizeOptions[this.selectedSize - 1]);
    },
    handleSizeClick(sizeId) {
      if (this.selectedSize && this.selectedSize === sizeId) {
        this.selectedSize = null;
        return;
      }
      this.selectedSize = sizeId;
    },
  },
  mounted() {
    window.addEventListener("resize", () => {
      this.windowWidth = window.innerWidth;
    });
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
  padding: 0 40px;
  margin: 0 auto;
  max-width: 1440px;
}
.header {
  background-color: var(--header-background);
  height: 34px;
  width: 100%;
  display: flex;
  align-items: center;
  margin-top: 14px;
  margin-bottom: 30px;
  margin-left: auto;
  margin-right: auto;
  max-width: 1440px;
}
.cart {
  display: flex;
  align-items: center;
  padding: 0 8px;
  height: 100%;
  margin-left: auto;
  margin-right: 12%;
  width: fit-content;
  font-size: 13px;
  transition: color 0.2s ease-in;
  color: var(--font-color-light);
  border: 1px solid var(--header-background);
  z-index: 3;
}
.cart:hover {
  cursor: pointer;
  color: var(--font-color-dark);
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
  height: 48px;
  width: 48px;
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
.error-message {
  position: absolute;
  top: 50%;
  transform: translateY(-50%);
  left: 164px;
  width: fit-content;
  height: fit-content;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 1;
}
.error-message span {
  color: rgb(255, 25, 25);
}

div,
span {
  color: var(--font-color-dark);
}
p {
  color: var(--font-color-light);
}
.light-grey-font {
  color: var(--font-color-light);
}
.dark-grey-font {
  color: var(--font-color-dark);
}
.light-grey-border {
  border: 1px solid var(--border-light-grey);
}

.dark-grey-border {
  border: 2px solid var(--border-dark-grey);
}
.selected {
  border: 2px solid var(--border-dark-grey);
  color: var(--font-color-dark);
}
.cart-selected {
  color: var(--font-color-dark) !important;
  border: 1px solid var(--border-light-grey);
  border-bottom: 1px solid white;
  background-color: white;
}
.cart-dropdown {
  position: absolute;
  top: 100%;
  /* right same as margin right of cart to keep aligned */
  right: 12%;
  width: 220px;
  background-color: white;
  border: 1px solid var(--border-light-grey);
  z-index: 999;
  display: flex;
  flex-direction: column;
  z-index: 2;
}

.cart-item {
  padding: 12px;
  display: flex;
  justify-content: space-between;
  align-items: center;
}
.img-wrapper {
  margin-right: 10px;
  max-height: 650px;
  max-width: 470px;
}
.img-wrapper img {
  height: 100%;
  width: 100%;
  object-fit: cover;
}
.img-wrapper-small {
  max-height: 100px;
  max-width: 60px;
  margin-right: 8px;
}
.img-wrapper-small img {
  height: 100%;
  width: 100%;
  object-fit: cover;
}
.right-side {
  margin-left: 18px;
}
.right-side p {
  max-width: 400px;
}
.left-side {
  margin-right: 18px;
}
.icon {
  height: 20px;
  width: 20px;
  margin-top: 4px;
}
@media (max-width: 470px) {
  .cart {
    margin-right: 5%;
  }
  .cart-dropdown {
    width: 90%;
    right: 5%;
  }
  .product-parent {
    flex-direction: column;
    justify-content: start;
  }
  .left-side {
    margin: 0 0 20px 0;
  }
  .right-side {
    margin: 0;
  }
  .container {
    padding: 0 16px;
  }
  .header {
    margin-bottom: 20px;
  }
  .add-to-cart {
    margin-bottom: 30px;
  }
}
</style>
