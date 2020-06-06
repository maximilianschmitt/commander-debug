# TypeError when running nested subcommand

```
internal/validators.js:120
    throw new ERR_INVALID_ARG_TYPE(name, 'string', value);
    ^

TypeError [ERR_INVALID_ARG_TYPE]: The "path" argument must be of type string. Received null
    at validateString (internal/validators.js:120:11)
    at Object.extname (path.js:1229:5)
    at Command._executeSubCommand (/Users/max/Desktop/commander-debug/node_modules/commander/index.js:774:46)
    at Command._dispatchSubcommand (/Users/max/Desktop/commander-debug/node_modules/commander/index.js:863:12)
    at Command._parseCommand (/Users/max/Desktop/commander-debug/node_modules/commander/index.js:882:12)
    at Command._dispatchSubcommand (/Users/max/Desktop/commander-debug/node_modules/commander/index.js:865:18)
    at Command._parseCommand (/Users/max/Desktop/commander-debug/node_modules/commander/index.js:882:12)
    at Command.parse (/Users/max/Desktop/commander-debug/node_modules/commander/index.js:717:10)
    at Object.<anonymous> (/Users/max/Desktop/commander-debug/cli/hello:14:9)
    at Module._compile (internal/modules/cjs/loader.js:1138:30) {
  code: 'ERR_INVALID_ARG_TYPE'
}
```

## Setup

Files:

```
cli/
    hello                # `hello`
    hello-world          # `hello world`
    hello-world-germany  # `hello world germany`
```

cli/hello

```
#!/usr/bin/env node
const { program, Command } = require("commander");
const packageJSON = require("../package.json");

program.version(packageJSON.version);

const helloWorld = new Command("world");
helloWorld.command("germany", "Saying hello to Germany", {
  executableFile: "hello-world-germany",
});

program.addCommand(helloWorld);

program.parse(process.argv);
```

## How to reproduce

### Running `hello`

✅ Works

```
~/Desktop/commander-debug $ node cli/hello
Usage: hello [options] [command]

Options:
  -V, --version   output the version number
  -h, --help      display help for command

Commands:
  world
  help [command]  display help for command
```

### Running `hello world`

✅ Works

```
~/Desktop/commander-debug $ node cli/hello world
Usage: hello world [options] [command]

Options:
  -h, --help      display help for command

Commands:
  germany         Saying hello to Germany
  help [command]  display help for command
```

### Running `hello world germany`

❌ Get TypeError

```
~/Desktop/commander-debug $ node cli/hello world germany
internal/validators.js:120
    throw new ERR_INVALID_ARG_TYPE(name, 'string', value);
    ^

TypeError [ERR_INVALID_ARG_TYPE]: The "path" argument must be of type string. Received null
    at validateString (internal/validators.js:120:11)
    at Object.extname (path.js:1229:5)
    at Command._executeSubCommand (/Users/max/Desktop/commander-debug/node_modules/commander/index.js:774:46)
    at Command._dispatchSubcommand (/Users/max/Desktop/commander-debug/node_modules/commander/index.js:863:12)
    at Command._parseCommand (/Users/max/Desktop/commander-debug/node_modules/commander/index.js:882:12)
    at Command._dispatchSubcommand (/Users/max/Desktop/commander-debug/node_modules/commander/index.js:865:18)
    at Command._parseCommand (/Users/max/Desktop/commander-debug/node_modules/commander/index.js:882:12)
    at Command.parse (/Users/max/Desktop/commander-debug/node_modules/commander/index.js:717:10)
    at Object.<anonymous> (/Users/max/Desktop/commander-debug/cli/hello:14:9)
    at Module._compile (internal/modules/cjs/loader.js:1138:30) {
  code: 'ERR_INVALID_ARG_TYPE'
}
```
