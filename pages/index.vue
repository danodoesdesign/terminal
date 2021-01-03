<template>
  <div
    id="terminal"
    ref="canvas"
    class="block"
    @click.native="focusCommandLine"
  >
    <div class="flex flex-row text-gray-600">
      <pre class="text-2xs mb-10">
                  (@@@@@@@@@@@@@                  
             @@@@@@@@   @@@   &@@@@@@@            
          @@@@@@@@      @@@       @@@@@@@         
       @@@ (@@@@        @@@         @@@  @@@      
     &@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@     
    @@   @@@@           @@@           @@@@   @@   
   @@   /@@@            @@@            @@@    @@  
  @@    @@@@            @@@            &@@@   ,@& 
  @@    @@@#            @@@             @@@    @@ 
  @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@
  @@    @@@/            @@@             @@@    @@ 
  @@    @@@@            @@@            (@@@    @@ 
   @@   %@@@            @@@            @@@    @@  
    @@   @@@@           @@@           @@@@   @@   
     @@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@@    
       @@@ @@@@/        @@@         @@@% @@@      
         @@@ @@@@,      @@@       @@@@(@@*        
             @@@@@@@    @@@    @@@@@@@            
                  @@@@@@@@@@@@@@@
                  </pre
      >
      <p id="introText" class="self-center hidden sm:inline-block ml-10">
        danodoesdesign terminal<br />
        version 0.2 pre-release<br />
        next up: airtable API to parse and respond to commands
      </p>
    </div>
    <ResponseLine>we have wares, if you have the coin</ResponseLine>
    <CommandLine id="the-line" v-on:command="progressTerminal" />
  </div>
</template>

<script>
import ResponseLine from '../components/ResponseLine.vue'
import Vue from 'vue'
export default {
  methods: {
    focusCommandLine() {
      document.getElementById('command-line').focus()
    },

    runCommand(command) {
      console.log('runCommand called with ' + command)

      /*
      some sort of string check to see if command matches anything
      it will need to retrieve:
      - what to respond with
      - type of response (error, success, future ideas like 'loading bar')
      */

      var responseType = 'success' // success / error / empty(default)
      var responseContent = 'word(s) entered successfully'

      var ResponseComponentClass = Vue.extend(ResponseLine)

      var ResponseInstance = new ResponseComponentClass({
        propsData: { type: responseType },
      })
      ResponseInstance.$slots.default = [responseContent]
      ResponseInstance.$mount()
      this.$refs.canvas.appendChild(ResponseInstance.$el)
    },

    progressTerminal(command) {
      console.log('progressTerminal run with ' + command)

      //create structure of a ResponseLine
      var ResponseComponentClass = Vue.extend(ResponseLine)

      //this is the logic for now — need to connect a spreadsheet or something for responses
      //logic needs to end by creating the instance and setting the slot (ie the text inside)

      // check if command is not empty
      if (command != '') {
        // if it has something, created an instance of ResponseLine that will play back the command entered
        var RepeatedCommand = new ResponseComponentClass({
          propsData: { type: 'repeated' },
        })
        RepeatedCommand.$slots.default = [command]

        // then mount it and append to the canvas
        RepeatedCommand.$mount()
        this.$refs.canvas.appendChild(RepeatedCommand.$el)

        //then actually run the command

        this.runCommand(command)

        // otherwise, if command was equal to empty, send specialised error
      } else {
        var ResponseInstance = new ResponseComponentClass({
          propsData: { type: 'error' },
        })
        ResponseInstance.$slots.default = ['no command entered']
        ResponseInstance.$mount()
        this.$refs.canvas.appendChild(ResponseInstance.$el)
      }

      //gets the editable command line and moves it to the end no matter what happens
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
