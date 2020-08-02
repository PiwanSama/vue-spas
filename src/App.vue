<template>
  <div id="app">
    <Navigation />
    <router-view class="container" :user="user" />
  </div>
</template>

<script>
// @ is an alias to /src
import Navigation from "@/components/Navigation.vue";
import db from "./db.js";
//import firebase from "firebase";

export default {
  name: "app",
  data: function() {
    return {
      user: null
    };
  },
  mounted() {
    db.collection("users")
      .doc("AUTrb0BUjcmmlakjtgvc")
      .get()
      .then(snapshot => {
        this.user = snapshot.data().name;
      });
  },
  components: {
    Navigation
  }
};
</script>
<style lang="scss">
$primary: #172a5a;
@import "node_modules/bootstrap/scss/bootstrap";
</style>
