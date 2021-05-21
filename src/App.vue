<template>
  <div id="app">
    <base-spinner/>
    <layout-notification/>

    <div class="container-fluid" v-if="isLogged">
      <div class="row">
        <div class="col-2 navigation-sidebar">
             <img src="https://www.chelseafcbrasil.com/wp-content/uploads/2017/06/twitter-icon-circle-blue-logo-preview.png" class="twitterLogo"/>
          <h1 class="app-title">Twitter Search</h1>
          <layout-navigation/>
        </div>
        <div class="col">
          <router-view/>
        </div>
      </div>
    </div>

    <router-view v-else/>
  </div>
</template>

<script>
import BaseSpinner from './components/global/BaseSpinner'
import LayoutNavigation from './components/layout/LayoutNavigation'
import LayoutNotification from './components/layout/LayoutNotification'

export default {
  name: 'App',
  components: {
    BaseSpinner,
    LayoutNavigation,
    LayoutNotification
  },
  data: () => ({ isLogged: false }),
  mounted () {
    this.$firebase.auth().onAuthStateChanged(user => {
      window.uid = user ? user.uid : null
      this.isLogged = !!user

      this.$router.push({ name: window.uid ? 'home' : 'login' })

      setTimeout(() => {
        this.$root.$emit('Spinner::hide')
      }, 300)
    })
  }
}
</script>

<style lang="scss">
#app {
  min-height: 100vh;
  color: var(--light);
  background-color: var(--darker);
  .navigation-sidebar {
    background-color: var(--dark-medium);
    .app-title {
      font-size: 20pt;
      margin-top: 10px;
      text-align: center;
    }

  }
  .twitterLogo{
    height: 10vh;
    display: block;
    margin-top: 2vh;
    margin-left: auto;
    margin-right: auto 
    
}
}
</style>
