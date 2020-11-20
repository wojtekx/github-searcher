<template>
  <div class="user">
    <router-link to="/">back</router-link>
    <p>Owner: {{ result.login }}</p>
    <p v-if="result.bio">Bio: {{ result.bio }}</p>
    <p>Followers: {{ result.followers }}</p>
    <a v-if="result.blog" :href="result.blog" class="btn btn-primary">Blog</a>
    <img :src="result.avatar_url" :alt="result.login" />

    <section class="repos" v-if="repos">
      <div class="repo" v-for="repo in repos" :key="repo.id">
        <p>name: {{ repo.name }}</p>
        <p>description: {{ repo.description }}</p>
        <p>language: {{ repo.language }}</p>
        <p>default branch:{{ repo.default_branch }}</p>
        <a :href="repo.git_url" target="_blank">link</a>
      </div>
    </section>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      result: {},
      repos: [],
    };
  },
  methods: {
    async fetchUser() {
      const userId = this.$route.params.userId;
      try {
        const { data } = await axios.get(
          `https://api.github.com/user/${userId}`
        );
        this.result = data;
        console.log(data);

        this.fetchRepos(data.repos_url);
      } catch (err) {
        console.log(err);
      }
    },
    async fetchRepos(url) {
      try {
        const { data } = await axios.get(url, { params: { per_page: "10" } });
        this.repos = data;
        console.log(data);
      } catch (err) {
        console.log(err);
      }
    },
  },
  created() {
    this.fetchUser();
  },
};
</script>

<style></style>
