<template>
  <div>
    <div class="container main-body">
      <nav class="navbar section">
        <div class="navbar-brand">
          <nuxt-link class="navbar-item" to="/">
            <img src="@/assets/amazing.png" width="120" height="28" />
          </nuxt-link>

          <div class="navbar-burger burger" data-target="top-menu">
            <span></span>
            <span></span>
            <span></span>
          </div>
        </div>

        <div id="top-menu" class="navbar-menu">
          <div class="navbar-start">
            <nuxt-link class="navbar-item" to="/"> Home </nuxt-link>
            <div
              v-if="userIsAdmin"
              class="navbar-item has-dropdown is-hoverable"
            >
              <a class="navbar-link is-active" href="#"> Admin </a>
              <div class="navbar-dropdown">
                <nuxt-link class="navbar-item" to="/admin/product-list">
                  Products
                </nuxt-link>
                <nuxt-link class="navbar-item" to="/admin/product-categories">
                  Product Categories
                </nuxt-link>
                <a class="navbar-item" href="#"> Orders </a>
                <a class="navbar-item" href="#"> Customers </a>
                <nuxt-link class="navbar-item" to="/admin/administrators">
                  Administrators
                </nuxt-link>
                <nuxt-link class="navbar-item" to="/admin/user-groups">
                  User Groups
                </nuxt-link>
              </div>
            </div>
          </div>

          <div class="navbar-start">
            <div
              v-if="userLoggedIn"
              class="navbar-item has-dropdown is-hoverable"
            >
              <a class="navbar-link is-active"> Hi, {{ username }} </a>
              <div class="navbar-dropdowm">
                <nuxt-link class="navbar-item" to="/user-profile">
                  Profile
                </nuxt-link>
                <nuxt-link class="navbar-item" to="/user-pwd-change"
                  >Change Password</nuxt-link
                >
                <a class="navbar-item" @click="logOut"> Log Out </a>
              </div>
            </div>
            <div v-else class="navbar-item">Hi {{ username }}</div>
            <div class="navbar-item">
              <div class="field is-grouped is-grouped-multiline">
                <p class="control">
                  <nuxt-link class="button" to="/cart">
                    <span class="icon is-small">
                      <i class="fa fa-shopping-cart"></i>
                    </span>
                    <span
                      >&bullet; {{ cart.items.length }}
                      {{ cart.items.length > 1 ? 'items' : 'item' }} ({{
                        cartTotal | currency
                      }})</span
                    >
                  </nuxt-link>
                </p>
              </div>
            </div>

            <p v-if="!userLoggedIn" class="control">
              <nuxt-link class="button is-warning" to="/login">
                <span class="icon is-small">
                  <i class="fa fa-unlock-alt"></i>
                </span>
                <span> Login </span>
              </nuxt-link>
            </p>

            <p v-if="!userLoggedIn" class="control">
              <nuxt-link class="button is-info" to="/signup">
                <span class="icon is-small">
                  <i class="fa fa-user-o"></i>
                </span>
                <span>Sign up</span>
              </nuxt-link>
            </p>
          </div>
        </div>
      </nav>
      <nuxt />
      <!-- This is where the pages are presented -->
    </div>

    <footer class="footer">
      <div class="container">
        <div class="content has-text-centered">
          <p>
            &copy; Amazing<br />
            created of Nuxt & Vue
          </p>
          <p>
            <img src="/nshop-icon.png" />
          </p>
        </div>
      </div>
    </footer>
  </div>
</template>

<script>
import cartMixin from '@/mixins/cartMixin'
export default {
  mixins: [cartMixin],
  data() {
    return {
      username: 'Guest',
    }
  },
  computed: {
    userProfile() {
      return this.$store.getters.user
    },
    userLoggedIn() {
      return this.$store.getters.loginStatus
    },
    userIsAdmin() {
      return this.$store.getters.userRole === 'admin'
    },
  },
  watch: {
    userProfile(value) {
      if (value) {
        this.username = value.name
      } else {
        this.username = 'Guest'
      }
    },
  },
  created() {
    if (!this.userLoggedIn) {
      this.$store.dispatch('setAuthStatus')
    }
  },
  mounted() {
    const cartInMemory = this.$warehouse.get('cart')
    if (this.cart.items.length === 0 && cartInMemory !== undefined) {
      this.$store.commit('catalog/reloadCart', cartInMemory)
    }
  },
  methods: {
    logOut() {
      this.$store.dispatch('logOut')
      this.$router.push('/')
    },
  },
}
</script>
