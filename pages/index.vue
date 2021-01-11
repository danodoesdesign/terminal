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
          version 0.4 pre-release<br />
          new: confirmation step. try `dance`<br />
          next: arbitrary loading bar
        </p>
      </div>
      <ResponseLine>type `help` to see available commands</ResponseLine>
      <CommandLine id="the-line" v-on:command="progressTerminal" />
    </div>
    <div v-else>
      <!-- List all programs here -->
      <!-- Set each to only be active if they are declared the activeProgram -->
      <DanceParty
        v-if="activeProgram == 'DanceParty'"
        v-on:exit="setTerminalView"
      />
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
      terminalView: true, // when false displays the second canvas
      activeProgram: '',
      commandsList: [],
      confirmActive: false,
      confirmIndex: '',
    }
  },
  mounted() {
    this.readCSV()
  },

  methods: {
    readCSV() {
      var file = 'commands.csv'
      this.$papa.parse(file, {
        // https://www.papaparse.com/docs -> parse remote file
        download: true,
        complete: this.onReadCSVComplete, // run seperate method (weird hack that made this work in Vue or Nuxt idk)
      })
    },

    onReadCSVComplete(results, file) {
      this.commandsList = results.data
    },

    focusCommandLine() {
      document.getElementById('command-line').focus()
    },

    setTerminalView() {
      this.terminalView = true
      this.activeProgram = ''
    },

    runCommand(command) {
      console.log(command)
      var responseType = '' // success / error / empty(default)
      var responseContent = ''

      if (this.confirmActive == true) {
        //
        // checking for Y / N
        //
        if (command == 'y') {
          //
          this.confirmActive = false
          this.runCommand('*' + this.commandsList[this.confirmIndex][3])
          this.confirmIndex = ''
          //
        } else if (command == 'n') {
          //
          this.confirmActive = false
          this.confirmIndex = ''
          responseType = 'error'
          responseContent = 'command cancelled'
          //
        } else {
          responseType = 'error'
          responseContent = '(y/n)'
        }
        //
        var ResponseClass = Vue.extend(ResponseLine)
        var ResponseInstance = new ResponseClass({
          propsData: { type: responseType },
        })
        ResponseInstance.$slots.default = [responseContent]
        ResponseInstance.$mount()
        this.$refs.canvas.appendChild(ResponseInstance.$el)
        //
      } else {
        //
        for (var i = 0; i < this.commandsList.length; i++) {
          //
          // get the current loop array and define managable variables
          var commandKey = this.commandsList[i][0]
          var commandType = this.commandsList[i][1]
          var response = this.commandsList[i][2]
          var secondResponse = this.commandsList[i][3]
          //
          if (commandKey == command) {
            //
            if (commandType == 'line') {
              //
              responseType = 'success'
              responseContent = response
              //
              //
            } else if (commandType == 'helpTable') {
              //
              var TableClass = Vue.extend(HelpTable)
              var TableInstance = new TableClass({
                propsData: { commandsList: this.commandsList },
              })
              TableInstance.$mount()
              this.$refs.canvas.appendChild(TableInstance.$el)
              break
              //
              //
            } else if (commandType == 'confirm') {
              //
              this.confirmActive = true
              this.confirmIndex = i
              responseContent = response + ': are you sure? (y/n)'
              break
              //
              //
            } else if (commandType == 'program') {
              //
              // each line below defines
              //
              if (response == 'DanceParty') {
                responseContent = 'stop dancing'
                this.activeProgram = response
                this.terminalView = false
                break
                //
              } else if (response == 'Refresh') {
                this.readCSV()
                responseType = 'success'
                responseContent = 'commands refreshed'
                break
                //
              }
              //
            } else {
              // if the command key matched, but the command type didn't
              responseType = 'error'
              responseContent =
                'system error: command key matched, but command type was not valid. check commands.csv for `' +
                commandKey +
                '`'
            }
            break
            //
          } else {
            // if the command key didn't match anything in the list
            responseType = 'error'
            responseContent = 'no such command'
            //
          }
        }
        //
        if (responseContent != '') {
          var ResponseClass = Vue.extend(ResponseLine)
          var ResponseInstance = new ResponseClass({
            propsData: { type: responseType },
          })
          ResponseInstance.$slots.default = [responseContent]
          ResponseInstance.$mount()
          this.$refs.canvas.appendChild(ResponseInstance.$el)
        }
      }
    },

    progressTerminal(command) {
      var ResponseComponentClass = Vue.extend(ResponseLine)

      if (command != '') {
        var RepeatedCommand = new ResponseComponentClass({
          propsData: { type: 'repeated' },
        })
        RepeatedCommand.$slots.default = [command]
        RepeatedCommand.$mount()
        this.$refs.canvas.appendChild(RepeatedCommand.$el)
        //
        this.runCommand(command)
        //
      } else {
        var ResponseInstance = new ResponseComponentClass({
          propsData: { type: 'error' },
        })
        ResponseInstance.$slots.default = ['no command entered']
        ResponseInstance.$mount()
        this.$refs.canvas.appendChild(ResponseInstance.$el)
      }

      //move editable command line to bottom
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
