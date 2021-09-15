<template>
  <v-row justify="center">
    <v-col cols="12" sm="8" md="16">
      <v-card>
        <v-col>
          <v-card flat>
            <v-card-title class="logo justify-center align-center">
              Settings
            </v-card-title>
          </v-card>
        </v-col>
        <v-card flat class="py-4 d-block d-xl-flex">
          <v-col>
            <v-expansion-panels flat accordion>
              <v-expansion-panel>
                <v-expansion-panel-header>
                  <div>
                    <v-icon left> fab fa-discord </v-icon>
                    Sniper
                  </div>
                  <template #actions class="mx-16">
                    <v-spacer></v-spacer>
                    <v-icon large> fas fa-angle-down </v-icon>
                  </template>
                </v-expansion-panel-header>
                <v-expansion-panel-content>
                  <v-expansion-panels flat accordion popout>
                    <v-expansion-panel>
                      <v-expansion-panel-header>
                        <div>
                          <v-icon left> fas fa-font </v-icon>
                          Tokens
                        </div>
                        <template #actions class="mx-16">
                          <v-spacer></v-spacer>
                          <v-icon large> fas fa-angle-down </v-icon>
                        </template>
                      </v-expansion-panel-header>
                      <v-expansion-panel-content>
                        <v-text-field
                          v-model="settings.tokens.main"
                          :rules="[
                            (value) =>
                              value.length > 10 || 'This is not a valid token',
                          ]"
                          label="Main token"
                          required
                          hint="Click <a href='https://github.com/Tyrrrz/DiscordChatExporter/wiki/Troubleshooting#i-cant-findcopy-my-token' target='_blank'>here</a> to learn how to get tokens."
                          persistent-hint
                        >
                          <template #message="{ message }">
                            <span v-html="message"></span>
                          </template>
                        </v-text-field>
                        <br />
                        Alt tokens
                        <div
                          v-for="(item, index) in settings.tokens.alts"
                          :key="index"
                        >
                          <v-text-field
                            v-model="settings.tokens.alts[index]"
                            :label="`Alt token n.${index + 1}`"
                            :rules="[
                              (value) =>
                                (value ? value.length > 10 : false) ||
                                'This is not a valid token',
                            ]"
                            required
                          >
                            <template #append>
                              <v-icon @click="deletealt(index)">
                                fas fa-times</v-icon
                              >
                            </template>
                          </v-text-field>
                        </div>
                        <br />
                        <v-btn color="orange" @click="addalt()">
                          Add new alt token
                          <v-icon right> fas fa-plus </v-icon>
                        </v-btn>
                        <br />
                        <br />
                        <v-select
                          v-model="settings.mode"
                          :items="[
                            {
                              show: 'Only main',
                              value: 'main',
                            },
                            {
                              show: 'Only alts',
                              value: 'alts',
                            },
                            {
                              show: 'All accounts',
                              value: 'both',
                            },
                          ]"
                          item-text="show"
                          item-value="value"
                          label="Snipe from"
                          hint="Note: Nitro will always be redeemed on main."
                          persistent-hint
                        ></v-select>
                        <br />
                        <v-divider></v-divider>
                        <br />
                        <div>
                          <v-select
                            v-model="settings.status.main"
                            :items="[
                              {
                                show: 'Default',
                                value: 'default',
                              },
                              {
                                show: 'Online',
                                value: 'online',
                              },
                              {
                                show: 'Do not disturb',
                                value: 'dnd',
                              },
                              {
                                show: 'Idle',
                                value: 'idle',
                              },
                              {
                                show: 'Offline/invisible',
                                value: 'offline',
                              },
                            ]"
                            item-text="show"
                            item-value="value"
                            hint="`Sniping from` needs to be set to `All accounts` or `Main only` for this to work."
                            :persistent-hint="settings.mode === 'alts'"
                            :disabled="settings.mode === 'alts'"
                            label="Main status"
                          ></v-select>
                          <br />
                          <v-select
                            v-model="settings.status.alts"
                            :items="[
                              {
                                show: 'Default',
                                value: 'default',
                              },
                              {
                                show: 'Online',
                                value: 'online',
                              },
                              {
                                show: 'Do not disturb',
                                value: 'dnd',
                              },
                              {
                                show: 'Idle',
                                value: 'idle',
                              },
                              {
                                show: 'Offline/invisible',
                                value: 'offline',
                              },
                            ]"
                            item-text="show"
                            item-value="value"
                            label="Alts status"
                          ></v-select>
                        </div>
                      </v-expansion-panel-content>
                    </v-expansion-panel>
                    <v-expansion-panel>
                      <v-expansion-panel-header>
                        <div>
                          <v-icon left> fas fa-cogs </v-icon>
                          Modes
                        </div>
                        <template #actions class="mx-16">
                          <v-spacer></v-spacer>
                          <v-icon large> fas fa-angle-down </v-icon>
                        </template>
                      </v-expansion-panel-header>
                      <v-expansion-panel-content>
                        Nitro
                        <v-slider
                          v-model="settings.nitro.max"
                          label="Nitro before cooldown"
                          append-icon="fas fa-gift"
                          thumb-label
                          :thumb-size="30"
                          min="0"
                          max="20"
                          hint="Isn't that too much? Just saying..."
                          :persistent-hint="settings.nitro.max > 10"
                        >
                        </v-slider>
                        <v-slider
                          v-model="settings.nitro.cooldown"
                          label="Cooldown duration (hours)"
                          append-icon="fas fa-clock"
                          thumb-label
                          :thumb-size="30"
                          min="12"
                          max="200"
                          hint="That's more than a week. Are you sure?"
                          :persistent-hint="settings.nitro.cooldown > 168"
                        >
                        </v-slider>
                        <br />
                        <v-divider></v-divider>
                        <br />
                        Giveaway sniper (Incomplete settings)
                        <v-switch
                          v-model="settings.giveaway.enabled"
                          label="Enable"
                          color="red"
                        ></v-switch>
                        <div v-if="settings.giveaway.enabled">
                          <v-slider
                            v-model="settings.giveaway.delay"
                            label="Giveaway delay (seconds)"
                            append-icon="fas fa-hourglass"
                            thumb-label
                            :thumb-size="30"
                            min="5"
                            max="250"
                          >
                          </v-slider>
                          <v-switch
                            v-model="settings.giveaway.dm"
                            label="DM on win?"
                          >
                          </v-switch>
                          <v-text-field
                            v-if="settings.giveaway.dm"
                            v-model="settings.giveaway.dmMessage"
                            label="Message to DM the host"
                            counter="4000"
                          >
                          </v-text-field>
                        </div>
                        <v-divider></v-divider>
                        <br />
                        Invite sniper
                        <v-switch
                          v-model="settings.invite.enabled"
                          label="Enable"
                          color="red"
                        ></v-switch>
                        <div v-if="settings.invite.enabled">
                          <v-switch
                            v-model="settings.invite.onlyAlts"
                            label="Only alts?"
                          >
                          </v-switch>
                          <v-slider
                            v-model="settings.invite.cooldown"
                            label="Cooldown duration (hours)"
                            append-icon="fas fa-clock"
                            thumb-label
                            :thumb-size="30"
                            min="2"
                            max="200"
                            hint="That's more than a week. Are you sure?"
                            :persistent-hint="settings.invite.cooldown > 168"
                          >
                          </v-slider>
                          <v-slider
                            v-model="settings.invite.cooldown"
                            label="Invites before cooldown"
                            append-icon="fas fa-envelope"
                            thumb-label
                            :thumb-size="30"
                            min="2"
                            max="30"
                          >
                          </v-slider>
                          <br />
                          <v-slider
                            v-model="settings.invite.delay.min"
                            label="Minimum delay to join server (seconds)"
                            thumb-label
                            :thumb-size="30"
                            :rules="[
                              (values) =>
                                values < settings.invite.delay.max ||
                                'Minimum cooldown can\'t be higher than maximum',
                            ]"
                            min="2"
                            max="199"
                          >
                          </v-slider>
                          <v-slider
                            v-model="settings.invite.delay.max"
                            label="Maximum delay to join server (seconds)"
                            thumb-label
                            :thumb-size="30"
                            :rules="[
                              (values) =>
                                values > settings.invite.delay.min ||
                                'Maximum cooldown can\'t be lower than minimum',
                            ]"
                            min="3"
                            max="200"
                          >
                          </v-slider>
                          <br />
                          <v-slider
                            v-model="settings.invite.members.min"
                            label="Minimum members to join invite"
                            thumb-label
                            append-icon="fas fa-users"
                            :thumb-size="30"
                            :rules="[
                              (values) =>
                                values < settings.invite.members.max ||
                                'Minimum members can\'t be higher than maximum',
                            ]"
                            min="1"
                            max="700000"
                          >
                          </v-slider>
                          <v-slider
                            v-model="settings.invite.members.max"
                            label="Maximum members to join invite"
                            append-icon="fas fa-users"
                            thumb-label
                            :thumb-size="30"
                            :rules="[
                              (values) =>
                                values > settings.invite.members.min ||
                                'Maximum members can\'t be lower than minimum',
                            ]"
                            min="1"
                            max="700000"
                          >
                          </v-slider>
                        </div>
                      </v-expansion-panel-content>
                    </v-expansion-panel>
                    <v-expansion-panel>
                      <v-expansion-panel-header>
                        <div>
                          <v-icon left> fas fa-comment </v-icon>
                          Webhooks
                        </div>
                        <template #actions class="mx-16">
                          <v-spacer></v-spacer>
                          <v-icon large> fas fa-angle-down </v-icon>
                        </template>
                      </v-expansion-panel-header>
                      <v-expansion-panel-content>
                        <v-switch
                          v-model="settings.webhook.genericenable"
                          label="Enable"
                          color="red"
                        >
                        </v-switch>
                        <div v-if="settings.webhook.genericenable">
                          <v-text-field
                            v-model="settings.webhook.url"
                            :disabled="!settings.webhook.genericenable"
                            :rules="[
                              (value) =>
                                value.includes('discord.com/api/webhooks/') ||
                                'This is not a valid webhook url',
                            ]"
                            label="Webhook url"
                            required
                            hint="Click <a href='https://discordjs.guide/popular-topics/webhooks.html#creating-webhooks' target='_blank'>here</a> to learn how to get this."
                            persistent-hint
                          >
                            <template #message="{ message }">
                              <span v-html="message"></span>
                            </template>
                          </v-text-field>
                          <br />
                          <v-divider></v-divider>
                          <div>
                            <v-switch
                              v-model="settings.webhook.enabled.codeInvalid"
                              color="primary"
                              label="Fire webhook on invalid code"
                            >
                            </v-switch>
                            <v-switch
                              v-model="
                                settings.webhook.mentionEveryone.codeInvalid
                              "
                              :disabled="!settings.webhook.enabled.codeInvalid"
                              color="secondary"
                              label="Mention everyone on invalid code"
                            >
                            </v-switch>
                          </div>
                          <br />
                          <v-divider></v-divider>
                          <div>
                            <v-switch
                              v-model="
                                settings.webhook.enabled.codeAlreadyRedeemed
                              "
                              color="primary"
                              label="Fire webhook on already redeemed code"
                            >
                            </v-switch>
                            <v-switch
                              v-model="
                                settings.webhook.mentionEveryone
                                  .codeAlreadyRedeemed
                              "
                              :disabled="
                                !settings.webhook.enabled.codeAlreadyRedeemed
                              "
                              color="secondary"
                              label="Mention everyone on already redeemed code"
                            >
                            </v-switch>
                          </div>
                          <br />
                          <v-divider></v-divider>
                          <div>
                            <v-switch
                              v-model="settings.webhook.enabled.codeSuccess"
                              color="primary"
                              label="Fire webhook on sniped code"
                            >
                            </v-switch>
                            <v-switch
                              v-model="
                                settings.webhook.mentionEveryone.codeSuccess
                              "
                              :disabled="!settings.webhook.enabled.codeSuccess"
                              color="secondary"
                              label="Mention everyone on sniped code"
                            >
                            </v-switch>
                          </div>
                          <br />
                          <v-divider></v-divider>
                          <div>
                            <v-switch
                              v-model="settings.webhook.enabled.giveawayEntered"
                              color="primary"
                              label="Fire webhook on giveaway enter"
                            >
                            </v-switch>
                            <v-switch
                              v-model="
                                settings.webhook.mentionEveryone.giveawayEntered
                              "
                              :disabled="
                                !settings.webhook.enabled.giveawayEntered
                              "
                              color="secondary"
                              label="Mention everyone on giveaway enter"
                            >
                            </v-switch>
                          </div>
                          <br />
                          <v-divider></v-divider>
                          <div>
                            <v-switch
                              v-model="settings.webhook.enabled.giveawayWin"
                              color="primary"
                              label="Fire webhook on giveaway win"
                            >
                            </v-switch>
                            <v-switch
                              v-model="
                                settings.webhook.mentionEveryone.giveawayWin
                              "
                              :disabled="!settings.webhook.enabled.giveawayWin"
                              color="secondary"
                              label="Mention everyone on giveaway win"
                            >
                            </v-switch>
                          </div>
                          <br />
                          <v-divider></v-divider>
                          <div>
                            <v-switch
                              v-model="settings.webhook.enabled.inviteJoin"
                              color="primary"
                              label="Fire webhook on invite join"
                            >
                            </v-switch>
                            <v-switch
                              v-model="
                                settings.webhook.mentionEveryone.inviteJoin
                              "
                              :disabled="!settings.webhook.enabled.inviteJoin"
                              color="secondary"
                              label="Mention everyone on invite join"
                            >
                            </v-switch>
                          </div>
                          <br />
                          <v-divider></v-divider>
                          <div>
                            <v-switch
                              v-model="settings.webhook.enabled.inviteFail"
                              color="primary"
                              label="Fire webhook on invite fail"
                            >
                            </v-switch>
                            <v-switch
                              v-model="
                                settings.webhook.mentionEveryone.inviteFail
                              "
                              :disabled="!settings.webhook.enabled.inviteFail"
                              color="secondary"
                              label="Mention everyone on invite fail"
                            >
                            </v-switch>
                          </div>
                        </div>
                      </v-expansion-panel-content>
                    </v-expansion-panel>
                  </v-expansion-panels>
                </v-expansion-panel-content>
              </v-expansion-panel>
              <v-expansion-panel>
                <v-expansion-panel-header>
                  <div>
                    <v-icon left> fas fa-cog </v-icon>
                    Other
                  </div>
                  <template #actions class="mx-16">
                    <v-spacer></v-spacer>
                    <v-icon large> fas fa-angle-down </v-icon>
                  </template>
                </v-expansion-panel-header>
                <v-expansion-panel-content>
                  <div>
                    For now there isn't anything here, check after an update!
                  </div>
                </v-expansion-panel-content>
              </v-expansion-panel>
            </v-expansion-panels>
          </v-col>

          <v-col class="align-center justify-space-around d-flex">
            Don't forget to restart after saving!
            <v-card outlined tile>
              <v-btn
                color="green"
                :loading="save"
                style="width: 180px"
                :disabled="save"
                @click="loader = 'save'"
              >
                Save
                <v-icon right> fas fa-save </v-icon>
              </v-btn>
            </v-card>
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
      finds: [''],
      settings: {
        tokens: {
          // Main Token (ex: Nz...)
          main: '',
          // Alt Tokens (ex: Nz...)
          alts: [''],
        },
        // The mode to run the sniper in. Options: main (only main account), alts (only alts), both
        mode: 'both',
        /*
           The status the accounts should be on.
           Options: online, dnd, idle, offline, default
           default means the status will not be modified.
        */
        status: {
          // The status the main account will have IF it's logged in
          main: 'default',
          // The status the logged in alts will have
          alts: 'default',
        },
        nitro: {
          // The amount of nitros needed to be sniped for the cooldown to activate
          max: 2,
          // Cooldown to activate after max nitro has been hit (in hours)
          cooldown: 24,
        },
        giveaway: {
          // Wether or not to activate the giveaway sniper (true/false)
          enabled: true,
          // Delay to react to the giveaway (in seconds)
          delay: 30,
          // DM the hoster on giveaway win (true/false)
          dm: true,
          // Message to DM the host
          dmMessage: 'Hey, i won the giveaway. Could i redeem my prize?',
          // How long to wait to DM (in seconds)
          dmDelay: 25,
          // Blacklisted words for giveaway prizes
          blacklistedWords: ['bot', 'test', 'ban'],
          // Only react to whitelisted giveaway prizes (true/false)
          whitelistOnly: false,
          // Whitelisted words for giveaway prizes
          whitelistedWords: ['nitro'],
          // Blacklisted Server IDs to not snipe giveaways on
          blacklistedServers: [''],
          // Only snipe giveaways on whitelisted servers (true/false)
          whitelistServersOnly: false,
          whitelistedServers: [''],
        },
        invite: {
          // Wether or not to activate the invite sniper (true/false)
          enabled: false,
          delay: {
            // Minimum delay to join the server (in seconds)
            min: 10,
            // Maximum delay to join the server (in seconds)
            max: 20,
          },
          members: {
            // The minimum member count the server should have
            min: 1500,
            // The maximum member count the server should have
            max: 50000,
          },
          // The amount of joined invites needed for the cooldown to activate
          max: 10,
          // Cooldown to activate after max joined invites has been hit (in hours)
          cooldown: 6,
          // Wether to allow the main token to snipe invites (true/false)
          onlyAlts: true,
        },
        webhook: {
          // URL to fire webhook to for notifications (ex: https://discord.com/api/webhooks/.../...)
          genericenable: false,
          url: '',
          enabled: {
            // Fire webhook on invalid code (true/false)
            codeInvalid: false,
            // Fire webhook on already redeemed code (true/false)
            codeAlreadyRedeemed: false,
            // Fire webhook on sniped code (true/false)
            codeSuccess: true,
            // Fire webhook on giveaway enter (true/false)
            giveawayEntered: true,
            // Fire webhook on giveaway win (true/false)
            giveawayWin: true,
            // Fire webhook on invite join (true/false)
            inviteJoin: false,
            // Fire webhook on failure of sniping invite (true/false)
            inviteFail: false,
          },
          mentionEveryone: {
            // Mention on invalid code (true/false)
            codeInvalid: false,
            // Mention on already redeemed code (true/false)
            codeAlreadyRedeemed: false,
            // Mention on sniped code (true/false)
            codeSuccess: true,
            // Mention on giveaway enter (true/false)
            giveawayEntered: false,
            // Mention on giveaway win (true/false)
            giveawayWin: true,
            // Mention on invite join (true/false)
            inviteJoin: false,
            // Mention on failure of sniping invite (true/false)
            inviteFail: false,
          },
        },
      },
      updatesetting: null,
      loader: null,
      save: null,
    }
  },
  watch: {
    async loader() {
      const l = this.loader
      this[l] = !this[l]

      await this.savesettings()
      await delay(2000)

      this[l] = false
      this.loader = null
    },
    'settings.webhook.enabled': {
      handler() {
        const entries = Object.entries(this.settings.webhook.enabled)
        for (const entry of entries) {
          if (!entry[1]) this.settings.webhook.mentionEveryone[entry[0]] = false
        }
      },
      deep: true,
    },
  },
  created() {
    this.updatesetting = this.$nuxtSocket({
      name: 'home',
      reconnectionAttempts: Number.MAX_VALUE,
      reconnectionDelay: 1000,
      reconnectionDelayMax: 2500,
      reconnection: true,
      teardown: false,
    })

    this.updatesetting.emit('settingrequest')

    this.updatesetting.on('settingback', (data) => {
      this.settings = data
    })
  },
  methods: {
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
    deletealt(index) {
      if (index > -1) this.settings.tokens.alts.splice(index, 1)
      console.log(this.settings.tokens.alts)
    },
    addalt() {
      this.settings.tokens.alts.push('')
      console.log(this.settings.tokens.alts)
    },
    async savesettings() {
      await this.updatesetting.emit('settingupdate', this.settings)
    },
  },
}
</script>
