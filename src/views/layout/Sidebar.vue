<template>
    <div class="main-sidebar">
        <aside id="sidebar-wrapper">
          <div class="sidebar-brand">
            <img height="100px" width="auto" class="my-3" src="../../../public/img/Logo.png" alt="">
          </div>
          <div class="sidebar-user" style="padding-top:75px">
            <div class="sidebar-user-picture" style="padding-left:10px">
              <img alt="image" src="../../../public/img/avatar/avatar-1.jpeg">
            </div>
            <div class="sidebar-user-details">
              <div class="user-name">Ujang Maman</div>
              <div class="user-role">
                Administrator
              </div>
            </div>
          </div>
          <ul class="sidebar-menu">
            <b><li class="menu-header">Dashboard</li></b>
            <li class="menu-title">
              <a href="/"><i class="ion ion-speedometer"></i><span>Dashboard</span></a>
            </li>
            <li class="menu-header">Components</li>
            <li>
              <a href="" class="has-dropdown"><i class="ion ion-ios-albums-outline"></i><span>Database</span></a>
              <ul class="menu-dropdown">
                <li><router-link to="/database"><i class="ion ion-ios-circle-outline"></i>Database</router-link></li>
                <li><router-link to="/tabelinformasi"><i class="ion ion-ios-circle-outline"></i>Tabel Informasi</router-link></li>
              </ul>
            </li>

            <li class="menu-header">Profile</li>
            <li>
              <b-nav-item @click="logout">
                  <i class="ion ion-log-out"></i><span>Logout</span>
              </b-nav-item>
            </li>
            </ul>

        </aside>
      </div>
</template>
<script>
export default {
    name: 'sidebar',
    computed : {
        isLoggedIn : function(){ return this.$store.getters.isLoggedIn}
    },
    methods:{
      logout:function(){
          let conf = { headers : {"Authorization" : "Bearer " + localStorage.getItem("Authorization")} };
          let form = new FormData();
          this.axios.post('/logout', form, conf).then(response => {
            if (response.data.logged === false || response.data.status === 0) {
                this.$store.commit('logout')
                localStorage.removeItem("Authorization")
                this.$router.push({name: 'login'})
            }
          }).catch(error => {

        });
      },
  },
}
</script>