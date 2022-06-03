<template>
  <p>{{ selectedProfessor.name }}: {{ selectedProfessor.details }}</p>
  <div
    v-if="
      store.currentUser && store.currentUser.roles.includes('ROLE_MODERATOR')
    "
  >
    <div class="review" v-for="review in reviews" :key="review">
      <p class="title">{{ review.studentsName }}, ocjena:{{ review.score }}</p>
      <div>
        <p class="comment">{{ review.comment }}</p>
      </div>
    </div>
  </div>
  <form
    style="margin-top: 2rem"
    v-if="store.currentUser && store.currentUser.roles.includes('ROLE_ADMIN')"
    @submit="submit()"
    action="#"
    onsubmit="return false"
  >
    <p>Edit professor</p>
    <div>
      <label for="name"
        >Name {{ newName }}<br />
        <input type="text" id="name" v-model="newName" />
      </label>
    </div>
    <div>
      <label for="details"
        >Details:<br />
        <textarea
          type="text"
          id="details"
          rows="4"
          cols="50"
          v-model="newDetails"
        />
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
</template>

<script>
import axios from "axios";
import store from "@/store";

export default {
  name: "AdminView",
  data() {
    return {
      reviews: [],
      selectedProfessor: {},
      newName: "",
      newDetails: "",
      store,
      error: "",
    };
  },
  created() {
    this.selectedProfessor = JSON.parse(localStorage.getItem("selectedProf"));
    if (!this.selectedProfessor) {
      this.$router.push({ name: "list" });
    }
  },
  mounted() {
    setTimeout(() => {
      if (!this.store.currentUser) {
        this.$router.push({ name: "login" });
      } else if (
        !this.store.currentUser.roles.includes("ROLE_ADMIN") &&
        !this.store.currentUser.roles.includes("ROLE_MODERATOR")
      ) {
        this.$router.push({ name: "home" });
      }
      axios
        .get(
          "http://localhost:8080/api/review/" + this.selectedProfessor.name,
          {
            headers: {
              Authorization: "Bearer " + this.store.currentUser.accessToken,
            },
          }
        )
        .then((response) => {
          this.reviews = response.data;
        })
        .catch((error) => {
          localStorage.clear();
          this.$router.push({ name: "list" });
          this.error = error.message;
        });
    }, 200);
  },
  methods: {
    submit() {
      let newProfessor = { ...this.selectedProfessor };
      console.log(newProfessor);
      if (this.newName) {
        newProfessor.name = this.newName;
      }
      if (this.newDetails) {
        newProfessor.details = this.newDetails;
      }
      axios
        .put(
          "http://localhost:8080/api/professor/" + this.selectedProfessor.name,
          newProfessor,
          {
            headers: {
              Authorization: "Bearer " + this.store.currentUser.accessToken,
            },
          }
        )
        .then((response) => {
          localStorage.clear();
          this.$router.push({ name: "home" });
        })
        .catch((error) => {
          this.error = error.message;
        });
    },
    del() {
      axios
        .delete(
          "http://localhost:8080/api/professor/" + this.selectedProfessor.name,
          {
            headers: {
              Authorization: "Bearer " + this.store.currentUser.accessToken,
            },
          }
        )
        .then((response) => {
          localStorage.clear();
          this.$router.push({ name: "list" });
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
