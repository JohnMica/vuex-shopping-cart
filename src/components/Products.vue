<template>
	<div class='content main-content'>
    <div class="categories">
        <div class="block" v-show='categShown' v-for="category in getCategories" :key="category.id"  :class="{ 'is-gray': selectedCategory == category }">
          <a class='button' @click="selectedCategory = category; hideCateg(); initialSubCateg(); updateNav(selectedCategory); filterProd(selectedCategory)" >
            {{ category }} 
          </a>
        </div>
    </div>
    <div class=" subcategories" v-show='!categShown'  v-if='selectedCategory'>
        <button class="button" v-for="subcategory in getSubcategories" :key="subcategory.id" @click="selectedSubCategory = subcategory" :class="{ 'is-gray': selectedSubCategory == subcategory }">
          {{ subcategory }} 
        </button>

    </div>
		<div class="columns is-multiline is-mobile no-padding">
      <div class="column is-12-phone is-4-mobile is-4-tablet is-4-desktop" v-show='!categShown'  v-for="link in filteredSubcateg" v-if='selectedCategory === link.category' :key="link.id" :class="{current: selectedSubCategory == link.subcategory }">
        <div class="card ">
          <div class="card-image">
            <figure class="image ">
              <img  :src="link.image" :alt="link.product">
            </figure>
          </div>
          <div class="card-content">
            <div class="title is-size-4">
              {{ link.product }} 
            </div>
            <div class="subtitle is-size-5">
              {{ link.description }} 
            </div>
          </div>
          <footer class="card-footer">
            <p class="card-footer-item">
              <span>
                {{ link.price | currency }}
              </span>
            </p>
            <p class="card-footer-item" v-if="!link.showing">
              <a @click.prevent.stop="()=>{addToCart(link); toggleDisplay(link); checkthisitemisincart(link) }"  class="button is-gray" href="#">Add To Cart</a>
            </p>
            <template  v-if='link.showing' v-for='cartItem of cartItems'  >
              <template v-if="link.id === cartItem.id ">
                <p class="card-footer-item" :key="cartItem.id" >
                  <span  class='addToCart-buttons'>
                      <button @click="decreaseItem(cartItem)"  class="button is-medium" >-</button>
                        <span >{{ cartItem.quantity }}</span>
                      <button @click="addToCart(cartItem)" class="button is-medium" >+</button>
                  </span>
                </p>
              </template>
            </template>
         
          
          </footer>
        </div>
      </div>
    </div>
	</div>
</template>

<script>
import { mapGetters, mapActions, mapMutations } from "vuex";
import store from "../store/index.js";

export default {
  name: "Products",
  created() {
    this.$store.dispatch("fetchProducts", { self: this });
  },
  mounted() {
    // this.initialSubCateg;
  },
  computed: {
    ...mapGetters({
      products: "allProducts",
      length: "getNumberOfProducts",
      cartItems: "cartProducts",
      categShown:'categShown'
    }),

      filteredSubcateg() {
        return this.$store.getters.filteredSubcateg(this.selectedSubCategory)
      },

    getCategories() {
      let categoriesSet = new Set();
      this.$store.state.all.forEach(link => {
        categoriesSet.add(link.category);
      });
      return (this.categories = Array.from(categoriesSet));
    },
    getSubcategories() {
      let subcategoriesSet = new Set();
      this.$store.state.filteredProducts.forEach(link => {
        subcategoriesSet.add(link.subcateg);
      });
      return (this.subcategories = Array.from(subcategoriesSet));
    },

    categlength() {
      return this.categories ? this.categories.length : 0;
    }
  },
  methods: {
    ...mapActions(    {
      filterProd: 'filterProducts'
    }),
    initialSubCateg() {
      setTimeout(() => {
        return (this.selectedSubCategory = this.getSubcategories[0]);
      }, 150);
    },
    hideCateg() {
      this.$store.dispatch('hideCateg')
    },
    updateNav(value){
      this.$store.dispatch('updateNav', value)
      },
    categoryCount(category) {
      return this.products.filter(link => {
        return link.category.match(category);
      });
    },
    ...mapActions({
      addToCart: "addToCart",
      decreaseItem: "decreaseItem"
    }),
    toggleDisplay(item) {
      item.showing = !item.showing;
    },
    checkthisitemisincart(existingItem) {
      existingItem = this.products.filter(item => item.id == existingItem.id);
    },

  filteredProducts(category) {
     return this.$store.state.all.filter(el => el.category === category)
  }

  },
  data() {
    return {
      selectedCategory: "",
      selectedSubCategory:'',
      categories: [],
      subcategories: []
    };
  }
};
</script>

<style lang="scss">
.main-content {
  padding-top: 2rem;
}
.no-padding {
  padding: 0;
  margin-left: 0;
  margin-right: 0;
}
.button {
  border-radius: 0;
}
.categories {
  .button {
    margin-right: 0.5rem;
    margin-bottom: 0.5rem;
    border-radius: 0;
    font-feature-settings: "tnum";
    font-variant-numeric: tabular-nums;
  }
}
.columns {
  margin-top: 1;
  align-items: stretch;
  justify-content: center;
  width: 100%;
  .column {
    margin-top: 1rem;
    margin-bottom: 1rem;

    &:first-child {
      padding-left: 0;
    }
    &:nth-child(even) {
      padding-left: 0;
    }
    &:nth-child(odd) {
      padding-right: 1rem;
    }
  }
  figure {
    margin-left: 0;
    margin-right: 0;
  }
}
.card {
  height: 100%;
  display: flex;
  flex-direction: column;
}
.card-content {
  flex: 1 0 auto;
}
.card-footer {
  margin-top: auto;
  flex-shrink: 0;
}

.button.is-gray {
  border-color: none;
  background: rgb(53, 77, 91);
  outline: none;
  color: whitesmoke;
  font-feature-settings: "tnum";
  font-variant-numeric: tabular-nums;
  &:active,
  &:focus {
    border-color: none;
    background: rgb(53, 77, 91);
    outline: none;
    color: whitesmoke;
  }
}
.card-footer {
  margin-top: auto;
}
.addToCart-buttons {
  display: inline-flex;
  width: 100%;
  justify-content: space-around;
  align-items: center;
  .button {
    border-radius: 50%;
    background-color: rgb(53, 77, 91);
    color: whitesmoke;
    font-feature-settings: "tnum";
    font-variant-numeric: tabular-nums;
    width: 2.25em;
    line-height: 1rem;
    &:active,
    &:focus,
    &:hover,
    &:visited {
      outline: 0;
      border: none;
    }
  }
}
@media screen and(max-width: 576px) {
  .column.is-12-phone {
    // flex: none;
    width: 50%;
    padding-left: 0;
    padding-right: 0;
    &:first-child {
      padding-left: 0;
    }
    &:nth-child(even) {
      padding-left: 0.5rem;
    }
    &:nth-child(odd) {
      padding-right: 0.5rem;
    }
    &:last-child {
      padding-right: 0;
    }
    .card-footer {
      flex-direction: column;
      padding-top: 1rem;
      padding-bottom: 2rem;
    }
  }
}

// @media screen and (max-width: 768px) {
//   .columns {
//     .column {
//       margin-left: 0;
//       margin-right: 0;
//       padding-left: 0;
//       padding-right: 0;
//     }
//   }
// }
.input {
  width: 50px;
  margin-left: 0;
  margin-right: 0;
}
input[type="number"]::-webkit-inner-spin-button,
input[type="number"]::-webkit-outer-spin-button {
  -webkit-appearance: none;
  margin: 0;
}
</style>
