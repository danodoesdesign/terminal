<template>
  <div id="terminal" ref="canvas" class="block">
    <div class="flex flex-row text-gray-600">
      <pre class="text-2xs mb-10">
                  (@@@@@@@@@@@@@                  
             @@@@@@@@   @@@   &@@@@@@@            
          @@@@@@@@      @@@       @@@@@@@         
       @@@ (@@@@        @@@         @@@  @@@      
     &@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@     
    @@   @@@@           @@@           @@@@   @@   
   @@   /@@@            @@@            @@@    @@  
  @@    @@@@            @@@            &@@@   ,@& 
  @@    @@@#            @@@             @@@    @@ 
  @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
  @@    @@@/            @@@             @@@    @@ 
  @@    @@@@            @@@            (@@@    @@ 
   @@   %@@@            @@@            @@@    @@  
    @@   @@@@           @@@           @@@@   @@   
     @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@    
       @@@ @@@@/        @@@         @@@% @@@      
         @@@ @@@@,      @@@       @@@@(@@*        
             @@@@@@@    @@@    @@@@@@@            
                  @@@@@@@@@@@@@@@
                  </pre
      >
      <p id="introText" class="self-center ml-20">
        danodoesdesign terminal<br />
        version 0.1 pre-release<br />
        <a href="http://danodoes.design">learn more</a>
      </p>
    </div>
    <ResponseLine>we have wares, if you have the coin</ResponseLine>
    <CommandLine id="the-line" v-on:command="runCommand" />
  </div>
</template>

<script>
import ResponseLine from '../components/ResponseLine.vue'
import Vue from 'vue'
export default {
  methods: {
    runCommand(command) {
      console.log('runCommand run with ' + command)

      //create structure of a ResponseLine
      var ResponseComponentClass = Vue.extend(ResponseLine)

      //this is the logic for now — need to connect a spreadsheet or something for responses
      //logic needs to end by creating the instance and setting the slot (ie the text inside)
      if (command === 'hack') {
        var ResponseInstance = new ResponseComponentClass({
          propsData: { type: 'success' },
        })
        ResponseInstance.$slots.default = [
          'danodoesdesign: big hack energy complete',
        ]
      } else {
        var ResponseInstance = new ResponseComponentClass({
          propsData: { type: 'error' },
        })
        ResponseInstance.$slots.default = [
          'danodoesdesign: command not found: ' + command,
        ]
      }

      //create a line that logs the actual command run, mimicking a real terminal
      var RepeatedCommand = new ResponseComponentClass({
        propsData: { type: 'repeated' },
      })
      RepeatedCommand.$slots.default = [command]

      //mount instances and add to the end of the refs="canvas" div
      ResponseInstance.$mount()
      RepeatedCommand.$mount()
      this.$refs.canvas.appendChild(RepeatedCommand.$el)
      this.$refs.canvas.appendChild(ResponseInstance.$el)

      //gets the editable command line and moves it to the end
      var movingLine = document.getElementById('the-line')
      movingLine.parentNode.appendChild(movingLine)
    },
  },
}
</script>

<style>
html {
  @apply bg-black  p-5;
}

#introText {
  margin-top: -5px;
}

a {
  font-weight: 500;
  &:hover {
    outline: none;
    text-align: center;
    background: rgb(255, 255, 255);
    color: black;
  }
}
</style>
