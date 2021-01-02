<template>
  <div class="block align-text-top text-gray-600" @click="focusCommandLine">
    guest@danodoesdesign %&nbsp;
    <div
      class="inline-block align-text-top pr-64 cursor-text"
      id="command-line"
      ref="commandLine"
      contenteditable="true"
      autocomplete="off"
      @keyup.enter="runCommand"
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
    runCommand() {
      // Remove the cursor through blur
      this.$refs.commandLine.blur()

      // get the content thats typed in
      var commandContent = document.getElementById('command-line')

      // clean it of the result of typing [enter] —— it added two <br>'s instead of one for some reason
      commandContent.innerHTML = commandContent.innerHTML.replace(
        '<br><br>',
        ''
      )
      // and incase of spaces, only going up to 2 atm
      commandContent.innerHTML = commandContent.innerHTML.replace('&nbsp;', '')
      commandContent.innerHTML = commandContent.innerHTML.replace(
        '&nbsp;&nbsp;',
        ''
      )

      // emit that result to parent
      this.$emit('command', commandContent.innerHTML)
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
