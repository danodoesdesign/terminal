<template v-bind:command="">
  <div class="block align-text-top text-gray-600" @click="focusCommandLine">
    guest@ddt %&nbsp;
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
  methods: {
    focusCommandLine() {
      // on page load focus cursor
      this.$refs.commandLine.focus()
    },
    cleanAndSend() {
      // Remove the cursor through blur
      this.$refs.commandLine.blur()

      // get the content thats typed in
      var rawCommandContent = document.getElementById('command-line')

      // make it lowercase
      var lowercaseCommandContent = rawCommandContent.innerHTML.toLowerCase()

      // remove forward slashes
      var slashCleaned = lowercaseCommandContent.replace(/\//g, '')

      // clean it of <div>
      var divCleaned = slashCleaned.replace(/<div>/gi, '')

      // clean it of &nbsp;
      var spaceCleaned = divCleaned.replace(/&nbsp;/gi, '')

      // clean it of <br>
      var breakCleaned = spaceCleaned.replace(/<br>/gi, '')

      /* 

      leaving space here incase further cleaning becomes required

      */

      //making it final variable that makes the most sense
      var cleanedCommand = breakCleaned

      // emit that result to parent
      this.$emit('command', cleanedCommand)

      // then clear the text box
      rawCommandContent.innerHTML = ''

      // and refocus it
      this.focusCommandLine()
    },
  },
  mounted() {
    // on page load (techically when component is mounted ((ie loaded)) successfully)
    this.focusCommandLine()
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
