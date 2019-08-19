<template>
  <v-app dark>
    <v-content>
      <v-container>
        <nuxt />
      </v-container>
    </v-content>
  </v-app>
</template>

<script>
export default {
  data() {
    return {}
  }
}
</script>

<style lang="scss">
.html {
  overflow-y: scroll;
}

.v-content {
  // background: linear-gradient(rgba(0, 0, 0, 0.6), rgba(0, 0, 0, 0)),
  //   url('~assets/github-logo.svg');
  background-image: url('~assets/github-logo.svg');
  background-attachment: fixed;
  background-position: center;
  background-repeat: no-repeat;
  // background-size: cover;
}
</style>
