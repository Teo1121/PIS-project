<template>
  <h1>List of users</h1>
  <div v-for="user in users" v-bind:key="user">
    <a href="#" @click="click(user)"> {{ user }} </a><br />
  </div>
  <form
    style="margin-top: 2rem"
    v-if="store.currentUser && store.currentUser.roles.includes('ROLE_ADMIN')"
    @submit="submit()"
    action="#"
    onsubmit="return false"
  >
    <p>New user</p>
    <div>
      <label for="name"
        >Name<br />
        <input type="text" id="name" v-model="newUser.username" />
      </label>
    </div>
    <div>
      <label for="email"
        >Email<br />
        <input type="email" id="email" v-model="newUser.email" />
      </label>
    </div>
    <div>
      <p>Roles:</p>
      <label for="adminRole"
        >Admin<br />
        <input
          type="checkbox"
          id="adminRole"
          v-model="newUser.role"
          value="ROLE_ADMIN"
        />
      </label>
      <label for="modRole"
        >Moderator<br />
        <input
          type="checkbox"
          id="modRole"
          v-model="newUser.role"
          value="ROLE_MODERATOR"
        />
      </label>
    </div>
    <div>
      <label for="pass"
        >Password<br />
        <input type="password" id="pass" v-model="newUser.password" />
      </label>
    </div>
    <p style="color: red">
      {{ error }}
    </p>
    <div>
      <button type="submit" class="btn btn-primary">Submit</button>
    </div>
  </form>
</template>

<script>
import axios from "axios";
import store from "@/store";
import { JSEncrypt } from "jsencrypt";

export default {
  name: "UserModView",
  data() {
    return {
      users: [],
      store,
      error: "",
      publicKey: "",
      newUser: { email: "", username: "", role: [], password: "" },
    };
  },
  mounted() {
    setTimeout(() => {
      if (!this.store.currentUser) {
        this.$router.push({ name: "login" });
      }
      if (!store.currentUser.roles.includes("ROLE_ADMIN")) {
        this.$router.push({ name: "home" });
      }
      axios
        .get("http://localhost:8080/api/user/all", {
          headers: {
            Authorization: "Bearer " + this.store.currentUser.accessToken,
          },
        })
        .then((response) => {
          this.users = response.data;
        })
        .catch((error) => {
          this.$router.push({ name: "home" });
          this.error = error.message;
        });
    }, 200);
    this.publicKey = localStorage.getItem("publicKey");
    if (this.publicKey) {
      return;
    }
    axios.get("http://localhost:8080/api/auth/key").then((response) => {
      if (response.data.message) {
        localStorage.setItem("publicKey", response.data.message);
        this.publicKey = response.data.message;
      }
    });
  },
  methods: {
    click(user) {
      if (this.store.currentUser.roles.includes("ROLE_ADMIN")) {
        //alert("not implemented");
        this.$router.push({ name: "userdetail", query: { name: user } });
      }
    },
    submit() {
      let encrypt = new JSEncrypt();
      encrypt.setPublicKey(this.publicKey);
      this.newUser.password = encrypt.encrypt(this.newUser.password);
      axios
        .post("http://localhost:8080/api/auth/signup", this.newUser, {
          headers: {
            Authorization: "Bearer " + this.store.currentUser.accessToken,
          },
        })
        .then((response) => {
          window.location.reload();
        })
        .catch((error) => {
          this.error = error.message;
        });
    },
  },
};
</script>

<style></style>

<style lang="scss">
label {
  margin-right: 0.5rem;
  margin-left: 0.5rem;
}
</style>
