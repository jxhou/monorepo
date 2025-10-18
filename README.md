This is a yarn 2+ workspace based monorepo using typescript for all projects.

For yarn workspace setup details see:
	docs/yarn-monorepo-setup.md
## apps and packages
	package.json
		"workspaces": [
			"apps/*",
			"packages/*"
		],
	
	Two workspace folders are defined: apps, and packages.
	app folder will host standalone app such as react app or nodejs app, and,
	packages folder will host shared libraries.
## yarn commands
Run a command within the specified workspace.
	$> yarn workspace <workspaceName> <commandName> ...
### Add a package to a single workspace :
	yarn workspace components add -D react
### Run build script on a api workspace :
	yarn workspace api run build
		or
	yarn workspace api build

### Common commands for this monorepo
	yarn myapi build - run api's build script
	yarn myapi dev   - run api's dev script
	yarn myapp build - run my-react-app's build script
		where myapi and myapp are scripts defined in root package.json

### vscode debug
	following debug settings for api server:
		"Launch api server + watch": launch api server in ts, and debug, and watch at same time;
		"Launch api server": launch api server in js, after compile ts, and debug;
		"Nodemon: Attach Express.js + TypeScript": attach to the running api server in debug mode.

## Features