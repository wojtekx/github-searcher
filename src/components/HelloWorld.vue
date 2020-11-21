<template>
  <div class="hello">
    <div class="container">
      <b-form-group label="Github searcher">
        <b-form-radio v-model="type" name="type" value="repos"
          >Repositories</b-form-radio
        >
        <b-form-radio v-model="type" name="type" value="users"
          >Users</b-form-radio
        >
      </b-form-group>

      <div class="row">
        <div class="col-12 mb-3">
          <form @submit.prevent="fetchData" class="form">
            <b-form-input v-model="name" type="text" class="searcher" />
            <button class="btn btn-primary" type="submit">Search</button>
          </form>
        </div>
        <div class="col-12 col-md-2">
          <b-form-group
            id="input-group-3"
            label="Per page"
            label-for="per_page"
          >
            <b-form-select
              id="per_page"
              v-model="per_page"
              :options="pageOptions"
            ></b-form-select>
          </b-form-group>
        </div>
        <div class="col-12 col-md-2" v-if="type === 'repos'">
          <b-form-group id="input-group-3" label="Sort" label-for="sort">
            <b-form-select
              id="sort"
              v-model="sort"
              :options="sortOptions"
            ></b-form-select>
          </b-form-group>
        </div>
        <div class="col-12 col-md-2" v-if="type === 'repos'">
          <b-form-group id="input-group-3" label="Order" label-for="order">
            <b-form-select
              id="order"
              v-model="order"
              :options="orderOptions"
            ></b-form-select>
          </b-form-group>
        </div>
      </div>

      <div class="content" v-if="results && results.length">
        <section class="results row" v-if="isUser">
          <b-card
            title=""
            :img-src="result.avatar_url"
            :img-alt="result.login"
            img-top
            tag="article"
            style=""
            class="mb-3 col-12 col-md-3 user"
            v-for="result in results"
            :key="result.id"
          >
            <b-card-text>
              <router-link
                class="card-title"
                :to="{ name: 'User', params: { userId: result.id } }"
              >
                {{ result.login }}
              </router-link>
            </b-card-text>
          </b-card>
        </section>
        <section class="results row" v-else>
          <b-card
            title=""
            :img-src="result.owner.avatar_url"
            :img-alt="result.full_name"
            img-top
            tag="article"
            style=""
            class="mb-5 col-12 col-md-3 repo"
            v-for="result in results"
            :key="result.id"
          >
            <b-card-text>
              <p>Language: {{ result.language }}</p>
              <p>Watchers count: {{ result.watchers_count }}</p>
              <p>Owner: {{ result.owner.login }}</p>
              <router-link
                class="card-title"
                :to="{
                  name: 'Commits',
                  params: {
                    repoOwner: result.owner.login,
                    repoName: result.name,
                  },
                }"
              >
                {{ result.full_name }}
              </router-link>
            </b-card-text>
          </b-card>
        </section>
        <b-pagination
          align="center"
          v-model="page"
          :total-rows="total_count"
          :per-page="per_page"
          aria-controls="my-table"
        ></b-pagination>
      </div>
      <div class="content" v-else>
        <h5 class="empty">{{ empty }}</h5>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { Component, Prop, Vue } from "vue-property-decorator";
import axios from "axios";

export default Vue.extend({
  data() {
    return {
      results: [],
      isUser: true,
      name: "",
      type: "repos",
      page: 1,
      per_page: 4,
      pageOptions: [
        { text: "4", value: 4 },
        { text: "8", value: 8 },
        { text: "12", value: 12 },
        { text: "16", value: 16 },
      ],
      sort: "best-match",
      sortOptions: [
        { text: "Best Match", value: "best-match" },
        { text: "Stars", value: "stars" },
        { text: "Forks", value: "forks" },
      ],
      order: "desc",
      orderOptions: [
        { text: "desc", value: "desc" },
        { text: "asc", value: "asc" },
      ],
      empty: "",
      total_count: 0,
    };
  },
  watch: {
    page(val) {
      this.fetchData(this.type, this.name);
    },
    per_page(val) {
      this.fetchData(this.type, this.name);
    },
    sort(val) {
      this.fetchData(this.type, this.name);
    },
    order(val) {
      this.fetchData(this.type, this.name);
    },
  },
  methods: {
    async fetchData(type: String, name: String) {
      if (this.name.length) {
        try {
          if (this.type === "repos") {
            this.isUser = false;
            const { data } = await axios.get(
              `https://api.github.com/search/repositories?q=${this.name}&per_page=${this.per_page}&sort=${this.sort}&direction=${this.order}&page=${this.page}`
            );
            this.results = data.items;
            this.total_count = data.total_count;

            if (!data.items.length) this.empty = "Not found";
          } else {
            this.isUser = true;
            const { data } = await axios.get(
              `https://api.github.com/search/users?q=${this.name}&per_page=${this.per_page}&sort=${this.sort}&direction=${this.order}&page=${this.page}`
            );
            this.results = data.items;
            this.total_count = data.total_count;
            if (!data.items.length) this.empty = "Not found";
          }
        } catch (err) {
          console.log(err);
        }
      } else {
        return;
      }
    },
  },
});
</script>

<style scoped lang="scss">
.hello {
  padding-top: 50px;
  min-height: 100vh;

  color: white;
  ::v-deep .form-group {
    legend {
      font-size: 30px;
      text-align: center;
      color: gold;
    }
    .bv-no-focus-ring {
      display: flex;
      justify-content: center;
      align-items: flex-start;
      .custom-radio {
        margin: 0 10px;
      }
    }
  }
  .form {
    display: flex;
    justify-content: center;
    .searcher {
      max-width: 300px;
    }
  }
  .content {
    margin: 80px 0 0;
    .results {
      .user {
        display: flex;
        flex-direction: row;
        align-items: center;
        background-color: transparent;

        .card-body {
          padding: 5px;
          max-width: 80%;
        }
        .card-img-top {
          max-width: 30%;
          border-radius: 50%;
        }
        .card-title,
        .card-text {
          color: gold;
          font-weight: bold;
          font-size: 20px;
          margin-left: 5px;
          text-overflow: ellipsis;
          white-space: nowrap;
          overflow: hidden;
        }
      }
      .repo {
        background-color: transparent;
        .card-img-top {
          max-width: 50%;
        }
        .card-body {
          padding: 10px 0;
          max-width: 80%;
        }
        .card-text {
          max-width: 100%;
          text-overflow: ellipsis;
          white-space: nowrap;
          overflow: hidden;
          p {
            margin-bottom: 2px;
            text-overflow: ellipsis;
            white-space: nowrap;
            overflow: hidden;
          }
          .card-title {
            color: gold;
          }
        }
      }
    }
    ::v-deep .pagination {
      margin-top: 50px;
      margin-bottom: 0;
      padding-bottom: 40px;
      .page-item {
        &.active {
          .page-link {
            color: gold;
          }
        }
        .page-link {
          background-color: transparent;
          border-color: transparent;
          color: #fff;
          font-size: 28px;
        }
      }
    }
    .empty {
      text-align: center;
    }
  }
}
</style>
