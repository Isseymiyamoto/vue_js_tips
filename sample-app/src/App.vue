<template>
  <v-app>
    <v-app-bar app>
      <v-app-bar-nav-icon @click.stop="openSideMenu"></v-app-bar-nav-icon>
      <v-toolbar-title class="headerline text-uppercase"></v-toolbar-title>
      <span>マイアドレス帳</span>
      <v-spacer></v-spacer>
    </v-app-bar>
    <SideNav />

    <v-main></v-main>
  </v-app>
</template>

<script>
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
    openSideMenu() {
      this.$store.dispatch("toggleSideMenu");
    }
  }
};
</script>
