<template>
  <div>
    <div class="container">
      <div class="row">
        <div class="col-lg-12 grid-margin stretch-card my-5 mx-5" style="padding-top:100px">
          <div class="card" style="margin-left:50px">
            <div class="card-body">
              <p class="card-title float-left"><b>Database</b></p>
              <p class="card-description float-right">
                <b-button variant="success" v-b-modal.modalDB v-on:click="Add"><i class="mdi mdi-plus btn-icon-prepend"></i> Tambah</b-button>
              </p>
              <div class="table-responsive">
                <b-table striped hover :items="wisata" :fields="fields">
                  <template v-slot:cell(foto)="data">
                    <img style="width:200px; height: 150px; border-radius:10%" :src="'http://localhost:8001/Backend_WEB/' + data.item.foto" />
                  </template>
                  <template v-slot:cell(Aksi)="data">
                    <b-button size="sm" variant="info" v-on:click="Edit(data.item)" v-b-modal.modalDB><i class="mdi mdi-pencil btn-icon-prepend"></i> Ubah</b-button>&nbsp;
                    <b-button size="sm" variant="danger" v-on:click="Drop(data.item.id)"><i class="mdi mdi-delete btn-icon-prepend"></i> Hapus</b-button>
                  </template>
                </b-table>
                <b-pagination
                  v-model="currentPage"
                  :per-page="perPage"
                  :total-rows="rows"
                  align="center"
                  v-on:input="pagination">
                </b-pagination>

                <b-toast id="loadingToast" title="Processing Data" no-auto-hide>
                  <b-spinner label="Spinning" variant="success"></b-spinner>
                  <strong class="text-success">Loading...</strong>
                </b-toast>

                <!-- toast untuk tampilan message box -->
                <b-toast id="message" title="Message">
                  <strong class="text-success">{{ message }}</strong>
                </b-toast>

              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <b-modal 
      id="modalDB"
      @ok="Save"
    >
      <template v-slot:modal-title>
        Form Wisata
      </template>
        <form ref="form">
          <div class="form-group">
            <label for="nama_wisata" class="col-form-label">Nama Wisata</label>
            <input type="text" name="nama_wisata" class="form-control" id="nama_wisata" placeholder="Nama Wisata" v-model="nama_wisata">
          </div>
          <div class="form-group">
            <label for="nama_daerah" class="col-form-label">Nama Daerah</label>
            <input type="text" name="nama_daerah" class="form-control" id="nama_daerah" placeholder="Nama Daerah" v-model="nama_daerah">
          </div>
          <div class="form-group">
            <label for="jenis" class="col-form-label">Jenis</label>
            <input type="text" name="jenis" class="form-control" id="jenis" placeholder="Jenis" v-model="jenis">
          </div>
          <div class="form-group">
            <label for="foto" class="col-form-label">Foto</label>
            <input type="file" name="foto" class="form-control" id="foto" placeholder="Foto" >
          </div>
          <div class="form-group">
            <label for="deskripsi" class="col-form-label">Deskripsi</label>
            <input type="text" name="deskripsi" class="form-control" id="deskripsi" placeholder="deskripsi" v-model="deskripsi">
          </div>
          <div class="form-group">
            <label for="akses" class="col-form-label">Akses</label>
            <input type="text" name="akses" class="form-control" id="akses" placeholder="akses" v-model="akses">
          </div>
        </form>
    </b-modal>
  </div>
</template>

