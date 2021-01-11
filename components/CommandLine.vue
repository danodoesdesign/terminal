<template v-bind:command="">
  <div class="block align-text-top text-gray-600" @click="focusCommandLine">
    guest@{{ terminalTitle }} %&nbsp;
    <div
      class="inline-block align-text-top ml-0 sm:-ml-2 pr-64 cursor-text"
      id="command-line"
      ref="commandLine"
      contenteditable="true"
      autocomplete="off"
      @keyup.enter="cleanAndSend"
      @click.native="focusCommandLine"
    ></div>
  </div>
</template>
<script>
export default {
  data() {
    return {
      terminalTitle: '',
    }
  },
  methods: {
    setDefaultTerminalTitle() {
      if (this.terminalTitle == '') {
        this.terminalTitle = 'terminal'
      } else {
        console.log('terminal title already set as ' + this.terminalTitle)
      }
    },
    focusCommandLine() {
      this.$refs.commandLine.focus()
    },
    cleanAndSend() {
      this.$refs.commandLine.blur()
      //
      var rawCommandContent = document.getElementById('command-line')
      var lowercaseCommandContent = rawCommandContent.innerHTML.toLowerCase()
      var slashCleaned = lowercaseCommandContent.replace(/\//g, '')
      var divCleaned = slashCleaned.replace(/<div>/gi, '')
      var spaceCleaned = divCleaned.replace(/&nbsp;/gi, '')
      var breakCleaned = spaceCleaned.replace(/<br>/gi, '')
      /* 
      leaving space here incase further cleaning becomes required
      */
      var cleanedCommand = breakCleaned
      //
      this.$emit('command', cleanedCommand)
      //
      rawCommandContent.innerHTML = ''
      //
      this.focusCommandLine()
    },
  },
  mounted() {
    this.focusCommandLine()
    this.setDefaultTerminalTitle()
  },
}
</script>
<style>
#command-line {
  @apply text-white;
  outline: none;
  margin-top: -2px;
  &:hover,
  &:focus {
    outline: none;
  }
}
</style>
