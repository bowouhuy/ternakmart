<template>
    <v-app id="inspire">
        <v-container fluid fill-height>
            <v-layout align-center justify-center>
                <v-flex xs12 sm8 md8>
                    <v-card class="elevation-4 px-5 py-5">
                        <v-img
                        class="mx-auto"
                        position="center center"
                        src="img/brand/ternakmart.png"
                        max-height="100"
                        max-width="100"
                        ></v-img>
                        <v-card-text>
                            <div class="display-1 font-weight-bold my-3">
                            Daftar Akun Baru
                            </div>
                            <ul v-if="error" class="red--text my-3">
                                <li v-for="(v, k) in error" :key="k"> {{ v[0] }}</li>
                            </ul>
                            <v-form @submit.prevent="onSubmit" id="register">
                                <v-row>
                                    <v-col cols="12" lg="6" sm="12">
                                        <v-text-field
                                        v-model="form.nama_lengkap"
                                        color="teal darken-2"
                                        label="Nama Lengkap"
                                        required
                                        ></v-text-field>
                                        <v-select
                                        v-model="form.jenis_kelamin"
                                        item-text="text"
                                        item-value="value"
                                        :items="jenis_kelamin"
                                        color="teal"
                                        label="Jenis Kelamin"
                                        required
                                        ></v-select>
                                        <v-text-field
                                        v-model="form.nomor_hp"
                                        color="teal darken-2"
                                        label="Nomor Telepon"
                                        required
                                        ></v-text-field>
                                        <v-textarea
                                            v-model="form.alamat"
                                            color="teal darken-2"
                                            rows="2"
                                            row-height="5"
                                            >
                                            <template v-slot:label>
                                                <div>
                                                Alamat
                                                </div>
                                            </template>
                                        </v-textarea>
                                    </v-col>
                                    <v-col cols="12" lg="6" sm="12">
                                        <v-menu
                                            v-model="menu"
                                            :close-on-content-click="false"
                                            :nudge-right="40"
                                            transition="scale-transition"
                                            offset-y
                                            min-width="auto"
                                        >
                                            <template v-slot:activator="{ on, attrs }">
                                            <v-text-field
                                                v-model="form.tanggal_lahir"
                                                label="Tanggal Lahir"
                                                append-icon="mdi-calendar"
                                                readonly
                                                v-bind="attrs"
                                                v-on="on"
                                            ></v-text-field>
                                            </template>
                                            <v-date-picker
                                            v-model="form.tanggal_lahir"
                                            @input="menu = false"
                                            ></v-date-picker>
                                        </v-menu>
                                        <v-text-field
                                            append-icon="mdi-gmail"
                                            v-model="form.email"
                                            label="Email"
                                            type="text"
                                        >
                                        </v-text-field>
                                        <v-text-field
                                            v-model="form.password"
                                            :append-icon="show_password ? 'mdi-eye' : 'mdi-eye-off'"
                                            :type="show_password ? 'text' : 'password'"
                                            label="Password"
                                            @click:append="show_password = !show_password"
                                        ></v-text-field>
                                        <v-text-field
                                            v-model="form.password_confirmation"
                                            :append-icon="show_password_confirmation ? 'mdi-eye' : 'mdi-eye-off'"
                                            :type="show_password_confirmation ? 'text' : 'password'"
                                            label="Konfirmasi Password"
                                            @click:append="show_password_confirmation = !show_password_confirmation"
                                        ></v-text-field>
                                    </v-col>
                                </v-row>
                            </v-form> 
                        </v-card-text>
                        <v-card-actions>
                            <v-spacer></v-spacer>
                            <v-btn 
                            type="submit"
                            class="white--text font-weight-bold"
                            color="#139CA4"
                            block
                            form="register"
                            :disabled="isLoading"
                            >
                            <span v-if="isLoading">
                                Loading 
                                <v-progress-circular
                                :size="15"
                                indeterminate
                                color="secondary"
                                ></v-progress-circular>
                            </span>
                            <span v-else>
                                Daftar
                            </span>
                            </v-btn>
                        </v-card-actions>
                    </v-card>
                    <div class="text-center mt-3">
                        <router-link class="subtitle-2" :to="{ name: 'login' }">
                            <small>Have an account?</small>
                        </router-link>
                    </div>
                </v-flex>
            </v-layout>
            <v-dialog
            v-model="dialog"
            persistent
            max-width="290"
            >
            <v-card>
                <v-card-title class="text-h5">
                Registrasi Berhasil!
                </v-card-title>
                <v-card-text>Silahkan buka email yang baru saja didaftarkan untuk aktivasi akun anda.</v-card-text>
                <v-card-actions>
                <v-spacer></v-spacer>
                <v-btn
                    color="green darken-1"
                    text
                    @click="dialog = false"
                    :to="'login'"
                >
                    Oke
                </v-btn>
                </v-card-actions>
            </v-card>
            </v-dialog>
        </v-container>
        <v-snackbar
            v-model="snackbar"
            timeout="2000"
            :color="color"
            >
            {{ message }}
        </v-snackbar>
    </v-app>
</template>
<script>
import { mapState } from "vuex";
import ApiService from "@/common/api.service";
// import axios from "axios";

export default {
    name: "Register",
    data() {
        return {
            show_password: false,
            show_password_confirmation: false,
            name: "",
            email: "",
            password: "",
            password_confirmation: "",
            form: {},
            jenis_kelamin: [
                { 
                    text: 'Laki-laki',
                    value: 'L',
                }, 
                {
                    text: 'Perempuan',
                    value: 'P',
                }
            ],
            menu: false,
            dialog: false,
            isLoading: false,
            error:""
        }
    },
    computed: {
        ...mapState({
            errors: state => state.auth.errors
        })
    },
    methods: {
        onSubmit() {
            // this.$store
            //     .dispatch(REGISTER, {
            //         name: this.name,
            //         email: this.email,
            //         password: this.password,
            //         password_confirmation: this.password_confirmation,
            //         role_id: '3'
            //     })
            //     .then((response) => {
            //         this.$toast.success(response.message, {
            //             type: "success",
            //             position: "top-right",
            //             duration: 3000,
            //             dismissible: true,
            //         });
            //         this.$router.push({ name: "login" })
            //     });

            this.isLoading = true
            this.form.name = this.form.nama_lengkap
            this.form.role_id = '4'
            this.form.user_st = 'Tidak Aktif'
                ApiService.setHeader();
                ApiService.post("users", this.form)
                .then(() => {
                    this.dialog = true
                    this.isLoading = false
                    this.snackbar = true
                    this.message = 'Berhasil Pendaftaran'
                    this.color = '#139CA4'
                    
                })

                .catch((err) => {
                    this.error = err.response.data
                    // console.log(this.errors)
                    this.snackbar = true
                    this.message = "Terjadi Kesalahan"
                    this.color = 'red'
                    this.isLoading = false
                });
        }
    }
};
</script>
<style>
</style>
