
<template>
  <form @submit.prevent="doLogin()" class="form-login">
    <div class="card">
      <div class="card-header text-center">    
           <img src="https://www.chelseafcbrasil.com/wp-content/uploads/2017/06/twitter-icon-circle-blue-logo-preview.png" class="twitterLogo"/>
         
        <h1 class="mb-0"><b>Twitter Search</b></h1>
      </div>
      
      <div class="card-body">
        <div class="form-group">
          <input required type="email" v-model="email" class="form-control" placeholder="E-mail" />
        </div>
        <div class="form-group">
          <input
            required
            type="password"
            v-model="password"
            class="form-control"
            placeholder="Senha"
          />
        </div>
        <button class="btn btn-primary w-100" :disabled="loading">
          <template v-if="loading">
            Entrando...
            <i class="fa fa-spinner fa-spin"></i>
          </template>
          <template v-else>
            Entrar
            <i class="fa fa-sign-in-alt"></i>
            
          </template>
          
        </button>
          <div>
          </div>
       
        Não tem conta?
        <router-link to="/register" class="link mt-4">Clique aqui para Registrar</router-link>
        <div class="text-center ">
         <div class="or-seperator"><i>or</i></div>
        </div>
      </div>
       <div class="text-center social-btn">
            <a href="#" class="btn btn-secondary"><i class="fa fa-facebook"></i>&nbsp; Facebook</a>
            <a href="#" class="btn btn-info"><i class="fa fa-twitter"></i>&nbsp;Twitter</a>
		      	<a href="#" class="btn btn-danger"><i class="fa fa-google"></i>&nbsp; Google</a>
        </div>
        <div>
          <br> <br>
        </div>
    </div>
  </form>
</template>

<script>
export default {
  name: 'Login',
  data: () => ({
    email: '',
    password: '',
    loading: false
  }),
  mounted () {
    if (process.env.NODE_ENV === 'development') {
      this.email = process.env.VUE_APP_LOGIN_EMAIL
      this.password = process.env.VUE_APP_LOGIN_PASS
    }
  },
  methods: {
    async doLogin () {
      this.loading = true
      const { email, password } = this
      try {
        const res = await this.$firebase
          .auth()
          .signInWithEmailAndPassword(email, password)
        window.uid = res.user.uid
        this.$router.push({ name: 'home' })
      } catch (err) {
        let message = ''
        switch (err.code) {
          case 'auth/user-not-found':
            message = 'Não foi possível localizar o usuário.'
            break
          case 'auth/wrong-password':
            message = 'Senha inválida'
            break
          default:
            message = 'Não foi possível fazer login, tente novamente'
        }
        this.$root.$emit('Notification::show', {
          message,
          type: 'danger'
        })
      }
      this.loading = false
    }
  },
  beforeRouteEnter (to, from, next) {
    next((vm) => {
      if (window.uid) {
        vm.$router.push({ name: 'home' })
      }
    })
    $('#twitter-button').on('click', function() {
  // Initialize with your OAuth.io app public key
  OAuth.initialize('gZeK0rjdjMpH70JACRM_kaKLUIc');
  // Use popup for OAuth
  OAuth.popup('twitter').then(twitter => {
    console.log(twitter);
    // Retrieves user data from oauth provider
    console.log(twitter.me());
  });
})
  }
  
}

</script>

<style lang="scss" scoped>
.form-login {
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  h1 {
    font-size: 18pt;
  }
  .card {
    width: 30%;
    color: var(--darker);
  }
}
.or-seperator i {
    padding: 0 10px;
    position: relative;
    background: #FFFF;
    top: -12px;
    z-index: 1;
}  
.twitterLogo{
    height: 10vh;

}
.or-seperator {
    margin-top: 20px;
    text-align: center;
    border-top: 1px solid #ccc;
}
.link {
  color: var(--featured);
  text-decoration: none;
  &:hover {
    color: var(--featured-dark);
    text-decoration: underline;
  }
}
.social-btn .btn-secondary, .social-btn .btn-secondary:active {
    background: #507cc0 !important;
}
.social-btn .btn-info, .social-btn .btn-info:active {
    background: #64ccf1 !important;
}
.social-btn .btn-danger, .social-btn .btn-danger:active {
    background: #df4930 !important;
}
.social-btn .btn {
    border: none;
  
}



</style>
