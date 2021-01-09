<template>
  <div>
    <div id="response-line-default" class="inline-block align-text-top">
      <span>ddt: available commands:</span><br />
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
  mounted() {
    var commandsLength = this.commandsList.length
    for (var i = 0; i < commandsLength; i++) {
      var ResponseComponentClass = Vue.extend(ResponseLine) // create uninitiated instance of ResponseLine component
      var ResponseInstance = new ResponseComponentClass({
        propsData: { type: 'table-line' },
      })
      var commandString = this.commandsList[i][0]
      ResponseInstance.$slots.default = commandString // put the responseContent into the component's slot
      ResponseInstance.$mount() // mount this instance
      this.$refs.tableArea.appendChild(ResponseInstance.$el) // append to the end of the div with ref of 'canvas'
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
