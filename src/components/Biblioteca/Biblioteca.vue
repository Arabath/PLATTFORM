<template>
  <v-layout>
    <v-flex>
      <div style="background-color: #e9e9e9">
        <div style="padding-right: 15px; padding-left: 15px; padding-top: 0px">
          <h1>BIBLIOTECA</h1>
          <v-row>
            <v-col cols="5">
              <v-text-field
                v-model="filtro"
                label="solo"
                placeholder="Ingrese un texto a buscar"
                solo
                style="width: 100%"
                @keydown.enter="listarLibrosXFiltro()"
              ></v-text-field>
            </v-col>
            <v-col cols="5">
              <v-tooltip bottom slot="activator">
                <template v-slot:activator="{ on }">
                  <v-btn
                    v-on="on"
                    class="mr-5"
                    fab
                    small
                    depressed
                    dark
                    @click="addAudio()"
                    v-bind:color="btn_audio ? '#41d0ea' : '#ffffff'"
                  >
                    <v-icon v-bind:color="btn_audio ? '#ffffff' : '#d0d0d0'">
                      mdi-headphones
                    </v-icon>
                  </v-btn>
                </template>
                <span class="tooltip_small">{{
                  btn_audio ? "QUITAR FILTRO DE AUDIO" : "BUSCAR AUDIOS"
                }}</span>
              </v-tooltip>

              <v-tooltip bottom slot="activator">
                <template v-slot:activator="{ on }">
                  <v-btn
                    v-on="on"
                    class="mr-5"
                    fab
                    small
                    depressed
                    dark
                    @click="
                      btn_video = !btn_video;
                      listarLibrosXFiltro();
                    "
                    v-bind:color="btn_video ? '#41d0ea' : '#ffffff'"
                  >
                    <v-icon v-bind:color="btn_video ? '#ffffff' : '#d0d0d0'">
                      mdi-video
                    </v-icon>
                  </v-btn>
                </template>
                <span class="tooltip_small">{{
                  btn_video ? "QUITAR FILTRO DE VIDEOS" : "BUSCAR VIDEOS"
                }}</span>
              </v-tooltip>

              <v-tooltip bottom slot="activator">
                <template v-slot:activator="{ on }">
                  <v-btn
                    v-on="on"
                    class="mr-5"
                    fab
                    small
                    depressed
                    dark
                    @click="
                      btn_texto = !btn_texto;
                      listarLibrosXFiltro();
                    "
                    v-bind:color="btn_texto ? '#41d0ea' : '#ffffff'"
                  >
                    <v-icon v-bind:color="btn_texto ? '#ffffff' : '#d0d0d0'">
                      mdi-book-open-page-variant
                    </v-icon>
                  </v-btn>
                </template>
                <span class="tooltip_small">{{
                  btn_texto
                    ? "QUITAR FILTRO DE DOCUMENTO DE TEXTO"
                    : "BUSCAR DOCUMENTO DE TEXTO"
                }}</span>
              </v-tooltip>

              <v-tooltip bottom>
                <template v-slot:activator="{ on }">
                  <v-btn
                    :to="{ name: 'crearlibro' }"
                    class="mx-2"
                    v-on="on"
                    fab
                    dark
                    small
                    color="cyan"
                  >
                    <v-icon dark> mdi-book-plus-outline </v-icon>
                  </v-btn>
                </template>
                <span class="tooltip_small">Nuevo Libro</span>
              </v-tooltip>
            </v-col>
          </v-row>
          <div v-for="(items, index) in itemsLibros" v-bind:key="index">
            <v-hover v-slot:default="{ hover }">
              <v-card
                max-width="244"
                height:190px
                color="#ffffff"
                dark
                style="
                  margin: 10px;
                  float: left;
                  text-align: center;
                  width: 230px;
                  height: 400px;
                  padding: 0px;
                "
              >
                <v-row justify="center" style="padding: 0px">
                  <v-img
                    v-if="items.imagenPortada"
                    contain
                    max-width="230"
                    :src="appUrl + '/Datos/' + items.imagenPortada"
                  >
                    <v-expand-transition>
                      <div
                        v-if="hover"
                        class="
                          d-flex
                          transition-fast-in-fast-out
                          darken-2
                          v-card--reveal
                          display-0
                          white--text
                          pa-5
                        "
                        style="height: 100%; background-color: #2eb2b8"
                      >
                        <v-btn
                          class="mx-2"
                          fab
                          dark
                          color="indigo"
                          @click="
                            showDocument(items.id, items.tituloLibro, index)
                          "
                        >
                          <v-icon dark> mdi-menu-right </v-icon>
                        </v-btn>
                      </div>
                    </v-expand-transition>
                  </v-img>
                </v-row>

                <v-row
                  justify="center"
                  style="padding: 10px; padding-left: 25px; padding-right: 25px"
                >
                  <span style="color: #222222">{{ items.tituloLibro }}</span>
                </v-row>
              </v-card>
            </v-hover>
          </div>
        </div>
      </div>
    </v-flex>

    <v-dialog
      v-model="preview"
      fullscreen
      hide-overlay
      transition="dialog-bottom-transition"
      scrollable
    >
      <v-card>
        <v-toolbar dark:color="dlgColor">
          <v-toolbar-title>
            Previsualización del archivo ( {{ fileName }} )
          </v-toolbar-title>
          <v-spacer></v-spacer>
          <span>
            <v-tooltip bottom slot="activator">
              <template>
                <v-btn
                  v-on="onTooltip"
                  icon
                  dark
                  fab
                  depressed
                  @click="openAdjunto()"
                >
                  <v-icon>mdi-file-download-outline</v-icon>
                </v-btn>
              </template>
              <span class="tooltip">Descargar archivo adjunto</span>
            </v-tooltip>
          </span>
          <v-btn icon dark fab depressed @click="preview = false">
            <v-icon>mdi-close</v-icon>
          </v-btn>
        </v-toolbar>
        <iframe width="100%" height="100%" style="border: 2px" :src="html" />
      </v-card>
    </v-dialog>

    <!-- Ver Documento -->

    <v-dialog max-width="90%" v-model="dlgDocumento">
      <v-card>
        <v-toolbar color="primary" dark>
          <span class="headline_dlg_white">{{ titulo }}</span>
        </v-toolbar>

        <v-card-text class="pt-3">
          <v-row justify="center" style="padding: 0px">
            <v-col cols="auto">
              <v-img
                v-if="itemSel > -1"
                width="230"
                height="320"
                :src="appURL + '/Datos/' + itemsLibros[itemSel].imagenPortada"
              >
              </v-img>
            </v-col>

            <v-col cols="5">
              <v-row>
                <v-col v-if="itemSel > -1">
                  <b>DESCRIPCION: </b>
                  {{ itemsLibros[itemSel].descripcionLibro }}
                </v-col>
              </v-row>

              <v-row>
                <v-col v-if="itemSel > -1">
                  <b>TEMA : </b>{{ itemsLibros[itemSel].tema }}
                </v-col>
              </v-row>

              <v-row>
                <v-col v-if="itemSel > -1">
                  <b>AUTOR : </b>{{ itemsLibros[itemSel].autor }}
                </v-col>
              </v-row>

              <v-row>
                <v-col v-if="itemSel > -1">
                  <b>EDITORIAL : </b>{{ itemsLibros[itemSel].editorial }}
                </v-col>
              </v-row>

              <v-row>
                <v-col v-if="itemSel > -1">
                  <b>ISBN : </b>{{ itemsLibros[itemSel].isbn }}
                </v-col>
              </v-row>
            </v-col>

            <v-col cols="4">
              <v-card>
                <v-list rounded>
                  <v-subheader>ARCHIVOS ADJUNTOS</v-subheader>
                  <v-list-item-group
                    v-model="selectedItem"
                    color="primary"
                    style="height: 300px; overflow-y: auto"
                  >
                    <v-list-item
                      v-for="(itemAdjunto, indexAdjuntos) in arrayAdjuntos"
                      v-bind:key="indexAdjuntos"
                    >
                      <v-list-item-icon>
                        <v-icon>{{ iconType(itemAdjunto.tipoArchivo) }}</v-icon>
                      </v-list-item-icon>
                      <v-list-item-content>
                        <v-list-item-title
                          v-text="itemAdjunto.nombreArchivo"
                          @click="
                            showPreview(
                              itemAdjunto.nombreArchivo,
                              itemAdjunto.nombreArchivoReal,
                              itemAdjunto.tipoArchivo
                            )
                          "
                        ></v-list-item-title>
                      </v-list-item-content>
                    </v-list-item>
                  </v-list-item-group>
                </v-list>
              </v-card>
            </v-col>
          </v-row>
        </v-card-text>
      </v-card>
    </v-dialog>

    <!-- ALERT -->
    <template v-if="alertDlg">
      <Alerts
        :alertColor="alertColor"
        :alertMessage="alertMessage"
        :snackbar="snackbar"
      />
    </template>
    <!-- ALERT -->

    <!-- DIALOGOS -->
  </v-layout>
