<template>
  <b-card
    class="mt-3"
    :header = "card_header"
    header-bg-variant = "success"
    header-text-variant = "white">

    <b-row>
      <b-col>Jenis Dokumen</b-col>
      <b-col cols="9">
        <b-form-select v-model="id_jenis_dokumen" v-on:change="get_document">
          <option value="0">--Semua Jenis Dokumen--</option>
          <option v-for="item in jenis_dokumen" :key="item.id_jenis_dokumen">
            {{ item.nama_jenis_dokumen }}
          </option>
        </b-form-select>
      </b-col>
    </b-row>

    <b-row class="my-2">
      <b-col>Pencarian</b-col>
      <b-col cols="9">
        <b-form-input v-model="search" placeholder="Kata Kunci" v-on:keyup.enter="searching">
        </b-form-input>
      </b-col>
    </b-row>

    <!-- <b-alert variant="info" class="my-1" dismissible :show="rows == 0">
        {{ pesan }}
    </b-alert> -->

    <b-list-group class="my-3">
      <b-list-group-item v-for="item in documents" :key="item.id_jenis_dokumen" class="mb-2">
        <b-row>
          <b-col cols="10">
            <h5>{{ item.id_dokumen + " / " + item.nama_dokumen }}</h5>
            <small>No. Revisi: {{ item.no_revisi }}</small> <br>
            <small>Deskripsi: {{ item.deskripsi }}</small> <br>
            <small>Author: {{ item.nama_petugas }}</small>
          </b-col>
          <b-col>
            <b-button v-b-modal.modal1
              pill
              @click="preview(item)"
              variant="outline-success"
              block size="sm">
              <span class="fa fa-eye"></span> Preview
            </b-button>
            <b-button block variant="outline-info" size="sm" pill
              v-b-modal.modal_document
              v-on:click="Edit(item)">
                <span class="fa fa-edit"></span> Edit
            </b-button>
            <b-button pill block variant="outline-danger" size="sm" v-on:click="Drop(item.id_dokumen)">
                <span class="fa fa-trash"></span> Hapus
            </b-button>
          </b-col>
        </b-row>
      </b-list-group-item>
    </b-list-group>

     <b-pagination
      size="md"
      :total-rows="rows"
      v-model="currentPage"
      :per-page="perPage"
      align="center"
      v-on:input="pagination">
    </b-pagination>

    <b-button variant="success" v-on:click="Add" v-b-modal.modal_document pill>
        <span class="fa fa-plus"></span> Tambah Data
    </b-button>

       <!-- Modal Component -->
    <b-modal
      id="modal_document"
      title="Form Dokumen ISO"
      header-bg-variant="info"
      header-text-variant="white"
      border-variant="info" hide-footer>

      <b-container fluid>
        <form v-on:submit.prevent="Save">
          ID Dokumen
          <b-form-input v-model="id_dokumen" class="mb-1" required></b-form-input>
          Nama Dokumen
          <b-form-input v-model="nama_dokumen" class="mb-1" required></b-form-input>
          Deskripsi
          <b-form-textarea v-model="deskripsi" class="mb-1" rows="3" required></b-form-textarea>
          No. Revisi
          <b-form-input v-model="no_revisi" type="number" class="mb-1" required></b-form-input>
          Jenis Dokumen
          <b-form-select v-model="id_doc_type" class="mb-1" required>
            <option v-for="item in doc_type" :key="item.id_jenis_dokumen">
              {{ item.nama_jenis_dokumen }}
            </option>
          </b-form-select>
          Author
          <b-form-select v-model="id_petugas" class="mb-1" required>
            <option v-for="item in petugas" :key="item.id_petugas">
              {{ item.nama_petugas }}
            </option>
          </b-form-select>
          File
          <b-form-file v-model="file" id="file" class="mb-1" :required="req_file"></b-form-file>
          <b-button pill type="submit" variant="success" size="md" class="pull-right">
            <span class="fa fa-check"></span> Simpan
          </b-button>
        </form>
      </b-container>

    </b-modal>

    <b-toast id="loadingToast" title="Processing Data" no-auto-hide>
      <b-spinner label="Spinning" variant="success"></b-spinner>
      <strong class="text-success">Loading...</strong>
    </b-toast>

    <!-- toast untuk tampilan message box -->
    <b-toast id="message" title="Message">
      <strong class="text-success">{{ message }}</strong>
    </b-toast>

    <b-modal id="modal1" :title="document_name" size="xl"
      header-bg-variant="success"
      header-text-variant="white"
      border-variant="success">
      <iframe :src="source_file" width="100%" height="450"></iframe>
      <template v-slot:modal-footer>
        <b-button variant="secondary" @click="$bvModal.hide('modal1')">
            Close
        </b-button>
      </template>
    </b-modal>

  </b-card>
</template>

