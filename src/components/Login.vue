<template>
  <div class="loginpage">
    <div class="container">
      <div v-show="error" class="alert alert-warning alert-dismissible" role="alert">
        <button type="button" class="close" data-dismiss="alert" aria-label="Close"><span aria-hidden="true">&times;</span></button>
        <strong>Warning!</strong> The username and password combination is incorrect.
      </div>
      <form v-on:submit.prevent="login" class="form-signin">
        <h2 class="form-signin-heading">Please sign in</h2>
        <label for="inputEmail" class="sr-only">Email address</label>
        <input v-model="email" type="email" id="inputEmail" class="form-control" placeholder="Email address" required autofocus>
        <label for="inputPassword" class="sr-only">Password</label>
        <input v-model="password" type="password" id="inputPassword" class="form-control" placeholder="Password" required>
        <div class="checkbox">
          <label>
            <input type="checkbox" value="remember-me"> Remember me
          </label>
          <a v-link="{ path: '/forgot-password', activeClass: 'active' }">Forgot Passowrd?</a>
        </div>
        <button class="btn btn-lg btn-primary btn-block login-button" data-loading-text="Signing in..." type="submit">Sign in</button>
      </form>

    </div> <!-- /container -->
  </div>
</template>
<style scoped>
  .form-signin {
    max-width: 330px;
    padding: 15px;
    margin: 0 auto;
  }
</style>
<script>
  import NProgress from 'nprogress'
  export default {
    data () {
      return {
        email: '',
        password: '',
        error: false
      }
    },

    methods: {
      login () {
        NProgress.start()
        window.$('.login-button').button('loading')
        this.$http.post(this.$root.serverUrl + 'login', { email: this.email, password: this.password }).then((response) => {
          window.$('.login-button').button('reset')
          if (response.ok) {
            window.localStorage.setItem('jwt-token', response.data.token)
            window.localStorage.setItem('user', JSON.stringify(response.data.user))
            this.$dispatch('userLoggedIn', response.data.user)
            NProgress.done()
            this.$router.go('/home')
          } else {
            this.error = true
            NProgress.done()
          }
        }).catch(() => {
          this.error = true
          window.$('.login-button').button('reset')
          NProgress.done()
        })
      }
    }
  }
</script>
