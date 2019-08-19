<template>
  <v-layout v-if="isDataFetched" justify-center>
    <v-flex xs12 md10 lg8>
      <v-card light class="mb-2">
        <v-list-item three-line>
          <v-list-item-avatar size="80" color="white" tile>
            <v-img
              :alt="user.login + ' GitHub Avatar'"
              class="elevation-3"
              :src="user.avatar_url"
            ></v-img>
          </v-list-item-avatar>

          <v-list-item-content>
            <v-list-item-title class="headline mb-1">
              {{ user.name }}
            </v-list-item-title>

            <div class="subtitle-2 mb-4">{{ user.login }}</div>

            <div class="subtitle-1">{{ user.bio }}</div>
          </v-list-item-content>
        </v-list-item>

        <v-list-item v-if="user.location !== null" class="body-1">
          <v-icon class="mr-1">mdi-map-marker</v-icon> {{ user.location }}
        </v-list-item>

        <v-list-item v-if="user.email !== null" class="body-1">
          <v-icon class="mr-1">mdi-email</v-icon> {{ user.email }}
        </v-list-item>

        <v-list-item v-if="user.blog !== ''" class="body-1">
          <v-icon class="mr-1">mdi-link-variant</v-icon>
          <a
            :href="user.blog"
            target="_blank"
            :title="user.name + ' Site'"
            class="text-truncate"
          >
            {{ user.blog }}
          </a>
        </v-list-item>

        <v-card-actions>
          <v-list-item>
            <v-row align="center">
              <v-icon class="ml-2 mr-1">mdi-account-group</v-icon>
              <v-chip class="subheading mr-2">{{ user.followers }}</v-chip>
              <span class="mr-1">·</span>
              <v-icon class="mr-1">mdi-account-arrow-right</v-icon>
              <v-chip class="subheading">{{ user.following }}</v-chip>
            </v-row>
            <v-row justify="end">
              <a
                :href="user.html_url"
                target="_blank"
                :title="user.name + ' GitHub Profile'"
                style="text-decoration: none;"
              >
                <v-btn dark>
                  <v-icon class="mr-1">mdi-github-face</v-icon>GitHub
                </v-btn>
              </a>
            </v-row>
          </v-list-item>
        </v-card-actions>
      </v-card>

      <v-card>
        <v-card-title>{{ user.name }} Repositories</v-card-title>
        <v-card-actions>
          <v-list-item>
            <v-row align="center">
              <v-icon class="mr-1">mdi-notebook</v-icon>
              <v-chip class="subheading mr-2">
                {{ user.public_repos }}
              </v-chip>
            </v-row>
            <v-row justify="end">
              <a
                :href="user.html_url"
                target="_blank"
                :title="user.name + ' GitHub Repositories'"
                style="text-decoration: none;"
              >
                <v-btn light>
                  <v-icon class="mr-1">mdi-github-face</v-icon>Repositories
                </v-btn>
              </a>
            </v-row>
          </v-list-item>
        </v-card-actions>

        <v-divider></v-divider>

        <template>
          <v-card-actions>
            <v-list-item>
              <div class="title">Tonight's availability</div>
            </v-list-item>
          </v-card-actions>

          <v-divider class="mx-4"></v-divider>
        </template>
      </v-card>
      <!-- <p>
        <code>{{ repos }}</code>
      </p> -->
    </v-flex>
  </v-layout>
</template>

<script>
import _ from 'lodash'

export default {
  data() {
    return {
      searchText: '',
      user: {},
      repos: [],
      feedback: 'Search GitHub Users.',
      isDataFetched: false
    }
  },
  computed: {
    hasText() {
      return this.searchText.length
    }
  },
  watch: {
    searchText() {
      if (this.searchText !== '') {
        this.feedback = 'Waiting for GitHub API ...'
      } else {
        this.feedback = 'Search GitHub Users.'
      }
      this.debouncedFetchResults()
    }
  },
  mounted() {
    this.$axios
      .$get('https://api.github.com/users/' + this.$route.params.user)
      .then((result) => {
        console.log(result)
        this.user = result
      })
      .catch((error) => {
        console.log(error)
      })

    this.$axios
      .$get(
        'https://api.github.com/users/' + this.$route.params.user + '/repos'
      )
      .then((result) => {
        console.log(result)
        this.repos = result
      })
      .catch((error) => {
        console.log(error)
      })

    this.isDataFetched = true
  },
  created() {
    this.debouncedFetchResults = _.debounce(this.fetchResults, 1000)
  },
  methods: {
    clearSearchText() {
      this.searchText = ''
    },
    fetchResults() {
      if (this.searchText === '') return
      this.$axios
        .$get(
          'https://api.github.com/search/users?q=' +
            this.searchText +
            '+type:user'
        )
        .then((result) => {
          this.results = []
          if (result.total_count === 0) {
            this.results = []
            this.feedback = 'No users found with these words.'
          } else {
            result.items.forEach((item) => this.results.push(item))

            result.total_count === 1
              ? (this.feedback = '1 User found.')
              : (this.feedback = result.total_count + ' Users found.')
          }
        })
        .catch((error) => {
          this.feedback = 'GitHub API: ' + error
        })
    }
  }
}
</script>

<style lang="scss"></style>
