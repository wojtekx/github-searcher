<template>
  <div class="commits">
    <div class="row">
      <div class="col-12 mb-5">
        <router-link class="btn btn-secondary" to="/">Go back</router-link>
      </div>
      <div class="col-12 col-md-2">
        <b-form-group id="input-group-3" label="Item count" label-for="per_page">
          <b-form-select
            id="per_page"
            v-model="per_page"
            :options="pageOptions"
          ></b-form-select>
        </b-form-group>
      </div>
    </div>
    <b-tabs content-class="mt-5" fill align="center">
      <b-tab title="Commits" active>
        <section class="row" v-if="commits">
          <b-card
            :title="commit.commit.author.name"
            :img-src="commit.author.avatar_url"
            :img-alt="commit.commit.author.name"
            img-top
            tag="article"
            style=""
            class="mb-5 col-12  commit"
            v-for="commit in commits"
            :key="commit.node_id"
          >
            <b-card-text>
              <!-- <p>author: {{ commit.commit.author.name }}</p> -->
              <a :href="'mailto:' + commit.commit.author.email">{{
                commit.commit.author.email
              }}</a>
              <p>
                <br />message: <br />
                {{ commit.commit.message }}
              </p>
            </b-card-text>
          </b-card>
        </section>
        <h5 v-else>Not found</h5>
      </b-tab>
      <b-tab title="Contributors">
        <section class="collaborators row" v-if="contributors">
          <b-card
            :title="con.login"
            :img-src="con.avatar_url"
            :img-alt="con.login"
            img-top
            tag="article"
            style=""
            class="mb-5 col-12 col-md-3  commit"
            v-for="con in contributors"
            :key="con.id"
          >
            <b-card-text>
              <router-link :to="{ name: 'User', params: { userId: con.id } }"
                >Profil</router-link
              >
            </b-card-text>
          </b-card>
        </section>
        <h5 v-else>Not found</h5>
      </b-tab>
    </b-tabs>
  </div>
</template>

<script>
import axios from "axios";
export default {
  data() {
    return {
      commits: [],
      contributors: [],
      per_page: 4,
      pageOptions: [
        { text: "4", value: 4 },
        { text: "8", value: 8 },
        { text: "12", value: 12 },
        { text: "16", value: 16 },
      ],
      order: "desc",
      orderOptions: [
        { text: "desc", value: "desc" },
        { text: "asc", value: "asc" },
      ],
    };
  },
  watch: {
    per_page() {
      this.fetchCommits();
      this.fetchContributors();
    },
  },
  methods: {
    async fetchCommits() {
      const repoOwner = this.$route.params.repoOwner;
      const repoName = this.$route.params.repoName;
      if (!repoOwner || !repoName) this.$router.push({ path: "/" });
      try {
        const { data } = await axios.get(
          `https://api.github.com/repos/${repoOwner}/${repoName}/commits?sha=master&per_page=${this.per_page}`
        );
        this.commits = data;
        
      } catch (err) {
        console.log(err);
      }
    },
    async fetchContributors() {
      const repoOwner = this.$route.params.repoOwner;
      const repoName = this.$route.params.repoName;
      if (!repoOwner || !repoName) this.$router.push({ path: "/" });
      try {
        const { data } = await axios.get(
          `https://api.github.com/repos/${repoOwner}/${repoName}/contributors?per_page=${this.per_page}`
        );
        this.contributors = data;

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

<style scoped lang="scss">
.commits {
  padding: 20px 50px;

  label {
    color: #fff;
  }

  .commit {
    display: flex;
    flex-direction: column;
    align-items: flex-start;
    background-color: transparent;

    .card-body {
      padding: 5px;
      max-width: 80%;
    }
    .card-img-top {
      max-width: 100px;
      border-radius: 50%;
    }
    .card-title,
    .card-text {
      color: #fff;
      font-weight: bold;
      font-size: 15px;
      margin-left: 5px;

      p {
        margin-bottom: 2px;
      }
    }
    .card-title {
      font-size: 18px;
    }
    .btn {
      margin-top: 20px;
    }
  }
  ::v-deep .nav-tabs {
    margin: 0 auto;
    width: 50%;
    min-width: 300px;
    .nav-item {
      a {
        color: #fff;
        background-color: transparent;
      }
    }
  }
}
</style>
