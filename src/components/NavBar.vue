<template>
<header>
	<nav class="navbar if-fixed has-shadow is-gray" >
	  <div class="navbar-brand">
	    <router-link to="/" class="nav-item">
	      <img srcset="/static/inverse-slim.png ,
                   /static/inverse-slim@2x.png 2x,
                   /static/inverse-slim@3x.png 3x"
            src="/static/inverse-slim@3x.png" alt="Secure Aid Logo">
	    </router-link>
            <button class="button" v-show='!categShown' @click='changeCateg'>
              {{ this.$store.state.selectedCategory}} <span class="fa fa-close">&nbsp; &nbsp; &times;</span>
            </button>
	  </div>
	  <div class="navbar-menu is-gray " id='mainNav' >
			<div class="navbar-end">
	    <div class="nav-item" >
	      <div class="field is-grouped">
	        <p class="control">
	          <router-link to='/cart' class="button is-gray " >
	            <span class="icon">
              <object data="/static/shopping-cart.svg" type="image/svg+xml"></object>
	            </span>
	            <span> {{total | currency}}</span>
	          </router-link>
	        </p>
	      </div>
	      </div>
	    </div>
	  </div>
	</nav>

	</header>
</template>

<style lang="scss" scoped>
header {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 100;
}
nav {
  padding-left: 1rem;
  padding-right: 1rem;
  padding-top: 0.5rem;
}
.is-gray {
  background-color: rgb(53, 77, 91);
  color: whitesmoke;
}

.navbar {
  height: auto;
  // margin-bottom: 2rem;
  display: flex;
  flex-flow: row wrap;
  justify-content: space-between;
  align-items: center;

  .navbar-menu {
    width: auto;
    box-shadow: none;
    display: block;
    padding-top: 0;
  }
}
.button.is-gray {
  &:active,
  &:focus {
    border-color: whitesmoke;
    background: lighten($color: rgb(53, 77, 91), $amount: 10%);
    outline: none;
  }
}
.nav-item {
  align-self: center;
  height: auto;
  .field.is-grouped {
    justify-content: center;
  }
}

.nav-item img {
  max-height: 2rem;
}

.icon svg:hover path,
.icon svg:focus path {
  fill: #220b0b;
}
</style>

<script>
import { mapGetters, mapActions } from "vuex";
import store from "../store/index.js";

export default {
  data() {
    return {
    };
  },
  methods: {
    changeCateg() {
      this.$store.dispatch('showCateg')
    },
  },
  computed: {
    ...mapGetters({
      products: "cartProducts",
      categShown: 'categShown'
    }),

    itemsInCart() {
      let cart = this.$store.getters.cartProducts;
      return cart.reduce((accum, item) => accum + item.quantity, 0);
    },
    total() {
      return this.products.reduce((total, p) => {
        return total + p.price * p.quantity;
      }, 0);
    }
  }
};
</script>
