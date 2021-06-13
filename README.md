# terminal
My attempt at creating a terminal lookalike in a browser with custom commands and stuff

## commands.csv
Biggest thing to be aware of is how commands.csv syntax works and runs.
'help' returns all the things currently in the file unless deliberately hidden with a prepended '*'

### syntax
```
[0],          [1],           [2],       [3]
[command key],[command type],[response],[second response]
```

### guide
```
// hide a command from help with *, it can still be run manually!
*command

// print a basic line
command,line,content

// present confirm step before running next command
// note the next command must be hidden and on the next line
command,confirm,content,nextCommand
*nextCommand

// run an 'installed' program
command,program,NameOfProgram
```


## Build Setup

```bash
# install dependencies
$ npm install

# serve with hot reload at localhost:3000
$ npm run dev

# build for production and launch server
$ npm run build
$ npm run start

# generate static project
$ npm run generate
```

For detailed explanation on how things work, check out [Nuxt.js docs](https://nuxtjs.org).
