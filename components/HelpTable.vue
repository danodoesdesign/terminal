<template>
  <div>
    <div id="response-line-default" class="inline-block align-text-top">
      <span>{{ terminalTitle }}: available commands:</span><br />
    </div>
    <div ref="tableArea" class="ml-10"></div>
  </div>
</template>

<script>
import ResponseLine from '../components/ResponseLine.vue'
import Vue from 'vue'
export default {
  props: {
    commandsList: {},
  },
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
  },
  mounted() {
    this.setDefaultTerminalTitle()
    var commandsLength = this.commandsList.length
    //
    for (var i = 0; i < commandsLength; i++) {
      //
      if (this.commandsList[i][0][0] != '*') {
        var ResponseComponentClass = Vue.extend(ResponseLine)
        var ResponseInstance = new ResponseComponentClass({
          propsData: { type: 'table-line' },
        })
        var commandString = this.commandsList[i][0]
        ResponseInstance.$slots.default = commandString
        ResponseInstance.$mount()
        this.$refs.tableArea.appendChild(ResponseInstance.$el)
        //
      }
    }
  },
}
</script>

<style>
#response-line-error {
  @apply text-red-600;
  outline: none;
  margin-top: -2px;
  &:hover,
  &:focus {
    outline: none;
  }
}
#response-line-success {
  @apply text-green-500;
  outline: none;
  margin-top: -2px;
  &:hover,
  &:focus {
    outline: none;
  }
}
#response-line-default {
  @apply text-white;
  outline: none;
  margin-top: -2px;
  &:hover,
  &:focus {
    outline: none;
  }
}
#response-line-repeated {
  @apply text-gray-600;
  outline: none;
  margin-top: -2px;
  &:hover,
  &:focus {
    outline: none;
  }
}
</style>
