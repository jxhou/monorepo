## Node server in typescript.
### typescript setup
    a. install typescript in root folder.
    b. @yarnpkg/plugin-typescript, which Automatically adds @types/ packages into your dependencies when you add a package that doesn't include its own types.
        This yarn plugin, see 'yarn-monorepo-setup.md' for details.

### Run typescript app
    a. Compile ts to js, then node on the js file for production.
        tsc compiler or
        swc, esbuild, which are faster compiler with enhanced performance.
    b. ts-node
        run ts file directly.
    c. nodemon in dev env
        nodemon can run against both js file and also ts file, when integrated with ts-node under the cover.

### nodemon with debug as the main dev env
    As an effective dev environment, use nodemon to watch the code change and restart the server,
    and at the same time to place the node process in debug mode.

#### nodemon code watch/restart only
    a. no configuration
        Just install both nodemon and ts-node: "yarn add --dev nodemon ts-node", then:
            nodemon ./index.ts
        nodemon will automatically use ts-node under the cover.

    b. Manual configuration
        touch ./nodemon.json
				
        {
            "execMap": {
                "ts": "ts-node"
            }
        }

        This will instruct nodemon to use ts-node when deal with .ts file, which appears to be the default option. 
        So it is not necessary.

    c. Custom execution command
        In your package.json file, create a start script. This will serve as the command that will be run and restarted by nodemon when a file changes.
				
		nodemon --exec "yarn start"

        run nodemon on top of one of the scripts in package.json

#### debug typescript code only
    The trick here is to integrate node --inspect option with ts compilers such as ts-node/register or esbuild-register.

    1. run node --inspect with ts-node/register preloaded
        node --require ts-node/register --inspect ./src/index.ts

        This explicitly preload ts-node/register using --register or -r, which enables code watch.

    2. run node --inspect with esbuild-register preloaded
        node -r esbuild-register --inspect ./src/index.ts

        require to install esbuild-register.
        This uses esbuild-register as typescript compiler.

    Similarly use --inspect-brk to run server in debug mode but waiting for debugger to connect. 

#### debug and watch typescript code at the same time
    This probably is the preferred development environment, in which we can debug the code and get server restart when code udpated.
    
    1. The cleanest command solution
        nodemon ./src/index.ts
    
        This looks like [#### nodemon code watch/restart only]a simple nodemon with .ts. But the trick is in the nodemon.json:
            {
				"execMap": {
					"ts": "node --require ts-node/register --inspect"
				}
			}

        Here execMap is different from above. The ts is mapped to node --inspect, 
        which is same as: [#### debug typescript code only: 1].
        This probably is the cleanest way to do watch and inspect.

    2. The same idea, but more verbose:
        nodemon -e ts --exec \"node --require ts-node/register --inspect

        -e ts: force watch files with extension ts, otherwise, not watch not work.
        --exec: execute script

        Very similar to 1. above.

    3. Slightly different way
        nodemon -e ts --exec \"yarn start:node:ts-node-register\"

        Similar to 2. But execute one of scripts in package.json

    Three options above will start the node server in debug mode and watched.
    Then attach debugger to the debug port: 9229.

    Check out all the implementation in related scripts in apps/api/package.json 
        
#### vscode debug using scripts:
    Two different ways to debug:
        a. launch node app using any of "start:watch:inspect:X" script.
            This is useful way to launch app via script in package.json and attach right away.
            This is a good way to even debug the bootstrap code.
            
            "cwd": "${workspaceFolder}/apps/api" is a critical way to target server api workspace in the yarn berry workspace env.
        
        b. attach.
            Start node app in debug mode, and then attach use the attach method in launch.json

    See .vscode/launch.json for the details.
    This is configured at workspace root:

        {
            // Use IntelliSense to learn about possible attributes.
            // Hover to view descriptions of existing attributes.
            // For more information, visit: https://go.microsoft.com/fwlink/?linkid=830387
            "version": "0.2.0",
            "configurations": [
                {
                    "name": "Launch api server + watch",
                    "request": "launch",
                    "type": "node",
                    // yarn or npm, here use the local yarn version
                    "runtimeExecutable": "${workspaceFolder}/.yarn/releases/yarn-berry.cjs",
                    // run start:watch:inspect:1 script set in package.json
                    // it is like vs code running yarn myapi start:watch:inspect:1
                    // and attach right way, like inspect-brk
                    "runtimeArgs": [
                    "start:watch:inspect:1"
                    ],
                    // target api server workspace in yarn workspace environment
                    "cwd": "${workspaceFolder}/apps/api",
                    "sourceMaps": true,
                    // VS Code default debug port is 9229. Feel free to change it you are
                    // running mulitple applications in different VS Code windows
                    "port": 9229,
                },
                {
                    // This run compiled .js directly, not ideal
                    "type": "node",
                    "request": "launch",
                    "name": "Launch api server",
                    "preLaunchTask": "npm: build - apps/api",
                    "skipFiles": [
                        "<node_internals>/**"
                    ],
                    // launch particular file
                    "program": "${workspaceFolder}/apps/api/dist/index.js",
                    "outFiles": ["${workspaceFolder}/apps/api/dist/**/*.js"],
                    "runtimeExecutable": "yarn",
                    "runtimeArgs": [
                        "node"
                    ],
                },
                {
                    "name": "Nodemon: Attach Express.js + TypeScript",
                    "type": "node",
                    "request": "attach",
                    // VS Code default debug port is 9229. Feel free to change it you are
                    // running mulitple applications in different VS Code windows. If you
                    // want to change the port, make sure to update the script, i.e.,
                    // "start:inspect": "nodemon --inspect=1234 src/index.ts",
                    "port": 9229,
                    // If you make changes to your code and save changes, nodemon will
                    // automatically restart your server. In a similar way, you can restart
                    // the debugger. If "restart" is set to false, then every time you save
                    // new changes to your code while running nodemon, you will need
                    // to re-run the debugger
                    "restart": true,
                    "protocol": "inspector"
                }
            ]
        }