<template>
    <div class="container-fluid page-body-wrapper full-page-wrapper">
      <div class="main-panel">
        <div class="content-wrapper d-flex align-items-center auth">
          <div class="row w-100">
            <div class="col-lg-4 mx-auto">
              <div class="auth-form-light text-left p-5">
                <div class="navbar-brand brand-logo">
                  <img height="auto" width="auto" class="my-3" src="../../public/img/Logo.png" alt="">
                </div>
                <h4>Selamat datang!</h4>
                <h6 class="font-weight-light">Daftar untuk menggunakan survey wisata.</h6>
                <form class="pt-3" v-on:submit.prevent="Register">
                  <div class="form-group">
                    <div class="input-group">
                      <div class="input-group-prepend bg-transparent">
                        <span class="input-group-text bg-transparent border-right-0">
                          <i class="mdi mdi-account-outline text-primary"></i>
                        </span>
                      </div>
                      <input type="text" class="form-control form-control-lg border-left-0" id="name" name="name" placeholder="Name" v-model="name" required>
                    </div>
                  </div>
                  <div class="form-group">
                    <div class="input-group">
                      <div class="input-group-prepend bg-transparent">
                        <span class="input-group-text bg-transparent border-right-0">
                          <i class="mdi mdi-account-outline text-primary"></i>
                        </span>
                      </div>
                      <input type="file" class="form-control form-control-lg border-left-0" id="foto" name="foto" v-on:change="onFileChange()" placeholder="Foto" required>
                    </div>
                  </div>
                  <div class="form-group">
                    <div class="input-group">
                      <div class="input-group-prepend bg-transparent">
                        <span class="input-group-text bg-transparent border-right-0">
                          <i class="mdi mdi-message-outline text-primary"></i>
                        </span>
                      </div>
                      <input type="email" class="form-control form-control-lg border-left-0" id="email" name="email" placeholder="E-Mail" v-model="email" required>
                    </div>
                  </div>
                  <div class="form-group">
                    <div class="input-group">
                      <div class="input-group-prepend bg-transparent">
                        <span class="input-group-text bg-transparent border-right-0">
                          <i class="mdi mdi-lock-outline text-primary"></i>
                        </span>
                      </div>
                      <input type="password" class="form-control form-control-lg border-left-0" name="password" id="password" placeholder="New Password" v-model="password" required>                        
                    </div>
                  </div>
                  <div class="form-group">
                    <div class="input-group">
                      <div class="input-group-prepend bg-transparent">
                        <span class="input-group-text bg-transparent border-right-0">
                          <i class="mdi mdi-cellphone-iphone text-primary"></i>
                        </span>
                      </div>
                      <input type="text" class="form-control form-control-lg border-left-0" name="no_telp" id="no_telp" placeholder="Nomer Handphone Anda"  v-model="no_telp" required>                        
                    </div>
                  </div>
                  <div class="my-3">
                    <input type="submit" name="submit" class="btn btn-block btn-primary btn-lg font-weight-medium auth-form-btn" v-on:click="submitFile()" value="REGISTER" href="#pablo">
                  </div>
                </form>
                <a href="/login" align-text="center"> Sudah memiliki akun? Klik disini</a>
              </div>
            </div>
          </div>
        </div>
      </div>
      <!-- content-wrapper ends -->
    </div>
</template>

<script>
export default {
    data() {
      return {
        name: '',
        foto: '',
        email: '',
        password: '',
        no_telp: ''
      }
    },
    methods: {
        Register : function(){
        let name = this.name;
        let foto = this.foto;
        let email = this.email;
        let password = this.password;
        let no_telp = this.no_telp;
        this.$store
        .dispatch('register',{
          name,
          foto,
          email,
          password,
          no_telp
       })
        .then(() => this.$router.push('/login'))
        .catch(err => console.log(err));
     },
     onFileChange(e) {
      var files = e.target.files || e.dataTransfer.files;
      if (!files.length)
        return;
      this.createImage(files[0]);
      },
      createImage(file) {
        var foto = new Foto();
        var reader = new FileReader();
        var vm = this;

      reader.onload = (e) => {
        vm.foto = e.target.result;
      };
      reader.readAsDataURL(file);
    }
  }
}
</script>