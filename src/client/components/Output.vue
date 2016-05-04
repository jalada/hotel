<template>
  <div>
    <div class="toggle-background">
      <a
        class="toggle"
        v-bind:class="lightsOn() ? 'on' : 'off'"
        v-on:click.prevent="toggleLights()"
      >
        <i
          class="fa"
          v-bind:class="lightsOn() ? 'fa-toggle-on' : 'fa-toggle-off'"
        >
        </i>
      </a>
    </div>

    <div v-on:scroll="onScroll" v-bind:class="lightsOn() ? 'white' : 'black'">
      <div v-for="item in output" track-by="_uid">
        {{{ item.line | escape | ansi }}}
      </div>
    </div>
  </div>
</template>

<script>
const escapeHTML = require('escape-html')
const ansiHTML = require('ansi-html')

export default {
  props: ['lights'],

  data() {
    return {
      scroll: true,
      scrollback: 1000,
      height: 20
    }
  },

  vuex: {
    getters: {
      outputId: (state) => state.outputId,
      output: (state) => state.output
    }
  },

  filters: {
    ansi: ansiHTML,
    escape: escapeHTML
  },

  watch: {
    output(now, before) {
      this.$nextTick(() => {
        // keep scroll at the bottom if it already is
        if (this.scroll) this.$el.scrollTop = this.$el.scrollHeight
      })
    }
  },

  methods: {
    onScroll(event) {
      var element = event.target
      this.scroll = element.scrollHeight - element.scrollTop === element.clientHeight
    },

    lightsOn() {
      return this.lights;
    },

    toggleLights() {
      this.$dispatch('toggle-lights');
    }
  }
}
</script>
