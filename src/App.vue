<template>
  <v-app id="inspire">
    <v-app-bar app>
      <v-app-bar-nav-icon @click="drawer = !drawer"></v-app-bar-nav-icon>

      <v-toolbar-title>PLATTFORM</v-toolbar-title>
    </v-app-bar>

    <v-navigation-drawer
      v-model="drawer"
      fixed
      temporary
    >
      <!--  -->
    </v-navigation-drawer>

    <v-main class="grey lighten-2">
      <v-container>
        <v-row>
          <template>
            <Biblioteca/>
          </template>
        </v-row>
      </v-container>
    </v-main>
  </v-app>
</template>

<script>

  import Biblioteca from './components/Biblioteca/Biblioteca.vue'

  export default {
    name:"App",
    components: {
      Biblioteca
    },
    data: () => ({ drawer: null }),
  }
</script>