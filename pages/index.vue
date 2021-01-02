<template>
  <div id="terminal" ref="canvas" class="block">
    <div class="flex flex-row text-gray-600">
      <pre class="text-xs mb-10">
                  (@@@@@@@@@@@@@                  
             @@@@@@@@   @@@   &@@@@@@@            
          @@@@@@@@      @@@       @@@@@@@         
       @@@ (@@@@        @@@         @@@  @@@      
     &@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@     
    @@   @@@@           @@@           @@@@   @@   
   @@   /@@@            @@@            @@@    @@  
  @@    @@@@            @@@            &@@@   ,@& 
  @@    @@@#            @@@             @@@    @@ 
  @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@ 
  @@    @@@/            @@@             @@@    @@ 
  @@    @@@@            @@@            (@@@    @@ 
   @@   %@@@            @@@            @@@    @@  
    @@   @@@@           @@@           @@@@   @@   
     @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@    
       @@@ @@@@/        @@@         @@@% @@@      
         @@@ @@@@,      @@@       @@@@(@@*        
             @@@@@@@    @@@    @@@@@@@            
                  @@@@@@@@@@@@@@@
                  </pre
      >
      <p id="introText" class="self-center ml-20">
        danodoesdesign terminal<br />
        version 0.1 pre-release
      </p>
    </div>
    <ResponseLine>we have wares, if you have the coin</ResponseLine>
    <CommandLine @command="runCommand" />
  </div>
</template>

<script>
import ResponseLine from '../components/ResponseLine.vue'
import CommandLine from '../components/CommandLine.vue'
import Vue from 'vue'
export default {
  methods: {
    runCommand(command) {
      console.log('test run with ' + command)

      var ResponseComponentClass = Vue.extend(ResponseLine)
      var CommandComponentClass = Vue.extend(CommandLine)

      if (command === 'success') {
        var ResponseInstance = new ResponseComponentClass({
          propsData: { type: 'success' },
        })
      } else {
        var ResponseInstance = new ResponseComponentClass({
          propsData: { type: 'error' },
        })
        ResponseInstance.$slots.default = [
          'danodoesdesign: command not found: ' + command,
        ]
      }

      var CommandInstance = new CommandComponentClass({})

      ResponseInstance.$mount()
      this.$refs.canvas.appendChild(ResponseInstance.$el)
      CommandInstance.$mount()
      this.$refs.canvas.appendChild(CommandInstance.$el)
    },
  },
  mounted() {},
}
</script>

<style>
html {
  @apply bg-black  p-5;
}

#introText {
  margin-top: -5px;
}
</style>
