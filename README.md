This is a minimal reproduction repository for https://github.com/shelljs/shelljs/issues/1133.

There seem to be a few things constributing to the issue:

- `shelljs` is a CJS module in `node_modules`
- `index.mjs` is an ESM module
- There is a circular dependency in `shelljs`
- This is all fine on node `node@20.5.1` but fails in `node@20.6.0`

```sh
> node --version
v20.5.1
> node index.mjs
start:  requiring common.js from shell.js
start:  requiring shell.js from common.js
finish: requiring shell.js from common.js
finish: requiring common.js from shell.js
calling common.register() -> register() return value
Done!
```

```sh
> node --version
v20.6.0
> node index.mjs
start:  requiring common.js from shell.js
start:  requiring shell.js from common.js
start:  requiring common.js from shell.js
finish: requiring common.js from shell.js
/Users/matt/me/shopify/repos/playgrounds/shelljs-repro/node_modules/shelljs-fake/shell.js:5
console.log('calling common.register() ->', common.register())
                                                   ^

TypeError: common.register is not a function
    at Object.<anonymous> (/Users/matt/me/shopify/repos/playgrounds/shelljs-repro/node_modules/shelljs-fake/shell.js:5:52)
    at Module._compile (node:internal/modules/cjs/loader:1241:14)
    at Module._extensions..js (node:internal/modules/cjs/loader:1295:10)
    at Module.load (node:internal/modules/cjs/loader:1091:32)
    at Module._load (node:internal/modules/cjs/loader:938:12)
    at Module.require (node:internal/modules/cjs/loader:1115:19)
    at require (node:internal/modules/helpers:130:18)
    at Object.<anonymous> (/Users/matt/me/shopify/repos/playgrounds/shelljs-repro/node_modules/shelljs-fake/common.js:2:15)
    at Module._compile (node:internal/modules/cjs/loader:1241:14)
    at Module._extensions..js (node:internal/modules/cjs/loader:1295:10)

Node.js v20.6.0
```
