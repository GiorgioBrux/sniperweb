<template>
  <v-row justify="center">
    <v-col cols="12" sm="8" md="6">
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
                Uptime: {{ uptimetime }}
              </v-card-title>
            </v-card>
            <v-card flat>
              <v-card-title class="justify-center">
                Accounts: {{ con.accounts ? con.accounts : "-"}}
              </v-card-title>
            </v-card>

            <v-card flat>
              <v-card-title class="justify-center">
                Servers: {{ con.servers ? con.servers : "-"}}
              </v-card-title>
            </v-card>
          </v-col>
          <v-col class="align-center justify-space-around d-flex">
            <v-card outlined tile>
              <v-btn color="green" :loading="loading1"
                     :disabled="con.status !== status.STOPPED && con.status !== status.RESTARTING || loading1 || loading2 || loading3 "
                     @click="loader = 'loading1'">
                Start
                <v-icon right>
                  fas fa-play
                </v-icon>
              </v-btn>
            </v-card>
            <v-card outlined tile>
              <v-btn color="red" :loading="loading2"
                     :disabled="con.status !== status.RUNNING || loading1 || loading2 || loading3"
                     @click="loader = 'loading2'">
                Stop
                <v-icon right>
                  fas fa-stop
                </v-icon>
              </v-btn>
            </v-card>
            <v-card outlined tile>
              <v-btn color="blue" :loading="loading3"
                     :disabled="con.status !== status.RUNNING || loading1 || loading2 || loading3"
                     @click="loader = 'loading3'">
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
import moment from 'moment'
import momentDurationFormatSetup from 'moment-duration-format'

momentDurationFormatSetup(moment);
const delay = ms => new Promise(resolve => setTimeout(resolve, ms))

export default {
  data() {
    return {
      status: {
        STARTING: "Starting",
        RUNNING: "Running",
        RESTARTING: "Restarting",
        STOPPED: "Stopped"
      },
      con: {
        status: "???",
        uptime: null,
        accounts: "???",
        servers: "???",
      },
      homesocket: null,
      uptimemoment: null,
      actualuptime: null,
      loader: null,
      loading1: false,
      loading2: false,
      loading3: false
    };
  },
  computed: {
    uptimetime(){
      if(this.uptimemoment !== null && !isNaN(this.uptimemoment))
        return this.uptimemoment.format('d[d] h[h] m[m] s[s]');
      return "-";
    }
  },
  watch: {
    async loader() {
      const l = this.loader;
      this[l] = !this[l];

      switch (l) {
        case "loading1":
          await this.sendaction("start");
          await this.waituntilstatus(this.status.RUNNING);
          break;
        case "loading2":
          await this.sendaction("stop");
          await this.waituntilstatus(this.status.STOPPED);
          break;
        case "loading3":
          await this.sendaction("restart");
          await this.waituntilstatus(this.status.STARTING);
          await this.waituntilstatus(this.status.RUNNING);
          break;
      }
      this[l] = false;
      this.loader = null;
    }
  },
  mounted() {
    setInterval(() => {
      const now = moment.utc();
      this.uptimemoment = moment.duration(now.diff(this.con.uptime));
    }, 1000);
  },
  created() {
    this.homesocket = this.$nuxtSocket({
      name: "home",
      reconnectionAttempts: Number.MAX_VALUE,
      reconnectionDelay: 1000,
      reconnectionDelayMax: 2500,
      reconnection: true,
      teardown: false
    });

    this.homesocket.on("change", (data) => {
      this.con = data;
    });
  },
  methods: {
    async sendaction(action) {
      await this.homesocket.emit("sniper", action);
    },
    async waituntilstatus(status) {
      const readyListener = async () => {
        if (this.con.status !== status){
          await delay(250);
          await readyListener();
        }
        else return 1;
      };
      await readyListener();
    }
  }
};
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
