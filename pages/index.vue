<template>
  <div>
    <div
      v-if="terminalView"
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
          version 0.3 pre-release<br />
          next up: new methods to display results beyond a single line
        </p>
      </div>
      <ResponseLine>you should `r dance`</ResponseLine>
      <CommandLine id="the-line" v-on:command="progressTerminal" />
    </div>
    <div v-else>
      <DanceParty v-on:exit="setTerminalView" />
    </div>
  </div>
</template>

<script>
import ResponseLine from '../components/ResponseLine.vue'
import HelpTable from '../components/HelpTable.vue'
import Vue from 'vue'
import VuePapaParse from 'vue-papa-parse'
Vue.use(VuePapaParse)

export default {
  data: function () {
    return {
      terminalView: true, // when false displays a 'booted program', but right now its just DanceParty
      commandsList: [],
    }
  },
  mounted() {
    this.readCSV()
  },

  methods: {
    readCSV() {
      // init Papa Parse with defined commands.csv sitting in /static/
      console.log('readCSV called')
      var file = 'commands.csv'
      this.$papa.parse(file, {
        // https://www.papaparse.com/docs -> parse remote file
        download: true,
        complete: this.onReadCSVComplete, // run seperate method (weird hack that made this work in Vue or Nuxt idk)
      })
    },

    onReadCSVComplete(results, file) {
      console.log('onReadCSVComplete called, result was')
      console.log(this.commandsList)
      this.commandsList = results.data // set the Papa Parse returned data to the exisiting variable
    },

    focusCommandLine() {
      document.getElementById('command-line').focus()
    },

    setTerminalView() {
      this.terminalView = true
    },

    runCommand(command) {
      //define variables to be used below
      var responseType = '' // can be success / error / empty(default)
      var responseContent = ''

      var commandsLength = this.commandsList.length
      for (var i = 0; i < commandsLength; i++) {
        // loop through the parse results
        if (this.commandsList[i][0] == command) {
          // when the entered command matches the first column parse result

          if (this.commandsList[i][1] == 'line') {
            // matches second column, which is the command response type. so far only: line
            console.log('command found of type line')
            responseType = 'success'
            responseContent = this.commandsList[i][2] // fill the variables from before, we actually do stuff with them below
          } else if (this.commandsList[i][1] == 'table') {
            //effectively, any command of type 'table' meaning there could be heaps of versions as a catchall, will list the help list
            console.log('command found of type table')
            var TableComponentClass = Vue.extend(HelpTable) // create uninitiated instance of HelpTable component
            var TableInstance = new TableComponentClass({
              propsData: { commandsList: this.commandsList },
            })
            TableInstance.$mount() // mount this instance
            this.$refs.canvas.appendChild(TableInstance.$el) // append to the end of the div with ref of 'canvas'
          } else {
            console.log(
              'command type from commands.csv ' + i + ' was not valid'
            )
            console.log('command type was ' + this.commandsList[i][1]) // otherwise spits fun error
            responseType = 'error'
            responseContent = 'system error — please contact your administrator'
          }
          break
        } else if (command == 'r dance') {
          // manually run dance party, need to make this better soon.
          responseContent = 'stop dancing'
          this.terminalView = false
          break
        } else if (command == 'r ref') {
          // reload (ie just re-get commands.csv)
          this.readCSV()
          responseType = 'success'
          responseContent = 'commands refreshed'
          break
        } else {
          responseType = 'error'
          responseContent = 'no such command'
        }
      }

      var ResponseComponentClass = Vue.extend(ResponseLine) // create uninitiated instance of ResponseLine component

      var ResponseInstance = new ResponseComponentClass({
        propsData: { type: responseType }, // initiate instance of it passing in the responseType prop, we defined this above
      })
      ResponseInstance.$slots.default = [responseContent] // put the responseContent into the component's slot
      ResponseInstance.$mount() // mount this instance
      this.$refs.canvas.appendChild(ResponseInstance.$el) // append to the end of the div with ref of 'canvas'
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
