<template>
  <v-app id="inspire">
    <!-- <v-system-bar  app>
      <v-spacer></v-spacer>

      <v-icon>mdi-square</v-icon>

      <v-icon>mdi-circle</v-icon>

      <v-icon>mdi-triangle</v-icon>
    </v-system-bar> -->

    <!-- <v-app-bar
      app
      clipped-right
      flat
      height="72"
    >
      <v-spacer></v-spacer>

      <v-responsive max-width="156">
        <v-text-field
          dense
          flat
          hide-details
          rounded
          solo-inverted
        ></v-text-field>
      </v-responsive>
    </v-app-bar> -->

    <v-navigation-drawer
      permanent
      absolute
      color="grey lighten-3"
      mini-variant
    >
      <v-avatar
        class="d-block text-center mx-auto mt-4"
        color="grey darken-1"
        size="36"
      ></v-avatar>

      <v-divider class="mx-3 my-5"></v-divider>

      <v-avatar class="d-flex align-center justify-center mx-auto mb-9" color="grey lighten-1" size="28">
        <router-link to="/">
          <v-btn>
            <v-icon>mdi-home</v-icon>
          </v-btn>
        </router-link>
      </v-avatar>

      <v-avatar class="d-flex align-center justify-center mx-auto mb-9" color="grey lighten-1" size="28">
        <router-link to="/cadastroProduto">
          <v-btn>
            <v-icon>mdi-plus</v-icon>
          </v-btn>
        </router-link>
      </v-avatar>  

      <v-avatar class="d-flex align-center justify-center mx-auto mb-9" color="grey lighten-1" size="28">
        <router-link to="/about">
          <v-btn>
            <v-icon>mdi-information</v-icon>
          </v-btn>
        </router-link>
      </v-avatar>
    </v-navigation-drawer>

    <v-main>
      <router-view />
    </v-main>

  </v-app>
</template>

<script>
  export default {
    data: () => ({ drawer: null }),
  }
</script>
