<template>
  <div class="signup">
    <h1 class="mb-4">Prijava</h1>
    <div class="container">
      <div class="row">
        <div class="col-sm"></div>
        <div class="col-sm">
          <form @submit="submit()" action="#" onsubmit="return false">
            <div class="form-outline mb-4">
              <input
                v-model="email"
                type="email"
                class="form-control"
                id="exampleInputEmail1"
                aria-describedby="emailHelp"
                placeholder="Email"
                required
              />
            </div>
            <div class="form-outline mb-3">
              <input
                v-model="password"
                type="password"
                class="form-control"
                id="exampleInputPassword1"
                placeholder="Password"
                required
              />
              <p style="color: red">
                {{ error }}
              </p>
            </div>
            <button type="submit" class="btn btn-primary">Submit</button>
          </form>
        </div>
        <div class="col-sm"></div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import store from "@/store";
import { JSEncrypt } from "jsencrypt";

export default {
  name: "LoginView",
  data() {
    return {
      email: "",
      password: "",
      error: "",
      publicKey: "",
    };
  },
  mounted() {
    axios
      .get("http://localhost:8080/api/auth/refreshToken", {
        withCredentials: true,
      })
      .then((response) => {
        if (response.data.accessToken) {
          store.currentUser = response.data;
          this.$router.push({ name: "home" });
        }
      })
      .catch((error) => {
        console.log("refresh token exipered or non existant");
      });

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
    submit() {
      this.error = "";
      let encrypt = new JSEncrypt();
      encrypt.setPublicKey(this.publicKey);
      let pass = encrypt.encrypt(this.password);
      axios
        .post(
          "http://localhost:8080/api/auth/login",
          { username: this.email, password: pass },
          { withCredentials: true }
        )
        .then((response) => {
          if (response.data.accessToken) {
            store.currentUser = response.data;
            this.$router.push({ name: "home" });
          }
        })
        .catch((error) => {
          this.error = error.message;
        });
    },
  },
};
</script>
