<template>
  <v-layout>
    <v-flex>
      <!-- <v-card>
        <v-card-title class="headline">
          Welcome to the Vuetify + Nuxt.js template
        </v-card-title>
        <v-card-text>
          <p>
            Vuetify is a progressive Material Design component framework for
            Vue.js. It was designed to empower developers to create amazing
            applications.
          </p>
          <p>
            For more information on Vuetify, check out the
            <a href="https://vuetifyjs.com" target="_blank"> documentation </a>.
          </p>
          <p>
            If you have questions, please join the official
            <a href="https://chat.vuetifyjs.com/" target="_blank" title="chat">
              discord </a
            >.
          </p>
          <p>
            Find a bug? Report it on the github
            <a
              href="https://github.com/vuetifyjs/vuetify/issues"
              target="_blank"
              title="contribute"
            >
              issue board </a
            >.
          </p>
          <p>
            Thank you for developing with Vuetify and I look forward to bringing
            more exciting features in the future.
          </p>
          <div class="text-xs-right">
            <em><small>&mdash; John Leider</small></em>
          </div>
          <hr class="my-3" />
          <a href="https://nuxtjs.org/" target="_blank">
            Nuxt Documentation
          </a>
          <br />
          <a href="https://github.com/nuxt/nuxt.js" target="_blank">
            Nuxt GitHub
          </a>
        </v-card-text>
        <v-card-actions>
          <v-spacer />
          <v-btn color="primary" nuxt to="/inspire">
            Continue
          </v-btn>
        </v-card-actions>
      </v-card> -->
      <v-text-field
        v-model="searchText"
        label="Search Users"
        @keyup.enter="fetchResults"
      >
        <!-- <template slot="append">
          <v-icon v-if="hasText" @click="clearButton">clear</v-icon>
        </template>
        <template slot="append-outer">
          <v-icon @click="fetchResults">search</v-icon>
        </template> -->
      </v-text-field>
      <p>{{ feedback }}</p>
      <p>
        <code>{{ results }}</code>
      </p>
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
    },
    hasResults() {
      return this.results.length
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
    this.debouncedFetchResults = _.debounce(this.fetchResults, 500)
  },
  mounted() {
    // this.$axios
    //   .$get('https://api.github.com/search/users?q=leandro+acquati+type:user')
    //   .then((result) => {
    //     console.log(result)
    //   })
    //   .catch((error) => {
    //     console.log(error)
    //   })
  },
  methods: {
    clearButton() {
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
