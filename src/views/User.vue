<template>
  <div class="user">
    <div class="row">
      <div class="col-12 mb-5 ">
        <router-link class="btn btn-secondary" to="/">Go back</router-link>
      </div>

      <b-card
        :title="result.login"
        :img-src="result.avatar_url"
        :img-alt="result.login"
        img-top
        tag="article"
        style=""
        class="mb-5 col-12 col-md-3 repo"
      >
        <b-card-text>
          <p v-if="result.bio">Bio: {{ result.bio }}</p>
          <p>Followers: {{ result.followers }}</p>
          <a
            v-if="result.blog"
            :href="result.blog"
            target="_blank"
            class="btn btn-primary"
            >Blog</a
          >
        </b-card-text>
      </b-card>
      <div class="col-12 col-md-9"></div>

      <div class="col-12 col-md-2">
        <b-form-group
          id="input-group-3"
          label="Item count"
          label-for="per_page"
        >
          <b-form-select
            id="per_page"
            v-model="per_page"
            :options="pageOptions"
          ></b-form-select>
        </b-form-group>
      </div>

      <div class="col-12 col-md-10"></div>

      <section class="repos row" v-if="repos">
        <b-card
          :title="repo.name"
          tag="article"
          style=""
          class="mb-5 col-12 col-md-3 repo"
          v-for="repo in repos"
          :key="repo.id"
        >
          <b-card-text>
            <p>
              Description:
              <span>
                {{ repo.description }}
              </span>
            </p>
            <p>
              Language:
              <span>
                {{ repo.language }}
              </span>
            </p>
            <p>
              Default branch:
              <span>
                {{ repo.default_branch }}
              </span>
            </p>
            <a :href="repo.html_url" target="_blank" class="btn btn-primary">
              LINK
            </a>
          </b-card-text>
        </b-card>
      </section>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue, Watch, Emit } from "vue-property-decorator";

import axios from "axios";

type OrderOptions = "desc" | "asc";
type Opt<V = number> = { text: string; value: V };
@Component
export default class User extends Vue {
  private result: Object = {};
  private repos: any[] = [];
  private per_page: number = 4;
  private pageOptions: Opt[] = [
    { text: "4", value: 4 },
    { text: "8", value: 8 },
    { text: "12", value: 12 },
    { text: "16", value: 16 },
  ];
  @Watch("per_page")
  perPageChanged(): void {
    this.fetchUser();
  }

  @Emit()
  async fetchUser(): Promise<void> {
    const userId = this.$route.params.userId;
    if (!userId) this.$router.push({ path: "/" });
    try {
      const { data } = await axios.get(`https://api.github.com/user/${userId}`);
      this.result = data;
      console.log(data);

      this.fetchRepos(data.repos_url);
    } catch (err) {
      console.log(err);
    }
  }

  @Emit()
  async fetchRepos(url: string): Promise<void> {
    try {
      const { data } = await axios.get(url, {
        params: { per_page: this.per_page },
      });
      this.repos = data;
      console.log(data);
    } catch (err) {
      console.log(err);
    }
  }
  @Emit()
  created() {
    this.fetchUser();
  }
}
</script>

<style scoped lang="scss">
.user {
  padding: 20px 50px;
  #input-group-3 {
    color: #fff;
  }

  .repos {
    ::v-deep .card.repo {
      background-color: transparent;
      color: #fff;
      .card-body {
        .card-text {
          p {
            font-weight: bold;
            span {
              font-weight: normal;
              color: #8c8a80;
            }
          }
        }
      }
    }
  }
}
</style>
