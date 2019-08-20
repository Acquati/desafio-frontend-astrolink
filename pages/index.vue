<template>
  <v-layout justify-center>
    <v-flex xs12 md8 lg6>
      <v-text-field
        v-model="searchText"
        label="Search Users"
        @keyup.enter="fetchResults"
      >
        <template slot="append">
          <v-icon v-if="hasText" @click="clearSearchText">mdi-close</v-icon>
        </template>

        <template slot="append-outer">
          <v-icon @click="fetchResults">mdi-magnify</v-icon>
        </template>
      </v-text-field>

      <p>{{ feedback }}</p>

      <v-card v-for="result in results" :key="result.id" class="mb-2">
        <v-card-actions class="pb-0">
          <v-list-item class="pl-2">
            <v-list-item-avatar color="white">
              <v-img
                :alt="result.login + ' GitHub Avatar'"
                class="elevation-6"
                :src="result.avatar_url"
              ></v-img>
            </v-list-item-avatar>

            <v-list-item-content>
              <v-list-item-title>{{ result.login }}</v-list-item-title>
            </v-list-item-content>
          </v-list-item>
        </v-card-actions>

        <v-card-actions class="pt-0">
          <v-list-item>
            <v-row>
              <span class="subheading mr-2">
                Score: <v-chip>{{ result.score }}</v-chip>
              </span>
            </v-row>

            <v-row justify="end">
              <v-btn :to="'/' + result.login" outlined>
                <v-icon class="mr-1">mdi-account-card-details</v-icon>Profile
              </v-btn>
            </v-row>
          </v-list-item>
        </v-card-actions>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
import _ from 'lodash'

export default {
  data() {
    return {
      searchText: '',
      results: [],
      feedback: 'Search GitHub Users.'
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
