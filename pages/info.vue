<template>
  <v-row justify="center">
    <v-col cols="12" sm="8" md="16">
      <v-card>
        <v-card flat class="py-4 d-block d-xl-flex" >

          <v-col>
            <v-card flat>
              <div class="container">
                <h1 class="red--text"> Credits </h1>
                <br>
                <h3>GiorgioBrux - Web/Controller</h3>
                <h3>slow - Sniper</h3>
                <br>
                <br>
                <h2>Thanks to the amazing people contributing to</h2>
                <h4>discord.js</h4>
                <h4>discord.js-light</h4>
                <h4>express</h4>
                <h4>chalk</h4>
                <h4>json5</h4>
                <h4>dotenv</h4>
                <h4>nuxt.js</h4>
                <h4>vue.js</h4>
                <h4>vuetify</h4>
                <h4>socket-io</h4>
                <h4>simple-git</h4>
                <h4>moment.js</h4>
                <h4>moment-duration-format</h4>
                <h4>eslint, stylelint, commitlint, prettier, husky</h4>
                <h2>who made this possible.</h2>
              </div>
            </v-card>
          </v-col>
          <v-col class="align-center justify-space-around d-flex">
            <v-card flat>
              <v-img :src="require('../assets/thankyou.gif')" alt="Thanks!"> </v-img>
            </v-card>
          </v-col>

        </v-card>
      </v-card>


    </v-col>
  </v-row>



</template>
