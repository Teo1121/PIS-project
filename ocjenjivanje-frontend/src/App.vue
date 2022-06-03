<template>
  <nav>
    <p v-if="store.currentUser">Welcome {{ store.currentUser.username }}</p>
    <router-link to="/">Home</router-link> |
    <router-link to="/list">{{
      store.currentUser && store.currentUser.roles.includes("ROLE_ADMIN")
        ? "Professor List"
        : "Review"
    }}</router-link>
    <span
      v-if="store.currentUser && store.currentUser.roles.includes('ROLE_ADMIN')"
    >
      |
    </span>
    <router-link
      v-if="store.currentUser && store.currentUser.roles.includes('ROLE_ADMIN')"
      to="/users"
      >Users</router-link
    >
    |
    <router-link v-if="!store.currentUser" to="/login">Login</router-link>
    <a href="/" v-if="store.currentUser" @click="submit()">Logout</a>
  </nav>
  <router-view />
</template>

<script>
import store from "@/store";
import axios from "axios";

export default {
  name: "App",
  data() {
    return {
      store,
    };
  },
  methods: {
    submit() {
      axios
        .post(
          "http://localhost:8080/api/auth/logout",
          {
            userId: this.store.currentUser.id,
          },
          {
            withCredentials: true,
            headers: {
              Authorization: "Bearer " + this.store.currentUser.accessToken,
            },
          }
        )
        .then((response) => {
          localStorage.clear();
          this.store.currentUser = null;
        });
    },
  },
  mounted() {
    axios
      .get("http://localhost:8080/api/auth/refreshToken", {
        withCredentials: true,
      })
      .then((response) => {
        if (response.data.accessToken) {
          store.currentUser = response.data;
        }
      })
      .catch((error) => {
        console.log("refresh token exipered or non existant");
      });
  },
};
</script>
<style lang="scss">
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: -webkit-center;
  color: #2c3e50;
}

nav {
  padding: 30px;

  a {
    font-weight: bold;
    color: #2c3e50;

    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>
