<template>
  <p>{{ professor }}</p>
  <div class="review" v-for="review in reviews" :key="review">
    <p class="title">{{ review.studentsName }}, ocjena:{{ review.score }}</p>
    <div>
      <p class="comment">{{ review.comment }}</p>
    </div>
  </div>
  <form
    style="margin-top: 2rem"
    v-if="store.currentUser && store.currentUser.roles.includes('ROLE_USER')"
    @submit="submit()"
    action="#"
    onsubmit="return false"
  >
    <div>
      <label for="score"
        >Score: {{ score }}<br />
        <input
          type="range"
          class="form-range"
          min="1"
          max="5"
          id="score"
          v-model="score"
        />
      </label>
    </div>
    <div>
      <label for="comment"
        >Comment:<br />
        <textarea
          type="text"
          id="comment"
          rows="4"
          cols="50"
          v-model="comment"
        />
      </label>
    </div>
    <p style="color: red">
      {{ error }}
    </p>
    <div>
      <label for="anon" style="margin-right: 2rem"
        >Anonymous:
        <input type="checkbox" id="anon" v-model="anonymous" />
      </label>
      <button type="submit" class="btn btn-primary">Submit</button>
    </div>
  </form>
</template>

<script>
import axios from "axios";
import store from "@/store";

export default {
  name: "Review",
  data() {
    return {
      reviews: [],
      professor: "",
      score: "3",
      comment: "",
      anonymous: false,
      store,
      error: "",
    };
  },
  mounted() {
    this.professor = JSON.parse(localStorage.getItem("selectedProf")).name;
    if (!this.professor) {
      this.$router.push({ name: "list" });
    }
    setTimeout(() => {
      if (!this.store.currentUser) {
        return;
      }
      axios
        .get("http://localhost:8080/api/review/" + this.professor, {
          headers: {
            Authorization: "Bearer " + this.store.currentUser.accessToken,
          },
        })
        .then((response) => {
          this.reviews = response.data;
        })
        .catch((error) => {
          localStorage.removeItem("selectedProf");
          this.$router.push({ name: "list" });
          this.error = error.message;
        });
    }, 200);
  },
  methods: {
    submit() {
      this.error = "";
      if (this.store.currentUser.roles.includes("ROLE_ADMIN")) {
        this.error = "Admins shouldn't post reviews";
        return;
      }
      axios
        .post(
          "http://localhost:8080/api/review",
          {
            professorsName: this.professor,
            studentsName: this.store.currentUser.username,
            score: parseInt(this.score),
            anonymous: this.anonymous,
            comment: this.comment,
          },
          {
            headers: {
              Authorization: "Bearer " + this.store.currentUser.accessToken,
            },
          }
        )
        .then((response) => {
          this.$router.push({ name: "home" });
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
