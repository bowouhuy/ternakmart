<template>
  <div>
    <AppBar :page="page" :updateCart="carts"/>
    <v-container class="elevation-0 mt-12 mb-12">
      <v-row v-if="carts.length != 0" dense>
        <v-col
          v-for="(item, i) in carts"
          :key="i"
          cols="12"
        >
          <v-card v-if="item.st == 'ternak'" class="mt-3">
                <v-img
                  class="white--text align-end"
                  height="250px"
                  :src="item.file_ternak" 
                >
                </v-img>
                <v-card-title>{{item.ternak_nama}}</v-card-title>
                <v-card-text> {{subStr(item.ternak_deskripsi)}}</v-card-text>
                <v-card-subtitle>
                  <v-row>
                    <v-col cols="5">Harga Ternak</v-col>
                    <v-col cols="1">:</v-col>
                    <v-col cols="6">
                      <div class="subtitle font-weight-bold" color="#FF8F0B">
                        Rp. {{ formatPrice(item.ternak_harga) }}
                      </div>
                    </v-col>
                  </v-row>
                  <v-row>
                    <v-col cols="5">Harga Pengiriman</v-col>
                    <v-col cols="1">:</v-col>
                    <v-col cols="6">
                      <div class="subtitle font-weight-bold" color="#FF8F0B">
                        Rp. {{ formatPrice(item.harga_ongkir) }}
                      </div>
                    </v-col>
                  </v-row>
                  <v-row>
                    <v-col cols="5">Masa Perawatan</v-col>
                    <v-col cols="1">:</v-col>
                    <v-col cols="6">
                      <div class="subtitle font-weight-bold" color="#FF8F0B">
                        {{(item.masa_perawatan)}} Hari
                      </div>
                    </v-col>
                  </v-row>
                  <v-row>
                    <v-col cols="5">Harga Perawatan</v-col>
                    <v-col cols="1">:</v-col>
                    <v-col cols="6">
                      <div class="subtitle font-weight-bold" color="#FF8F0B">
                        Rp. {{ formatPrice(item.perawatan_harga*item.masa_perawatan) }}
                      </div>
                    </v-col>
                  </v-row>
                </v-card-subtitle>
                <v-card-actions>
                  <div class="text-center">
                    <v-dialog
                      
                      width="500"
                    >
                      <template v-slot:activator="{ on, attrs }">
                        <v-btn
                          color="#139CA4"
                          dark
                          v-bind="attrs"
                          v-on="on"
                          class="mr-2"
                        >
                          Details
                        </v-btn>
                        <br>
                        <v-btn
                          color="orange lighten-2"
                          dark
                          :to="'payment/'+item.id"
                        >
                          Checkout
                        </v-btn>
                      </template>

                      <v-card>
                        <v-card-title class="headline grey lighten-2">
                          Detail Pesanan
                        </v-card-title>

                        <v-card-text>
                          <div class="mt-3">
                            <v-row>
                              <v-col cols="4">Nama Ternak</v-col>
                              <v-col cols="1">:</v-col>
                              <v-col cols="7">{{item.ternak_nama}}</v-col>
                            </v-row>
                            <v-row>
                              <v-col cols="4">Deskripsi</v-col>
                              <v-col cols="1">:</v-col>
                              <v-col cols="12">{{item.ternak_deskripsi}}</v-col>
                            </v-row>
                            <v-row>
                              <v-col cols="4">Harga</v-col>
                              <v-col cols="1">:</v-col>
                              <v-col cols="7">Rp. {{formatPrice(item.ternak_harga)}}</v-col>
                            </v-row>
                            <v-row>
                              <v-col cols="4">Penerima</v-col>
                              <v-col cols="1">:</v-col>
                              <v-col cols="7">{{item.nama_penerima}}</v-col>
                            </v-row>
                            <v-row>
                              <v-col cols="4">Alamat Penerima</v-col>
                              <v-col cols="1">:</v-col>
                              <v-col cols="7">{{item.detail_alamat}}, {{item.city_name}}, {{item.province}}</v-col>
                            </v-row>
                            <v-row>
                              <v-col cols="4">Catatan</v-col>
                              <v-col cols="1">:</v-col>
                              <v-col cols="7">{{item.transaksi_note}}</v-col>
                            </v-row>
                          </div>
                        </v-card-text>

                        <v-divider></v-divider>

                        <v-card-actions>
                          <v-spacer></v-spacer>
                          
                          <v-btn
                          color="orange lighten-2"
                          dark
                          :to="'payment/'+item.id"
                        >
                          Checkout
                        </v-btn>
                        </v-card-actions>
                      </v-card>
                    </v-dialog>
                  </div>
                </v-card-actions>
          </v-card>
          <v-card v-else class="mt-3">
            <v-img
                class="white--text align-end"
                height="250px"
                :src="item.file_produk" 
              >
              </v-img>
              <v-card-title>{{item.produk_nama}}</v-card-title>
              <v-card-subtitle>{{ item.qty }} {{ toUpperCase(item.produk_jenis) }}</v-card-subtitle>
              <v-card-text> {{subStr(item.produk_deskripsi)}}</v-card-text>
              <v-card-text>
                <v-row>
                  <v-col cols="5">Harga Produk</v-col>
                  <v-col cols="1">:</v-col>
                  <v-col cols="6">
                    <div class="subtitle font-weight-bold" color="#FF8F0B">
                      Rp. {{ formatPrice(item.ternak_harga) }} / {{ item.qty }} {{ toUpperCase(item.produk_jenis) }}
                    </div>
                  </v-col>
                </v-row>
                <v-row>
                  <v-col cols="5">Harga Pengiriman</v-col>
                  <v-col cols="1">:</v-col>
                  <v-col cols="6">
                    <div class="subtitle font-weight-bold" color="#FF8F0B">
                      Rp. {{ formatPrice(item.harga_ongkir) }}
                    </div>
                  </v-col>
                </v-row>
              </v-card-text>
              <v-card-actions>
                <v-btn
                color="orange lighten-2"
                dark
                :to="'payment/'+item.id"
              >
                Checkout
              </v-btn>
              </v-card-actions>
          </v-card>
        </v-col>
      </v-row>
      <v-row v-else class="mt-3">
        <v-col cols="12" class="text-center">
            <span class="subtitle font-weight-medium grey--text">Tidak ada keranjang</span> 
        </v-col>
      </v-row>
    </v-container>
  </div>
</template>

<script>

import AppBar from '@/components/AppBar.vue';
import axios from "axios";

export default {
  name: 'Cart',
  components: {
    AppBar,
    
  },
  data(){
    return{
      page: {
            link: '/',
            title: 'Keranjang',
        },
            carts: [],
    }
  },
  methods:{
    setCarts(data) {
      this.carts = data;
    },
    subStr(value) {
      return value.substring(0,100) + '...';
    },
    formatPrice(value) {
      let val = (value/1).toFixed(2).replace('.', ',')
      return val.toString().replace(/\B(?=(\d{3})+(?!\d))/g, ".")
    },
    toUpperCase(value){
        if(!value) return ''
            value = value.toString()
        return value.toUpperCase()
    },
  },

  mounted() {
    axios
      .get("transaksi/"+this.$store.state.auth.user.id+"/cart")
      .then((response) => this.setCarts(response.data.cart))
      .catch((error) => console.log(error));
  },
}
</script>
