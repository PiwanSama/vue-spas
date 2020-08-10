<template>
	<div id="app">
		<Navigation/>
		<router-view 
		 class="container"
		 :user="user"
		 :meetings = "meetings"
		 @logout="logout"
		 @addMeeting="addMeeting" 
		/>
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
			meetings:[]
		};
	},
	methods: {
		logout: function() {
			firebase
				.auth()
				.signOut()
				.then(() => {
					this.user = null;
					this.$router, push("login");
				});
		},
		addMeeting: function(payload){
			db.collection("users")
			.doc(this.user.uid)
			.collection("meetings")
			.add({
				name:payload,
				createdAt:firebase.firestore.FieldValue.serverTimestamp()
			})
		}
	},
	mounted() {
		firebase.auth().onAuthStateChanged((user) => {
			if (user) {
				this.user = user;
				
				db.collection("users")
				.doc(this.user.uid)
				.collection("meetings")
				.orderBy("name")
				.onSnapshot(snapshot => {
					const snapData = [];
					snapshot.forEach(doc => {
						snapData.push({
							id: doc.id,
							name: doc.data().name
						});
					});

					this.meetings = snapData;
				});
			}
		});
	},
	components: {
		Navigation
	},
};
</script>
<style lang="scss">
$primary: #172a5a;
@import "node_modules/bootstrap/scss/bootstrap";
</style>
