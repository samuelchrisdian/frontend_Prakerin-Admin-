<template>
    <!-- <div>
        <div class="container">
            <form v-on:submit.prevent="Login">
                <b-form-group
                id="lbl_email"
                label="email"
                label-for="input_email"
                >
                <b-form-input id="input_email" v-model="email" trim></b-form-input>
                </b-form-group>
                <b-form-group
                id="lbl_password"
                label="Password"
                label-for="input_password"
                >
                <b-form-input type="password" id="input_password" v-model="password" trim></b-form-input>
                </b-form-group>
                <button
                    type="submit"
                    href="#pablo"
                    class="btn btn-primary btn-round btn-lg btn-block"
                    tabindex="4"
                    pill
                    >LOGIN
                </button>
            </form>
        </div>
    </div> -->


    <div class="container-scroller">
    <div class="page-body-wrapper full-page-wrapper">
      <div class="main-panel">
        <div class="content-wrapper d-flex align-items-center auth">
          <div class="row w-100">
            <div class="col-lg-4 mx-auto">
              <div class="auth-form-light text-left p-5">
                <div class="navbar-brand brand-logo">
                  <img height="auto" width="auto" class="my-3" src="../../public/img/Logo.png" alt="">
                </div>
                <h4>Selamat datang!</h4>
                <h6 class="font-weight-light">Login untuk mengelola admin.</h6>
                <!-- <form class="pt-3" method="post" action="#"> -->
                    <form v-on:submit.prevent="Login">
                <div class="form-group">
                  <div class="input-group">
                    <div class="input-group-prepend bg-transparent">
                      <span class="input-group-text bg-transparent border-right-0">
                        <i class="mdi mdi-account-outline text-primary"></i>
                      </span>
                    </div>
                    <input type="email" class="form-control form-control-lg border-left-0" v-model="email" id="input_email" name="email" placeholder="E-Mail" required>
                  </div>
                </div>
                <div class="form-group">
                  <div class="input-group">
                    <div class="input-group-prepend bg-transparent">
                      <span class="input-group-text bg-transparent border-right-0">
                        <i class="mdi mdi-lock-outline text-primary"></i>
                      </span>
                    </div>
                    <input type="password" class="form-control form-control-lg border-left-0" name="password" v-model="password" id="input_password" placeholder="Kata Sandi" required>                        
                  </div>
                </div>
                <div class="my-3">
                  <input type="submit" name="submit" class="btn btn-block btn-primary btn-lg font-weight-medium auth-form-btn" value="MASUK">
                </div>
              </form>
              </div>
            </div>
          </div>
        </div>
      </div>
      <!-- content-wrapper ends -->
    </div>
    <!-- page-body-wrapper ends -->
  </div>
</template>
<script>
export default {
    data() {
      return {
        email: '',
        password: ''
      }
    },
    methods: {
        Login: function(){
            let email = this.email 
            let password = this.password
            this.$store
            .dispatch('login', { email, password })
            .then(() => this.$router.push('/'))
            .catch(err => console.log(err))
        }
    }
}
</script>