<template>
	<div id="app">
		<Navigation/>
		<router-view 
		 class="container"
		 :user="user"
		 :meetings = "meetings"
		 :error = "error"
		 @logout="logout"
		 @addMeeting="addMeeting"
		 @deleteMeeting = "deleteMeeting"
		 @CheckIn="CheckIn"
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
			error:null,
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
		},
		deleteMeeting: function(payload){
			db.collection("users")
			.doc(this.user.uid)
			.collection("meetings")
			.doc(payload)
			.delete()
		},
		CheckIn: function(payload){
			db.collection("users")
			.doc(payload.userID)
			.collection("meetings")
			.doc(payload.meetingID)
			.get()
			.then(doc => {
				if(doc.exists){
					db.collection("users")
					.doc(payload.userID)
					.collection("meetings")
					.doc(payload.meetingID)
					.collection("attendees")
					.add({
						displayName:payload.displayName,
						email:payload.email,
						createdAt:firebase.firestore.FieldValue.serverTimestamp()
					})
					.then(()=>this.$router.push("/"))
				}else{
					this.error = "Sorry, this meeting doesn't exist"
				}
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
