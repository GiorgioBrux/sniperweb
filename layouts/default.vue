<template>
  <v-app dark>
    <v-navigation-drawer
      v-model="drawer"
      :mini-variant="miniVariant"
      :clipped="clipped"
      :expand-on-hover="$vuetify.breakpoint.mdAndUp"
      fixed
      app
    >
      <v-list>
        <v-list-item
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
          router
          exact
        >
          <v-list-item-action>
            <v-icon>{{ item.icon }}</v-icon>
          </v-list-item-action>
          <v-list-item-content>
            <v-list-item-title v-text="item.title" />
          </v-list-item-content>
        </v-list-item>
      </v-list>
    </v-navigation-drawer>
    <v-app-bar
      :clipped-left="clipped"
      fixed
      app
    >
      <v-app-bar-nav-icon @click.stop="drawer = !drawer" />
      <v-toolbar-title v-text="title" />

      <v-spacer></v-spacer>
      <v-tooltip bottom>
        <template #activator="{ on, attrs }">
          <v-btn icon>
            <v-icon :color=hearbeatcolor v-bind="attrs"
                    v-on="on">
              fas fa-heartbeat
            </v-icon>
          </v-btn>
        </template>
        <span>{{ hearbeatcolor==="red" ? "I can't connect to the backend :(" : "I'm connected to the backend! yay :)" }}</span>
      </v-tooltip>
      <v-toolbar-title class="red--text" v-text="hearbeatcolor==='red' ? 'Disconnected from backend' : ''" />
    </v-app-bar>
    <v-main>
      <v-container>
        <Nuxt v-if="hearbeatcolor !== 'red'" />
        <div v-else>
          <v-card height="200">

          <v-layout align-center justify-center column fill-height>
            <v-flex row align-center>
              <v-icon x-large color="white">
                  fas fa-skull-crossbones
              </v-icon>
            </v-flex>
            <v-flex row align-center>
              <h2 class="red--text">You are either offline or the backend isn't working.</h2> <br>
            </v-flex>
          </v-layout>
          </v-card>
        </div>

      </v-container>
    </v-main>
    <v-footer
      fixed
      app
      :expand-on-hover="$vuetify.breakpoint.mdAndUp"
      :value="$vuetify.breakpoint.smAndDown? drawer : true"
    >
      <span>&copy; {{ new Date().getFullYear() }} <a href="https://github.com/GiorgioBrux"
                                                     target="_blank">GiorgioBrux</a> (WEB) | &copy; {{ new Date().getFullYear()
        }} <a href="https://github.com/slow" target="_blank">slow</a> (SNIPER)</span>
    </v-footer>
  </v-app>
</template>

<script>


export default {
  data() {
    return {
      clipped: false,
      drawer: false,
      fixed: false,
      items: [
        {
          icon: "fas fa-home",
          title: "Main",
          to: "/"
        },
        {
          icon: "fas fa-terminal",
          title: "Terminal",
          to: "/terminal"
        },
        {
          icon: "fas fa-cogs",
          title: "Settings",
          to: "/settings"
        },
        {
          icon: "fas fa-sync-alt",
          title: "Update",
          to: "/update"
        },
        {
          icon: "fas fa-info-circle",
          title: "Info",
          to: "/info"
        }
      ],
      miniVariant: true,
      right: false,
      rightDrawer: false,
      title: "Nitro Sniper Dashboard",
      hearbeatcolor: "red"
    };
  },
  created() {
    const home = this.$nuxtSocket({
      name: "home",
      reconnectionAttempts: Number.MAX_VALUE,
      reconnectionDelay: 1000,
      reconnectionDelayMax: 2500,
      reconnection: true,
      teardown: false
    });

    home.on("connect", () => {
      console.log("Connected!")
      this.hearbeatcolor = "green";
    });


   home.on("disconnect", () => {
      console.log("Disconnect!")
      this.hearbeatcolor = "red";
    });

  },
};
</script>

<style>
html { overflow-y: auto }
</style>