</template>

<script>
import axios from 'axios'
import moment from "moment"

import Alerts from '@/components/public/Alerts.vue'
import '../../animate.min.css'
import '../../App.css'
import Editor from '@tinymce/tinymce-vue'


export default {
  name: "Biblioteca",
  components: {
    Alerts,
    editor: Editor
  },
  data() {
    return {
      alertDlg: false,
      take: 100,
      btn_audio: true,
      btn_video: true,
      btn_texto: true,
      filtro: null,
      dlgDocumento: false,
      titulo: "",
      arrayAdjuntos: [],
      itemsLibros: [],
      preview: false,
      html: "https://www.plattform.com.ar/loading.html",
      dlgColor: "",
      fileName: "",
      realName: "",
      fileType: "",
      itemSel: -1,
      selectedItem: "",
    };
  },


  watch: {
    alumnosMain: function () {
        if(this.alumnosMain == true) {
            this.barraBuscador = false;
        }
    },
  },

  computed: {
    usuarioID() {
        if(this.$store.state.hijoID) {
            console.log("Desde Padre")
            return this.$store.state.hijoID
        }
        console.log("Desde Hijo")
        return this.$store.state.usuario.idusuario
    },
    ultimodiaanio() {
        var anio = new Date().getFullYear()
        var finanio = anio + "-12-31 00:00:00"
        return moment(finanio).format("DD-MM-YYYY")
    },
    fechaHoraActual() {
        var mFecha = new Date()
        return moment(mFecha).format("YYYYMMDDHHmm")
    },
    institucion(){
        return this.$store.state.usuario.institucion
    },
    appURL(){
        return axios.defaults.baseURL
    }
  },

  created() {
    this.listarLibrosXFiltro()
  },

  methods: {
    moment,

    getImgUrl(pic) {
        try{
            return require("@/assets" + pic)
        } catch(error){
            return require('@/assets/desconocido.svg')
        }
    },

    listarLibrosXFiltro() {
        if(this.filtro == "") {
            this.filtro = null
        }
        if(
            this.filtro != null &&
            (this.filtro.includes("?") || 
            this.filtro.includes("#") || 
            this.filtro.includes("/"))
        ){
            this.filtro = null
        }
        this.itemsLibros = []
        let me = this
        let header = { Authorization: "Bearer " + this.$store.state.token } 
        let configuracion = { headers:header }
        me.actividadesUsuario = true
        me.cargando_msg = "Cargando Libros..."
        console.log(me.itemsLibros.length)
        axios
            .get(
                "api/Biblioteca" +
                me.institucion +
                "," +
                me.filtro +
                "," +
                me.btn_audio +
                "," +
                me.btn_texto +
                "," +
                me.btn_video +
                "/ListaBibliotecaXFiltro",
                configuracion
            )
            .then(function(response) {
                me.itemsLibros = response.data
                me.cargando_msg = ""
                console.log(me.itemsLibros)
            })
            .catch(function(error){
                if(error.response && error.response.status === 401) {
                    me.salir()
                } else {
                    // Handle error however you want
                }
            })
         
    },

    

  }

};
</script>

<style>
</style>