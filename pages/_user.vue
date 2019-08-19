<template>
  <v-layout v-if="isDataFetched" justify-center>
    <v-flex xs12 md10 lg8>
      <v-card light class="mb-3">
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
            <v-row>
              <v-icon class="ml-2 mr-1">mdi-account-group</v-icon>

              <v-chip class="subheading mr-2">{{ user.followers }}</v-chip>

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
                  <v-icon class="mr-1">mdi-github-face</v-icon>Profile
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
            <v-row>
              <v-icon class="mr-1">mdi-notebook</v-icon>

              <v-chip class="subheading mr-2">
                {{ user.public_repos }}
              </v-chip>

              <v-btn light @click="sortRepos">
                <v-icon class="mr-1">mdi-star</v-icon>

                <v-icon v-if="sorted">mdi-arrow-down-thick</v-icon>
                <v-icon v-if="!sorted">mdi-arrow-up-thick</v-icon>
              </v-btn>
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

        <div v-for="(repo, index) in repos" :key="repo.id">
          <v-list-item-content>
            <v-list-item>
              <div class="title">{{ repo.name }}</div>
            </v-list-item>

            <v-list-item v-if="repo.description !== null">
              <div class="body-1">{{ repo.description }}</div>
            </v-list-item>

            <v-card-actions class="cardPadding">
              <v-list-item>
                <v-row>
                  <v-icon class="mr-1">mdi-star</v-icon>

                  <v-chip class="subheading mr-2">
                    {{ repo.stargazers_count }}
                  </v-chip>
                </v-row>
              </v-list-item>
            </v-card-actions>

            <v-card-actions class="cardPadding">
              <v-list-item>
                <v-row v-if="repo.language !== null">
                  <v-icon class="mr-1">mdi-file-code</v-icon>

                  <div class="subtitle-1">{{ repo.language }}</div>
                </v-row>

                <v-row justify="end">
                  <a
                    :href="repo.html_url"
                    target="_blank"
                    :title="repo.name + ' GitHub Repositorie'"
                    style="text-decoration: none;"
                  >
                    <v-btn light class="mr-1">
                      <v-icon class="mr-1">mdi-github-face</v-icon>Repositorie
                    </v-btn>
                  </a>
                </v-row>
              </v-list-item>
            </v-card-actions>
          </v-list-item-content>

          <v-divider
            v-if="index + 1 !== user.public_repos"
            class="mx-4"
          ></v-divider>
        </div>
      </v-card>
    </v-flex>
  </v-layout>
</template>

<script>
import _ from 'lodash'

export default {
  data() {
    return {
      user: {},
      repos: [],
      feedback: '',
      isDataFetched: false,
      sorted: true
    }
  },
  mounted() {
    this.$axios
      .$get('https://api.github.com/users/' + this.$route.params.user)
      .then((result) => {
        this.user = result
      })
      .catch((error) => {
        this.feedback = error
      })

    this.$axios
      .$get(
        'https://api.github.com/users/' + this.$route.params.user + '/repos'
      )
      .then((result) => {
        result = _.orderBy(result, 'stargazers_count', 'desc')
        this.repos = result
      })
      .catch((error) => {
        this.feedback = error
      })

    this.isDataFetched = true
  },
  methods: {
    sortRepos() {
      if (this.sorted) {
        this.repos = _.orderBy(this.repos, 'stargazers_count', 'asc')
      } else {
        this.repos = _.orderBy(this.repos, 'stargazers_count', 'desc')
      }

      this.sorted = !this.sorted
    }
  }
}
</script>

<style lang="scss">
.cardPadding {
  padding: 0px 8px;
}
</style>