<script type="text/javascript">
  module.exports = {
    data : function(){
      return {
        card_header: "Daftar Dokumen " + this.$route.params.id_unit,
        id_unit: this.$route.params.id_unit,
        id_jenis_dokumen: "",
        jenis_dokumen: [],
        id_doc_type: "",
        doc_type: [],
        petugas: [],
        id_dokumen: "",
        id_dokumen_old: "",
        nama_dokumen: "",
        deskripsi: "",
        no_revisi: "",
        file: "",
        id_petugas: "",
        action: "",
        message: "",
        documents: [],
        rows: 0,
        perPage: 10,
        currentPage: 1,
        search: "",
        pesan: "",
        source_file: "",
        document_name: "",
        key: "",
        req_file: true,
      }
    },

    methods : {
      init_petugas : function(){
        let conf = { headers: { "Api-Token" : this.key} };
        axios.get(base_url+"/authors", conf)
        .then(response => {
          this.petugas = response.data.petugas;
        })
        .catch(error => {
          console.log(error);
        });
      },

      init_jenis_dokumen : function(){
        let conf = { headers: { "Api-Token" : this.key} };
        axios.get(base_url+"/document_unit/"+this.$route.params.id_unit, conf)
        .then(response => {
          if (response.data.count > 0) {
            this.jenis_dokumen = response.data.document_type;
            this.id_jenis_dokumen = "0";
            this.get_document("0");
          }
        })
        .catch(error => {
          console.log(error);
        });
      },

      init_doc_type : function(){
        let conf = { headers: { "Api-Token" : this.key} };
        axios.get(base_url+"/documents", conf)
        .then(response => {
          this.doc_type = response.data.documents;
          this.id_doc_type = this.doc_type[0].id_jenis_dokumen;
        })
        .catch(error => {
          console.log(error);
        });
      },

      Add : function(){
        this.action = "insert";
        this.id_dokumen = "";
        this.id_dokumen_old = "";
        this.nama_dokumen = "";
        this.deskripsi = "";
        this.no_revisi = "";
        this.id_doc_type = this.id_jenis_dokumen;
        this.file = null;
        this.id_petugas = "";
        this.req_file = true;
      },

      Edit : function(item){
        this.action = "update";
        this.id_dokumen = item.id_dokumen;
        this.id_dokumen_old = item.id_dokumen;
        this.nama_dokumen = item.nama_dokumen;
        this.deskripsi = item.deskripsi;
        this.no_revisi = item.no_revisi;
        this.id_doc_type = item.id_jenis_dokumen;
        this.file = null;
        this.id_petugas = item.id_petugas;
        this.req_file = false;
      },

      Save : function(){
        let conf = { headers: { "Api-Token" : this.key} };
        this.$bvModal.hide("modal_document");
        this.$bvToast.show("loadingToast");
        let form = new FormData();
        form.append("action", this.action);
        form.append("id_dokumen", this.id_dokumen);
        form.append("id_dokumen_old", this.id_dokumen_old);
        form.append("id_unit", this.id_unit);
        form.append("nama_dokumen", this.nama_dokumen);
        form.append("deskripsi", this.deskripsi);
        form.append("no_revisi", this.no_revisi);
        form.append("id_jenis_dokumen", this.id_doc_type);
        form.append("id_petugas", this.id_petugas);
        form.append("file", document.getElementById("file").files[0]);

        axios.post(base_url+"/documents/save", form, conf)
        .then(response => {
          this.message = response.data.message;
          this.$bvToast.hide("loadingToast");
          this.$bvToast.show("message");
          this.get_document(this.id_jenis_dokumen);
        })
        .catch(error => {
          console.log(error);
        });
      },

      get_document : function(id){
        let conf = { headers: { "Api-Token" : this.key} };
        let offset = (this.currentPage-1) * this.perPage;
        this.$bvToast.show("loadingToast");
        axios.get(base_url+"/documents/"+this.id_unit+"/"+
        id+"/"+this.perPage+"/"+offset, conf)
        .then(response => {
          this.$bvToast.hide("loadingToast");
          this.documents = response.data.documents;
          this.rows = response.data.count;
        })
        .catch(error => {
          console.log(error);
        });
      },

      searching : function(){
        let conf = { headers: { "Api-Token" : this.key} };
        let offset = (this.currentPage-1) * this.perPage;
        this.$bvToast.show("loadingToast");
        let form = new FormData();
        form.append("find", this.search);
        this.id_jenis_dokumen = "0";
        axios.post(base_url+"/documents/"+this.id_unit+"/"+
        this.perPage+"/"+offset, form, conf)
        .then(response => {
          this.$bvToast.hide("loadingToast");
          this.documents = response.data.documents;
          this.rows = response.data.count;
        })
        .catch(error => {
          console.log(error);
        });
      },

      pagination : function(){
        if (this.search == "") {
          this.get_document(this.id_jenis_dokumen);
        }else{
          this.searching();
        }
      },

      preview : function(item){
        this.document_name = item.nama_dokumen;
        this.source_file = "http://docs.google.com/gview?url="+item.link+"&embedded=true";
      },

    //   authenticate : function(){
    //     if (!this.$cookies.isKey("Api-Token")) {
    //       window.location = "../index.html";
    //     }else{
    //       let token = this.$cookies.get("Api-Token");
    //       let form = new FormData();
    //       form.append("token",token);
    //       axios.post(base_url+"/auth/check", form)
    //       .then(response => {
    //         if (response.data.auth == false) {
    //           window.location = "../login.html";
    //         }else {
    //           this.key = token;
    //           this.init_jenis_dokumen();
    //           this.init_doc_type();
    //           this.init_petugas();
    //         }
    //       })
    //       .catch(error => {
    //         console.log(error);
    //       });
    //     }
    //   },

    // },

  //   mounted(){
  //     this.authenticate();
  //   }
  // }
  }
}
</script>
