<template>
<div class="container">
    <div class="row">
        <div class="col-md-8 col-md-offset-2">
            <ul class="breadcrumb">
              <li>Home</li>
              <li><router-link :to="{name: 'IndexTransaksiKas'}">Transaksi Kas</router-link></li>
              <li class="active">Tambah Kas Mutasi</li>
            </ul>
            <div class="panel panel-default">
                <div class="panel-heading">Tambah Kas Mutasi</div>

                <div class="panel-body">
                    <form v-on:submit.prevent="saveForm()" class="form-horizontal" >
                      <div class="form-group">
                        <label for="name" class="col-md-2 control-label" >Dari Kas</label>
                        <div class="col-md-4">
                        <vue-selectize
                          v-model="transaksiKas.akun_keluar" 
                          class="form-control" 
                          required="" >
                            <option value="">Pilih Kas</option>
                            <option v-for="akun in akuns" :value="akun.id">{{ akun.nama}}</option>
                          </vue-selectize>
                        <span v-if="errors.akun_keluar" class="label label-danger"> {{ errors.akun_keluar[0]}}</span>
                        </div>
                      </div>
                      <div class="form-group">
                        <label for="name" class="col-md-2 control-label" >Ke Kas</label>
                        <div class="col-md-4">
                        <vue-selectize v-model="transaksiKas.akun_masuk" class="form-control" required="" >
                            <option value="">Pilih Kas</option>
                            <option v-for="akun in akuns" :value="akun.id">{{ akun.nama}}</option>
                          </vue-selectize>
                        <span v-if="errors.akun_masuk" class="label label-danger"> {{ errors.akun_masuk[0]}}</span>
                        </div>
                      </div>
                      <div class="form-group">
                        <label for="name" class="col-md-2 control-label" >Keterangan</label>
                        <div class="col-md-4">
                          <input 
                            type="text" class="form-control"
                            placeholder="Keterangan" v-model="transaksiKas.deskripsi" />
                          <span 
                            v-if="errors.deskripsi"
                            class="label label-danger"> 
                            {{ errors.deskripsi[0]}}
                          </span>
                        </div>
                      </div>
                      <div class="form-group">
                        <label for="name" class="col-md-2 control-label" >Jumlah</label>
                        <div class="col-md-4">
                          <input 
                            type="text" 
                            class="form-control" 
                            required=""
                            placeholder="Jumlah Mutasi"
                            v-model="transaksiKas.jumlah"/>
                          <span v-if="errors.jumlah" class="label label-danger"> {{ errors.jumlah[0]}}</span>
                        </div>
                      </div>
                      <div class="form-group">
                        <div class="col-md-4 col-md-offset-2">
                          <button class="btn btn-primary" type="submit">Submit</button>
                        </div>
                      </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>
</template>


<script>
  export default {
    data: function() {
      return {
        transaksiKas: {
          akun_masuk: '',
          akun_keluar: '',
          deskripsi: '',
          jumlah: ''
        },
        akuns: [],
        url: window.location.origin + (window.location.pathname).replace("home","transaksi-kas"),
        errors: [],
        message: ''
      }
    },
    mounted()  {
     var app = this;
     app.getAkuns();
    },
    methods: {
      dangerAlert(){
        this.$swal({
          title: "Terjadi Kesalah",
          text: "Kas Tidak boleh sama",
          icon: "failure"
        });
      },
      alert(pesan){
        this.$swal({
          title: "Berhasil Menambah Kas Mutasi",
          text: pesan,
          icon: "success"
        });
      },
      saveForm(){
        if(this.transaksiKas.akun_masuk === this.transaksiKas.akun_keluar){
           this.dangerAlert();
        } else { 
            var newTransaksiKas = this.transaksiKas;
            axios.post(this.url + '/kas-mutasi',newTransaksiKas)
            .then((resp) => {
              this.alert('Berhasil Menambah Kas Mutasi Sejumlah' + this.transaksiKas.jumlah);
              this.$router.replace('/transaksi-kas/');
            })
            .catch((resp) =>{
              if(resp.response.status == 500) alert('Something Goes Wrong');
              this.errors = resp.response.data.errors;
              console.log(resp);
            });
        }
      },
      getAkuns(){ 
        var app = this;
        axios.get(app.url.replace("transaksi-kas","akun") + '/all?kas=1')
        .then(function(resp){
          app.akuns = resp.data;
        })
        .catch(function(resp){
          console.log(resp); 
        })
      }
    }
  }

</script>
