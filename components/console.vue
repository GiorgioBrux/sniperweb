<template>
  <div class="col s12 offset-m2 m8 test">
  <vuescroll>
    <div class="console" v-html="html">
  </div>
  </vuescroll>
  </div>

</template>

<script>
import AnsiUp from 'ansi_up';
import vuescroll from 'vuescroll';

export default {
  name: 'Console',
  components: {
    vuescroll
  },
  data () {
    return {
      ansi: new AnsiUp(),
      content: '',
      settings: {
        suppressScrollY: false,
        suppressScrollX: false,
        wheelPropagation: false
      }
    }
  },
  computed: {
    html () {
      return this.ansi.ansi_to_html(this.content).replace(/\n/gm, '<br>')
    }
  },
  beforeMount() {
    this.ansi = new AnsiUp();
  },
  mounted() {
    const socket = this.$nuxtSocket({
      name: 'logs'
    })
    socket.on("connect", () => {
      console.log(`Connected`);
    })

    socket
      .on('message', (msg) => {
        this.content += msg;
        console.log(`Received msg: ${msg}`);
      })
  },
  updated () {
    this.$el.lastElementChild.lastElementChild.lastElementChild.firstElementChild.lastElementChild.scrollIntoView({behavior: 'smooth'});
  }
}
</script>

<style lang="scss" scoped>
.console {
  font-family: 'Roboto Mono', monospace;
  text-align: left;
  background-color: black;
  color: #fff;
  overflow-y: auto;
  overflow-x: hidden;
}
.test {
  overflow: hidden;
}
</style>
