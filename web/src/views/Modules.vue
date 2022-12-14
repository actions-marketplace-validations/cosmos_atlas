<template>
  <div class="wrapper">
    <div
      class="page-header page-header-small header-filter skew-separator skew-mini"
      style="position: absolute;"
    >
      <div class="page-header-image"></div>
    </div>

    <div
      class="main"
      style="position: relative; padding-top: 20vh; min-height: 100vh;"
    >
      <div class="container">
        <div
          class="row"
          v-if="responseData.results && responseData.results.length > 0"
        >
          <div class="col-lg-12 text-lg-left align-self-lg-left">
            <div class="py-4">
              <base-dropdown class="dropdown">
                <base-button
                  type="primary"
                  size="md"
                  class="mr-4"
                  role="button"
                  slot="title"
                  data-toggle="dropdown"
                >
                  <i class="ni ni-tablet-button d-lg-none"></i>
                  <span class="nav-link-inner--text">Sort By</span>
                </base-button>
                <div
                  class="dropdown-item"
                  v-bind:class="{ selected: orderBy['alpha'] }"
                  v-on:click="sortModules('alpha')"
                >
                  Alphabetical
                </div>
                <div
                  class="dropdown-item"
                  v-bind:class="{ selected: orderBy['updated'] }"
                  v-on:click="sortModules('updated')"
                >
                  Recently Updated
                </div>
                <div
                  class="dropdown-item"
                  v-bind:class="{ selected: orderBy['new'] }"
                  v-on:click="sortModules('new')"
                >
                  Newly Added
                </div>
                <div
                  class="dropdown-item"
                  v-bind:class="{ selected: orderBy['popular'] }"
                  v-on:click="sortModules('popular')"
                >
                  Highest Rated
                </div>
              </base-dropdown>
            </div>
          </div>
        </div>
        <div
          class="row"
          v-if="responseData.results && responseData.results.length > 0"
        >
          <div
            class="col-lg-4 col-md-6"
            v-for="mod in responseData.results"
            v-bind:key="mod.name"
          >
            <card class="card-blog" style="height: 290px;">
              <template slot="body">
                <h6 class="card-category">
                  <i class="ni ni-badge"></i> {{ mod.team }}
                </h6>
                <h5 class="card-title">
                  <router-link
                    :to="{ name: 'modules', params: { id: mod.id } }"
                  >
                    {{ mod.name }}
                  </router-link>
                </h5>
                <p class="card-description">
                  <v-clamp autoresize :max-lines="3">{{
                    mod.description
                  }}</v-clamp>
                </p>
              </template>
              <template slot="footer">
                <div class="avatar-group author">
                  <router-link
                    v-for="author in mod.authors.slice(0, 3)"
                    v-bind:key="author.name"
                    :to="{ name: 'profile', params: { name: author.name } }"
                    class="avatar avatar-sm rounded-circle"
                    :title="author.name"
                  >
                    <img alt="Image placeholder" :src="avatarPicture(author)" />
                  </router-link>
                  <p class="extra-authors" v-if="mod.authors.length > 3">
                    ...
                  </p>
                </div>
                <div class="stats stats-right">
                  <i class="fa fa-star"></i> {{ mod.stars }} ??
                  <i class="ni ni-archive-2"></i>
                  {{ latestVersion(mod.versions).version }}
                </div>
              </template>
            </card>
          </div>
        </div>
        <div
          class="row justify-content-center"
          style="padding: 30px 0 50px 0;"
          v-if="responseData.results && responseData.results.length > 0"
        >
          <base-button
            class="align-self-center"
            nativeType="submit"
            type="neutral"
            :disabled="!this.responseData.prev_uri"
            v-on:click="prevModules"
          >
            <i class="ni ni-bold-left"></i>
          </base-button>
          <base-button
            class="align-self-center"
            nativeType="submit"
            type="neutral"
            :disabled="!this.responseData.next_uri"
            v-on:click="nextModules"
          >
            <i class="ni ni-bold-right"></i>
          </base-button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { Table, TableColumn } from "element-ui";
import BaseDropdown from "@/components/BaseDropdown";
import APIClient from "../plugins/apiClient";
import VClamp from "vue-clamp";

export default {
  bodyClass: "search-results-page",
  components: {
    BaseDropdown,
    VClamp,
    [Table.name]: Table,
    [TableColumn.name]: TableColumn
  },
  watch: {
    pageURI: function() {
      this.getModules();
    }
  },
  computed: {},
  methods: {
    prevModules() {
      this.pageURI = this.responseData.prev_uri;
    },

    nextModules() {
      this.pageURI = this.responseData.next_uri;
    },

    sortModules(order) {
      switch (order) {
        case "alpha":
          Object.keys(this.orderBy).forEach(v => (this.orderBy[v] = false));
          this.orderBy["alpha"] = true;
          this.pageURI = `?page=1&limit=${this.pageSize}&order=name,id`;
          break;

        case "updated":
          Object.keys(this.orderBy).forEach(v => (this.orderBy[v] = false));
          this.orderBy["updated"] = true;
          this.pageURI = `?page=1&limit=${this.pageSize}&order=updated_at,id&reverse=true`;
          break;

        case "new":
          Object.keys(this.orderBy).forEach(v => (this.orderBy[v] = false));
          this.orderBy["new"] = true;
          this.pageURI = `?page=1&limit=${this.pageSize}&order=created_at,id&reverse=true`;
          break;

        case "popular":
          Object.keys(this.orderBy).forEach(v => (this.orderBy[v] = false));
          this.orderBy["popular"] = true;
          this.pageURI = `?page=1&limit=${this.pageSize}&order=stars,id&reverse=true`;
          break;
      }
    },

    getModules() {
      APIClient.getModules(this.pageURI)
        .then(resp => {
          this.responseData = resp;
        })
        .catch(err => {
          console.log(err);
          this.$notify({
            group: "errors",
            type: "error",
            duration: 3000,
            title: "Error",
            text: this.getResponseError(err)
          });
        });
    }
  },
  created() {
    this.getModules();
  },
  data() {
    return {
      pageSize: 9,
      pageURI: "?page=1&limit=9&order=name,id",
      responseData: {},
      orderBy: {
        alpha: true,
        updated: false,
        name: false,
        popular: false
      }
    };
  },
  beforeRouteUpdate(to, from, next) {
    next();
  }
};
</script>

<style>
div.main {
  background: url(/img/stars.d8924548.d8924548.svg) repeat top,
    linear-gradient(145.11deg, #202854 9.49%, #171b39 91.06%);
}

.stats i {
  top: 0;
}

.selected {
  background: #5064fb;
  color: white;
}

.search-results-page .main {
  margin-top: 0;
}

.card-category {
  color: #ba3fd9;
}

.avatar-group .avatar {
  background-color: white;
}

.extra-authors {
  float: right;
  padding-left: 5px;
  margin-bottom: 0%;
  font-size: 1.2rem;
}
</style>
