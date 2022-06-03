<template>
  <div>
    <p>Username: {{ user.username }}</p>
    <p>Email: {{ user.email }}</p>
    <p>Roles: {{ user.roles.join(" ") }}</p>
    <form
      style="margin-top: 2rem"
      v-if="store.currentUser && store.currentUser.roles.includes('ROLE_ADMIN')"
      @submit="submit()"
      action="#"
      onsubmit="return false"
    >
      <p>Edit user</p>
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
        <label for="pass"
          >Password<br />
          <input type="password" id="pass" v-model="newPassword" />
        </label>
      </div>
      <div>
        <label for="pass"
          >Repeted Password<br />
          <input type="password" id="pass" v-model="repPassword" />
        </label>
      </div>
      <p style="color: red">
        {{ error }}
      </p>
      <div>
        <button type="button" @click="del()" class="btn btn-primary me-4">
          Delete
        </button>
        <button type="submit" class="btn btn-primary">Submit</button>
      </div>
    </form>
  </div>
</template>

<script>
import axios from "axios";
import store from "@/store";
import { JSEncrypt } from "jsencrypt";

export default {
  name: "UserDetailView",
  data() {
    return {
      newPassword: "",
      repPassword: "",
      user: { username: "", email: "", roles: [] },
      newUser: { username: "", email: "" },
      store,
      publicKey: "",
      error: "",
    };
  },
  mounted() {
    setTimeout(() => {
      if (!this.store.currentUser) {
        return;
      }
      axios
        .get("http://localhost:8080/api/user/" + this.$route.query.name, {
          headers: {
            Authorization: "Bearer " + this.store.currentUser.accessToken,
          },
        })
        .then((response) => {
          this.user = response.data;
        })
        .catch((error) => {
          localStorage.clear();
          //this.$router.push({ name: "list" });
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
    del() {
      axios
        .delete("http://localhost:8080/api/user/" + this.$route.query.name, {
          headers: {
            Authorization: "Bearer " + this.store.currentUser.accessToken,
          },
        })
        .then((response) => {
          this.$router.push({ name: "users" });
        })
        .catch((error) => {
          this.error = error.message;
        });
    },
    submit() {
      this.error = "";
      if (this.newPassword && this.newPassword == this.repPassword) {
        let encrypt = new JSEncrypt();
        encrypt.setPublicKey(this.publicKey);
        this.newUser.password = encrypt.encrypt(this.newPassword);
      }
      if (this.newUser.username == "") {
        this.newUser.username = this.user.username;
      }
      if (this.newUser.email == "") {
        this.newUser.email = this.user.email;
      }
      axios
        .put(
          "http://localhost:8080/api/user/" + this.$route.query.name,
          this.newUser,
          {
            headers: {
              Authorization: "Bearer " + this.store.currentUser.accessToken,
            },
          }
        )
        .then((response) => {
          this.$router.push({ name: "users" });
        })
        .catch((error) => {
          this.error = error.message;
        });
    },
  },
};
</script>

<style lang="scss">
label {
  margin-right: 0.5rem;
  margin-left: 0.5rem;
}
.review {
  width: min-content;
  margin: 1rem;
  p.title {
    margin-bottom: 0;
    text-align: left;
    width: max-content;
  }
  p.comment {
    margin: 1rem;
  }
  div {
    border: solid;
    width: max-content;
  }
}
</style>
