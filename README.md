# docker-chakram-nightmare

The Docker image for running [Chakram](https://github.com/CKOTech/checkout-chakram) and [Nightmare](https://github.com/segmentio/nightmare) tests.

## Command line use

Given your current working directory is the root of your sources, the following command executes the Chakram tests.

```bash
docker run -e MOCHA_GREP=@regression -e ENV=QA --rm -t -v ${PWD}:/usr/src/app vladimiraleksandrovcko/docker-chakram-nightmare:9.11-slim-tc
```

Environment variable **ENV** defines the target environment to test (e.g. _QA_, _DEV_), while environment variable **MOCHA_GREP** specifies the test scope. 

A _mochawesome_ test report will be saved in the current directory as specified by `${PWD}`. The Docker image is configured to execute command `xvfb-run npm run test` upon running. 

To execute another command, e.g. `node test/mochaScript.js`, prepend it with script name `xvfb-run` and execute in `/bin/sh -c` as follows:

```bash
docker run -e MOCHA_GREP=@regression -e ENV=QA --rm -t -v ${PWD}:/usr/src/app vladimiraleksandrovcko/docker-chakram-nightmare:9.11-slim-tc /bin/sh -c 'xvfb-run node test/mochaScript.js'
```

**The use of shell script [`xvfb-run`](https://github.com/revnode/xvfb-run/blob/master/xvfb-run) is required for Nightmare tests.**

## Image Details

The Docker image located in [slim-tc](slim-tc) is based on `node:9.11-slim` (see [official Node.js repository](https://hub.docker.com/_/node/)) and upon build installs all the required Node.js Modules (as listed in [`package.json`](package.json)) along with shared dependencies for `electron` and `xvfb` ([X virtual framebuffer](https://en.wikipedia.org/wiki/Xvfb)) required for headless browser testing.

Current directory should not contain a folder `node_modules` as local modules may conflict with the modules in the Docker image, which is especially the case with module `electron` used by `Nightmare` tests.

Any references to local `node_modules` should also be avoided:

~~`var chakram = require('../../node_modules/chakram/lib/chakram.js')`~~
 `var chakram = require('chakram')`

There's another Docker image vladimiraleksandrovcko/docker-chakram-nightmare:dotnet-2.1.302-node-10.7.0, located in [dotnet-node](dotnet-node) which is based on top of danielmunchcko/dotnet:2.1.302-node-10.7.0 and thus contains a completed .NET Core SDK.