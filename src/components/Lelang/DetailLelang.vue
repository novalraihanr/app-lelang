<template>
  <div id="wrapper">
    <sidebar-component></sidebar-component>
    <div id="content-wrapper" class="d-flex flex-column">
      <div id="content">
        <navbar-component></navbar-component>
        <div class="container-fluid">
          <h1 class="h3 mb-4 text-gray-800">Detail Lelang</h1>
          <div class="row">
            <div class="col">
              <div class="card border-left-primary shadow h-100 py-2">
                <div class="card-header py-3">
                  <h6 class="m-0 font-weight-bold text-primary">Data Lelang</h6>
                </div>
                <div class="card-body">
                  <div class="row no-gutters align-items-center">
                    <div class="col-md-4 text-center">
                      <img src="" alt="image" />
                    </div>
                    <div class="col mr-2">
                      <div class="row">
                        <div class="col">
                          <div
                            class="
                              text-xs
                              font-weight-bold
                              text-primary text-uppercase
                            "
                          >
                            Nama Barang
                          </div>
                          <div
                            class="h6 mb-0 font-weight-bold text-gray-800 mb-2"
                          >
                            {{ lelang.nama_barang }}
                          </div>
                          <div
                            class="
                              text-xs
                              font-weight-bold
                              text-primary text-uppercase
                            "
                          >
                            Penjual
                          </div>
                          <div
                            class="h6 mb-0 font-weight-bold text-gray-800 mb-2"
                          >
                            {{ lelang.nama_petugas }}
                          </div>
                          <div
                            class="
                              text-xs
                              font-weight-bold
                              text-primary text-uppercase
                            "
                          >
                            Tanggal Lelang
                          </div>
                          <div
                            class="h6 mb-0 font-weight-bold text-gray-800 mb-2"
                          >
                            {{ lelang.tgl_lelang | moment("DD/MM/YYYY") }}
                          </div>
                        </div>
                        <div class="col">
                          <div
                            class="
                              text-xs
                              font-weight-bold
                              text-primary text-uppercase
                            "
                          >
                            Penawar Terbesar
                          </div>
                          <div
                            class="h6 mb-0 font-weight-bold text-gray-800 mb-2"
                          >
                            {{ lelang.nama_masyarakat }}
                          </div>
                          <div
                            class="
                              text-xs
                              font-weight-bold
                              text-primary text-uppercase
                            "
                          >
                            Status
                          </div>
                          <div
                            v-if="lelang.status == 'berlangsung'"
                            class="badge bg-success text-light"
                          >
                            {{ lelang.status }}
                          </div>
                          <div
                            v-if="lelang.status == 'berhenti'"
                            class="badge bg-danger text-light"
                          >
                            {{ lelang.status }}
                          </div>
                        </div>
                      </div>
                      <div class="col-xl-8 col-md-8 mb-4">
                        <div class="card border-left-success shadow h-100 py-2">
                          <div class="card-body">
                            <div class="row no-gutters align-items-center">
                              <div class="col mr-3">
                                <div
                                  class="
                                    text-xs
                                    font-weight-bold
                                    text-primary text-uppercase
                                    mb-1
                                  "
                                >
                                  Harga Penawaran Terbesar
                                </div>
                                <div
                                  class="h5 mb-0 font-weight-bold text-gray-800"
                                >
                                  Rp. {{ lelang.harga_akhir }}
                                </div>
                              </div>
                              <div class="col-auto">
                                <i
                                  class="fas fa-dollar-sign fa-2x text-gray-300"
                                ></i>
                              </div>
                            </div>
                          </div>
                        </div>
                      </div>
                    </div>
                  </div>
                </div>
              </div>
            </div>
          </div>
          <div class="row">
            <div class="col">
              <div class="card shadow mb-4">
                <div class="card-header py-3">
                  <h6 class="m-0 font-weight-bold text-primary">
                    History Lelang
                  </h6>
                </div>
                <div class="card-body">
                  <table class="table">
                    <thead>
                      <tr>
                        <th>#</th>
                        <th>Peserta (Pembeli)</th>
                        <th>Besar Penawaran</th>
                      </tr>
                    </thead>
                    <tbody>
                      <tr v-for="(h, index) in history" :key="index">
                        <td>{{ index + 1 }}</td>
                        <td>{{ h.nama_masyarakat }}</td>
                        <td>{{ h.penawaran_harga }}</td>
                      </tr>
                    </tbody>
                  </table>
                  <button
                    type="submit"
                    class="btn btn-success mr-3"
                    @click="ubahStatus"
                  >
                    Tutup Lelang
                  </button>
                  <router-link
                    class="btn btn-primary"
                    v-if="lelang.status != 'berlangsung'"
                    :to="{ name: 'tambahtransaksi', params: { id: id_lelang } }"
                    >Tambah Transaksi</router-link
                  >
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <footer-component></footer-component>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      id_lelang: this.$route.params.id,
      lelang: {},
      history: {},
      maxhistory: {
        harga_akhir: "",
      },
    };
  },
  computed: {},
  created() {
    var data = JSON.parse(this.$store.state.datauser);
    var level = data.level;

    if (level == "admin") {
      this.$swal("Error", "Anda tidak dapat mengakses halam ini", "error");
      this.$router.push("/");
    } else if (level == "masyarakat") {
      this.$swal("Error", "Anda tidak dapat mengakses halam ini", "error");
      this.$router.push("/dashboard");
    }

    this.axios
      .get(
        `http://localhost/lelangOn/public/api/lelang/${this.$route.params.id}`
      )
      .then((res) => {
        this.lelang = res.data;
      })
      .catch((err) => console.log(err));

    this.axios
      .get(
        `http://localhost/lelangOn/public/api/hlelang/id/${this.$route.params.id}`
      )
      .then((res) => {
        this.history = res.data;
      })
      .catch((err) => console.log(err));

    this.axios
      .get(
        `http://localhost/lelangOn/public/api/hlelang/max/${this.$route.params.id}`
      )
      .then((res) => {
        console.log(res.data);
        this.maxhistory.harga_akhir = res.data;
      })
      .catch((err) => console.log(err));

    // this.axios
    //   .put(
    //     `http://localhost/lelangOn/public/api/lelang/hargamasyarakat/${this.$route.params.id}`,
    //     this.maxhistory
    //   )
    //   .then(() => {
    //     this.$route.go();
    //   })
    //   .catch((err) => console.log(err));
  },
  methods: {
    ubahStatus() {
      if (this.lelang.status == "berlangsung") {
        this.axios
          .put(
            `http://localhost/lelangOn/public/api/lelang/status/${this.$route.params.id}`
          )
          .then(() => {
            this.$router.go();
          })
          .catch((err) => console.log(err));
      }
    },
  },
};
</script>