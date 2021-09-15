<template>
  <v-row justify="center">
    <v-col cols="12" sm="8" md="16">
      <v-card>
        <v-col>
          <v-card flat>
            <v-card-title class="logo justify-center align-center">
              Updater
            </v-card-title>
          </v-card>
        </v-col>
        <v-card flat class="py-4 d-block d-xl-flex">
          <v-col>
            <v-expansion-panels v-model="panel" multiple flat>
              <v-expansion-panel>
                <v-expansion-panel-header>
                  <div>
                    <v-icon left> fas fa-cog </v-icon>
                    Controller
                  </div>
                  <template #actions class="mx-16">
                    <div class="mx-2 green--text">
                      {{
                        needupdate.controller === true
                          ? updatetext.available
                          : needupdate.controller === false
                          ? updatetext.uptodate
                          : ''
                      }}
                    </div>
                    <v-spacer> </v-spacer>
                    <v-icon large> fas fa-angle-down </v-icon>
                  </template>
                </v-expansion-panel-header>
                <v-expansion-panel-content>
                  <div>
                    The controller is the heart of the program. <br />
                    It handles the communication between the sniper and the
                    website. <br />
                    An update to this component will likely enable new website
                    and sniper features.
                  </div>
                </v-expansion-panel-content>
              </v-expansion-panel>
              <v-expansion-panel>
                <v-expansion-panel-header>
                  <div>
                    <v-icon left> fas fa-cloud </v-icon>
                    Website
                  </div>
                  <template #actions class="mx-16">
                    <div class="mx-2 green--text">
                      {{
                        needupdate.website === true
                          ? updatetext.available
                          : needupdate.website === false
                          ? updatetext.uptodate
                          : ''
                      }}
                    </div>
                    <v-spacer> </v-spacer>
                    <v-icon large> fas fa-angle-down </v-icon>
                  </template>
                </v-expansion-panel-header>
                <v-expansion-panel-content>
                  <div>
                    The website is what you're using right now. <br />
                    An update to this component will likely make the website
                    better. Yay!
                  </div>
                </v-expansion-panel-content>
              </v-expansion-panel>
              <v-expansion-panel>
                <v-expansion-panel-header>
                  <div>
                    <v-icon left> fab fa-discord </v-icon>
                    Sniper
                  </div>
                  <template #actions class="mx-16">
                    <div class="mx-2 green--text">
                      {{
                        needupdate.sniper === true
                          ? updatetext.available
                          : needupdate.sniper === false
                          ? updatetext.uptodate
                          : ''
                      }}
                    </div>
                    <v-spacer> </v-spacer>
                    <v-icon large> fas fa-angle-down </v-icon>
                  </template>
                </v-expansion-panel-header>
                <v-expansion-panel-content>
                  <div>
                    The sniper is what is actually used to listen to messages,
                    for giveaways, for invites, etc. <br />
                    An update to this component will likely enable new settings
                    or fix issues.
                  </div>
                </v-expansion-panel-content>
              </v-expansion-panel>
            </v-expansion-panels>
          </v-col>

          <v-col class="align-center justify-space-around d-flex">
            <v-card outlined tile>
              <v-btn
                color="blue"
                :loading="check"
                style="width: 180px"
                :disabled="check || update"
                @click="loader = 'check'"
              >
                Check updates
                <v-icon right> fas fa-sync </v-icon>
              </v-btn>
            </v-card>
            <v-btn
              color="green"
              :loading="update"
              style="width: 180px"
              :disabled="
                check ||
                update ||
                (!needupdate.sniper &&
                  !needupdate.website &&
                  !needupdate.controller)
              "
              @click="loader = 'update'"
            >
              Update all
              <v-icon right> fas fa-arrow-circle-down </v-icon>
            </v-btn>
          </v-col>
        </v-card>
      </v-card>
    </v-col>
  </v-row>
</template>

<script>
const delay = (ms) => new Promise((resolve) => setTimeout(resolve, ms))

export default {
  data() {
    return {
      updatetext: {
        available: 'Update available!',
        uptodate: 'Up-to-date',
      },
      needupdate: {
        controller: null,
        website: null,
        sniper: null,
      },
      panel: [],
      homesocket: null,
      loader: null,
      check: null,
      update: null,
    }
  },
  watch: {
    async loader() {
      const l = this.loader
      this[l] = !this[l]

      switch (l) {
        case 'check':
          this.needupdate.controller = null
          this.needupdate.website = null
          this.needupdate.sniper = null
          await delay(200)
          await this.checkupdates()
          await delay(500)
          await this.waituntilupdate()
          break
        case 'update':
          await this.updatesocket.emit('updateall')
          await delay(4000)
          location.reload()
          break
      }
      this[l] = false
      this.loader = null
    },
  },
  created() {
    this.updatesocket = this.$nuxtSocket({
      name: 'home',
      reconnectionAttempts: Number.MAX_VALUE,
      reconnectionDelay: 1000,
      reconnectionDelayMax: 2500,
      reconnection: true,
      teardown: false,
    })

    this.updatesocket.on('updateinfo', (data) => {
      this.needupdate = data
      let i = 0
      for (const property in data) {
        if (data[property] === true) this.panel.push(i)
        i++
      }
    })
  },
  methods: {
    async checkupdates() {
      await this.updatesocket.emit('checkupdates')
    },
    async waituntilupdate() {
      const readyListener = async () => {
        if (this.needupdate.controller !== null) {
          return 1
        } else {
          await delay(250)
          await readyListener()
        }
      }
      await readyListener()
    },
  },
}
</script>

<style>
@-moz-keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}

@-webkit-keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}

@-o-keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}

@keyframes loader {
  from {
    transform: rotate(0);
  }
  to {
    transform: rotate(360deg);
  }
}
</style>
