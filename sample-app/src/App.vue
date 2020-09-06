<template>
  <v-app>
    <v-app-bar app>
      <v-app-bar-nav-icon @click.stop="toggleSideMenu"></v-app-bar-nav-icon>
      <v-toolbar-title class="headerline text-uppercase"></v-toolbar-title>
      <span>マイアドレス帳</span>
      <v-spacer></v-spacer>
    </v-app-bar>
    <SideNav />

    <v-main>
      <v-container fluid fill-height align-start>
        <router-view />
      </v-container>
    </v-main>
  </v-app>
</template>

<script>
import { mapActions } from "vuex";
import SideNav from "./components/SideNav";
export default {
  name: "App",
  components: {
    SideNav
  },
  data: () => ({
    //
  }),
  methods: {
    ...mapActions(["toggleSideMenu"])
  }
};
</script>
