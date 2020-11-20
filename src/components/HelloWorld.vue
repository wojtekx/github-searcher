<template>
  <div class="hello">
    <input type="radio" name="type" value="repos" v-model="type" /><span
      >Repo</span
    >
    <input type="radio" name="type" value="users" v-model="type" /><span
      >User</span
    >
    <select name="page" id="page" v-model="page">
      <option value="5">5</option>
      <option value="10">10</option>
      <option value="15">15</option>
    </select>

    <input type="text" v-model="name" />

    <button @click="fetchData">szukaj</button>

    <section class="results" v-if="type === 'users'">
      <div class="user" v-for="result in results" :key="result.id">
        <router-link :to="{name: 'User', params: {userId: result.id}}">Owner: {{result.login }}</router-link>
        <img :src="result.avatar_url" :alt="result.login" />
      </div>
    </section>
    <section class="results" v-else>
      <div class="repos" v-for="result in results" :key="result.id">
        <router-link :to="{name:'Commits', params: {repoOwner: result.owner.login, repoName: result.name} }">Name: {{result.full_name}}</router-link>
        <p>Language: {{result.language}} </p>  
        <p>Watchers count: {{result.watchers_count}} </p>  
        <p>Owner: {{result.owner.login}}</p>  
        <img :src="result.owner.avatar_url" alt="result.login" />
      </div>
    </section>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import axios from "axios";

export default Vue.extend({
  data() {
    return {
      results: [],
      name: "",
      type: "",
      repo_count: 5,
      sha:'master',
      page: 5
    };
  },
  methods: {
    async fetchData(type: String, name: String) {
      try {
        if (this.type === "repos") {
          const { data } = await axios.get(
            `https://api.github.com/search/repositories?q=${this.name}&per_page=${this.page}`
          );
          this.results = data.items;
          console.log(data);
        } else {
          const { data } = await axios.get(
            `https://api.github.com/search/users?q=${this.name}`
          );
          this.results = data.items;
          console.log(data);
        }
      } catch (err) {
        console.log(err);
      }
    },
  },
});
</script>

<style scoped lang="scss"></style>
