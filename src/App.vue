<template>
	<div id="app">
		<Navigation />
		<router-view class="container" 
		:user="user" @logout="logout" />
	</div>
</template>

<script>
// @ is an alias to /src
import Navigation from "@/components/Navigation.vue";
import db from "./db.js";
import firebase from "firebase";

export default {
	name: "app",
	data: function() {
		return {
			user: null,
		};
	},
	methods: {
		logout: function() {
			firebase
				.auth()
				.signOut()
				.then(() => {
					this.user = null;
					this.$router, push("/login");
				});
		},
	},
	mounted() {
		firebase.auth().onAuthStateChanged((user) => {
			if (user) {
				this.user = user.displayName;
			}
		});
	},
	components: {
		Navigation,
	},
};
</script>
<style lang="scss">
$primary: #172a5a;
@import "node_modules/bootstrap/scss/bootstrap";
</style>
