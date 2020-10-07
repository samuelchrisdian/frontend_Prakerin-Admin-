<template>
  <div>
    <div class="container">
      <div class="row">
        <div class="col-lg-12 grid-margin stretch-card my-5 mx-5" style="padding-top:100px">
          <div class="card" style="margin-left:50px">
            <div class="card-body">
              <p class="card-title float-left"><b>Tabel Informasi</b></p>
              <p class="card-description float-right">
                <b-button variant="success" v-b-modal.modalInformasi v-on:click="Add"><i class="mdi mdi-plus btn-icon-prepend"></i> Tambah</b-button>
              </p>
              <div class="table-responsive">
                <b-table striped hover :items="informasi" :fields="fields">
                  <template v-slot:cell(Aksi)="data">
                    <b-button size="sm" variant="info" v-on:click="Edit(data.item)" v-b-modal.modalInformasi><i class="mdi mdi-pencil btn-icon-prepend"></i> Ubah</b-button>&nbsp;
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
      id="modalInformasi"
      @ok="Save"
    >
      <template v-slot:modal-title>
        Form Informasi
      </template>

        <form ref="form">
          <div class="form-group">
            <label for="nama_jalan" class="col-form-label">Nama Jalan</label>
            <input type="text" name="nama_jalan" class="form-control" id="nama_jalan" placeholder="Nama Jalan" v-model="nama_jalan">
          </div>
          <div class="form-group">
            <label for="hari" class="col-form-label">Hari</label>
            <input type="text" name="hari" class="form-control" id="hari" placeholder="Hari" v-model="hari">
          </div>
          <div class="form-group">
            <label for="jam" class="col-form-label">Jam</label>
            <input type="text" name="jam" class="form-control" id="jam" placeholder="Jam" v-model="jam">
          </div>
          <div class="form-group">
            <label for="harga_tiket" class="col-form-label">Harga Tiket</label>
            <input type="text" name="harga_tiket" class="form-control" id="harga_tiket" placeholder="Harga Tiket" v-model="harga_tiket">
          </div>
          <div class="form-group">
            <label for="cocok_untuk" class="col-form-label">Cocok Untuk</label>
            <input type="text" name="cocok_untuk" class="form-control" id="cocok_untuk" placeholder="Cocok Untuk" v-model="cocok_untuk">
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
      nama_jalan: "",
      hari: "",
      jam: "",
      harga_tiket: "",
      cocok_untuk: "",
      action: "",
      message: "",
      currentPage: 1,
      rows: 0,
      perPage: 10,
      key: "",
      informasi: [],
      fields: ["id", "nama_jalan", "hari", "jam", "harga_tiket", "cocok_untuk", "Aksi"],
    }
  },

 methods: {
    getData : function(){
      let conf = { headers: { "Authorization" : 'Bearer ' + this.key } };
      let offset = (this.currentPage - 1) * this.perPage;
      this.$bvToast.show("loadingToast");
      this.axios.get("/daftar_Informasi/" + this.perPage + "/" + offset, conf)
      .then(response => {
        if(response.data.status == 1){
          this.$bvToast.hide("loadingToast");
          this.informasi = response.data.informasi;
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
      this.nama_jalan   = "";
      this.hari         = ""; 
      this.jam          = "";
      this.harga_tiket  = "";
      this.cocok_untuk  = "";
    },
    Edit : function(item){
      this.action     = "update";
      this.id         = item.id;
      this.nama_jalan = item.nama_jalan;
      this.hari = item.hari;
      this.jam = item.jam;
      this.harga_tiket = item.harga_tiket;
      this.cocok_untuk = item.cocok_untuk;
      
    },

    Save : function(){
      let conf = { headers: { "Authorization" : 'Bearer ' + this.key } };
      this.$bvToast.show("loadingToast");
      if(this.action === "insert"){
        let form = new FormData();
        form.append("id", this.id);
        form.append("nama_jalan", this.nama_jalan);
        form.append("hari", this.hari);
        form.append("jam", this.jam);
        form.append("harga_tiket", this.harga_tiket);
        form.append("cocok_untuk", this.cocok_untuk);

        this.axios.post("/tambah_Informasi", form, conf)
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
        let form = {
          nama_jalan    : this.nama_jalan,
          hari          : this.hari,
          jam           : this.jam,
          harga_tiket   : this.harga_tiket,
          cocok_untuk   : this.cocok_untuk,
        }
        this.axios.post("/edit_Informasi/" + this.id, form, conf)
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
        this.axios.delete("/hapus_Informasi/" + id, conf)
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