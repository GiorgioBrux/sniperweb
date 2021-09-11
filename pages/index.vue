<template>
  <v-row justify="center">
    <v-col cols="12" sm="8" md="6">
      <v-col cols="12" sm="8" md="6">

      </v-col>
      <v-card>
        <v-col>
          <v-card flat>
            <v-card-title class="logo justify-center align-center">
              <DiscordLogo />

            </v-card-title>
            <v-card-title class="logo justify-center align-center">
              {{ con.status || "Stopped" }}
            </v-card-title>
          </v-card>
        </v-col>
        <v-card flat class="py-4 d-block d-xl-flex">

          <v-col>
            <v-card flat>
              <v-card-title class="justify-center">
                Uptime: 3d 5m 1s
              </v-card-title>
            </v-card>
            <v-card flat>
              <v-card-title class="justify-center">
                Accounts: {{ con.accounts }}
              </v-card-title>
            </v-card>

            <v-card flat>
              <v-card-title class="justify-center">
                Last nitro sniped: 1
              </v-card-title>
            </v-card>
          </v-col>
          <v-col class="align-center justify-space-around d-flex">
            <v-card outlined tile>
                <v-btn color="green" :disabled="con.status !== status.STOPPED && con.status !== status.RESTARTING" >
                  Start
                  <v-icon right>
                    fas fa-play
                  </v-icon>
                </v-btn>
            </v-card>
            <v-card outlined tile>
                <v-btn color="red" :disabled="con.status !== status.RUNNING">
                  Stop
                  <v-icon right>
                    fas fa-stop
                  </v-icon>
                </v-btn>
            </v-card>
            <v-card outlined tile>
                <v-btn color="blue" :disabled="con.status !== status.RUNNING">
                  Restart
                  <v-icon right>
                    fas fa-sync-alt
                  </v-icon>
                </v-btn>
            </v-card>

          </v-col>

        </v-card>
      </v-card>


    </v-col>
  </v-row>
</template>

<script>




export default {
  data(){
    return {
      status: {
        STARTING: "Starting",
        RUNNING: "Running",
        RESTARTING: "Restarting",
        STOPPED: "Stopped",
      },
      con: {
        status: "???",
        accounts: "???"
      },
    }
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

    home.on("change", (data) => {
      this.con = data;
    });
  }
}
</script>

