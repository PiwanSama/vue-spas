<template>
	<div>
		<nav class="site-nav navbar navbar-expand bg-primary navbar-dark">
			<div class="container-fluid">
				<router-link class="navbar-brand" to="/">Meeting Log</router-link>
				<div class="navbar-nav ml-auto">
					<router-link class="nav-item nav-link" to="/meetings"
                    v-if="user"
						>Meetings</router-link
					>
					<router-link class="nav-item nav-link" to="/login" v-if="!user">Login</router-link>
					<router-link class="nav-item nav-link" to="/register" v-if="!user"
						>Register</router-link
					>
                    <button class="nav-item nav-link btn btn-link" @click="$emit('logout')">Log Out</button>
				</div>
			</div>
		</nav>
	</div>
</template>

<script>
export default {
	name: "Navigation",
	props: ["user"],
};
</script>
