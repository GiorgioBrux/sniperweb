<template>
  <v-card>
    <div class="container">
      <h1 class="center"> Sniper </h1>

      <div class="row">
        <v-console style="height: 60vh" :socket="socket"/>
      </div>
    </div>
  </v-card>

</template>

<script>
// @ is an alias to /src
import Console from '~/components/console'

export default {
  name: 'Home',
  components: {
    'v-console': Console,
  },
  data() {
      return {
        socket: ''
      }
      }
  }
</script>

<style>
.container {
  overflow: hidden;
}
.row {
  overflow: hidden;
}
</style>