<script>
module.exports = {
  data : function(){
    return {
      search: "",
      id: "",
      nama_wisata: "",
      nama_daerah: "",
      jenis: "",
      foto: "",
      file: "",
      nama_file: "",
      deskripsi: "",
      akses: "",
      action: "",
      message: "",
      currentPage: 1,
      rows: 0,
      perPage: 10,
      key: "",
      wisata: [],
      fields: ["id", "nama_wisata", "nama_daerah", "jenis", "foto", "deskripsi", "akses", "Aksi"],
    }
  },

 methods: {
    getData : function(){
      let conf = { headers: { "Authorization" : 'Bearer ' + this.key } };
      let offset = (this.currentPage - 1) * this.perPage;
      this.$bvToast.show("loadingToast");
      this.axios.get("/daftar_Wisata/" + this.perPage + "/" + offset, conf)
      .then(response => {
        if(response.data.status == 1){
          this.$bvToast.hide("loadingToast");
          this.wisata = response.data.wisata;
          this.rows = response.data.count;
        } else {
          this.$bvToast.hide("loadingToast");
          this.message = "Gagal menampilkan data siswa."
          this.$bvToast.show("message");
          this.$router.push({name: "login"})
        }
        
      })
      .catch(error => {
        console.log(error);
      });
    },

    pagination : function(){
      if(this.search == ""){
        this.getData();
      } else {
        this.searching();
      }
    },

    Add : function(){
      this.action       = "insert";
      this.nama_wisata  = "";
      this.nama_daerah  = "";
      this.jenis        = "";  
      this.foto         = ""; 
      this.deskripsi    = "";
      this.akses        = "";
      document.getElementById("foto").value = null;
    },

    Edit : function(item){
      this.action     = "update";
      this.id         = item.id;
      this.nama_wisata  = item.nama_wisata;
      this.nama_daerah  = item.nama_daerah;
      this.jenis        = item.jenis;
      this.akses        = item.akses;
      this.foto         = item.foto;
      this.deskripsi    = item.deskripsi;
      document.getElementById("foto").value = null;
    },

    Save : function(){
      let conf = { headers: { "Authorization" : 'Bearer ' + this.key } };
      this.$bvToast.show("loadingToast");
      if(this.action === "insert"){
        let form = new FormData();
        form.append("id", this.id);
        form.append("nama_wisata", this.nama_wisata);
        form.append("nama_daerah", this.nama_daerah);
        form.append("jenis", this.jenis);
        form.append("akses", this.akses);
        form.append("foto", document.getElementById("foto").files[0]);
        form.append("deskripsi", this.deskripsi);
        
        this.axios.post("/tambah_Wisata/", form, conf)
        .then(response => {
          this.$bvToast.hide("loadingToast");
          if(this.search == ""){
            this.getData();
          } else {
            this.searching();
          }
          this.message = response.data.message;
          this.$bvToast.show("message");
        })
        .catch(error => {
          console.log(error);
        });
      } else {
        // let form = {
        //   nama_wisata       : this.nama_wisata,
        //   nama_daerah: this.nama_daerah,
        //   jenis     : this.jenis,
        //   // foto     : this.foto,
        //   deskripsi   : this.deskripsi,
        // }
        let form = new FormData();
        form.append("id", this.id);
        form.append("nama_wisata", this.nama_wisata);
        form.append("nama_daerah", this.nama_daerah);
        form.append("jenis", this.jenis);
        form.append("foto", document.getElementById("foto").files[0]);
        form.append("deskripsi", this.deskripsi);
        form.append("akses", this.akses);
        this.axios.post("/edit_Wisata/" + this.id, form, conf)
        .then(response => {
          this.$bvToast.hide("loadingToast");
          if(this.search == ""){
            this.getData();
          } else {
            this.searching();
          }
          this.message = response.data.message;
          this.$bvToast.show("message");
        })
        .catch(error => {
          console.log(error);
        });
      }
    },

    Drop : function(id){
      let conf = { headers: { "Authorization" : "Bearer " + this.key} };
      if(confirm('Apakah anda yakin ingin menghapus data ini?')){
        this.$bvToast.show("loadingToast");
        this.axios.delete("/hapus_Wisata/" + id, conf)
        .then(response => {
            this.getData();
            this.$bvToast.hide("loadingToast");
            this.message = response.data.message;
            this.$bvToast.show("message");
        })
        .catch(error => {
          console.log(error);
        });
      }
    },
  },

  mounted(){
    this.key = localStorage.getItem("Authorization");
    this.getData();
  }
}
</script>