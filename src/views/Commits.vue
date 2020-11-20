<template>
  <div class="commits">
    <router-link to="/">back</router-link>

    <section class="commits" v-if="commits">
      <div class="commit" v-for="commit in commits" :key="commit.node_id">
        <p>author: {{ commit.commit.author.name }}</p>
        <p>email: {{ commit.commit.author.email }}</p>
        <p>message: {{ commit.commit.message }}</p>
        <a :href="commit.commit.url" target="_blank">link</a>
      </div>
    </section>

    <section class="collaborators" v-if="contributors">
        <div class="collaborator" v-for="con in contributors" :key="con.id">
            <p>name: {{ con.login }}</p>
            <img :src="con.avatar_url" :alt="con.login">
            <router-link :to="{name: 'User', params: {userId: con.id}}">Profil</router-link>
        </div>
    </section>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      commits: [],
      contributors: []
    };
  },
  methods: {
    async fetchCommits() {
      const repoOwner = this.$route.params.repoOwner;
      const repoName = this.$route.params.repoName;
      try {
        const { data } = await axios.get(
          `https://api.github.com/repos/${repoOwner}/${repoName}/commits?sha=master`
        );
        this.commits = data;
        console.log(data);

      
      } catch (err) {
        console.log(err);
      }
    },
    async fetchContributors() {
        const repoOwner = this.$route.params.repoOwner;
        const repoName = this.$route.params.repoName;
      try {
        const { data } = await axios.get(`https://api.github.com/repos/${repoOwner}/${repoName}/contributors?per_page=5` );
        this.contributors = data;
        console.log('contributors',data);
      } catch (err) {
        console.log(err);
      }
    },
  },
  created() {
    this.fetchCommits();
    this.fetchContributors();
  },
};
</script>

<style></style>
