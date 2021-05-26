<template>
  <v-app>
    <!-- Preloader -->
    <div class="loader-wrapper">
      <div class="loader"></div>
    </div>
    <!-- Preloader end -->
    <nav-bar></nav-bar>
    <vue-progress-bar></vue-progress-bar>
    <section>
    <div id="main_div">
      <center>
      <v-container fluid style="width: 1200px;">
      <Bridge></Bridge>
      </v-container>
      </center>
    <Map></Map>
    <Footer></Footer>
    <js></js>
    </div>
    </section>
  </v-app>
</template>

<script>
import Bridge from "./components/bridge";
import Map from "./components/map";
import Footer from "./components/footer";
import NavBar from "./components/navbar";
import js from '@/components/js';

export default {
  name: "App",
  components: {
    Bridge,
    NavBar,
    Footer,
    Map,
    js,
  },
  data() {
    return {
      uploadApplication: false,
    };
  },
  methods: {
    showUploadApplication() {
      this.uploadApplication = true;
    },
    hideUploadApplication() {
      this.uploadApplication = false;
    },
  },
};
</script>

