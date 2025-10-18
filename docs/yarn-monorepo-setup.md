# monorepo
monorepo exercise
## Setup monorepo using yarn berry + pnp + typescript

### yarn 
	The official yarn 2+ 
			https://yarnpkg.com/cli/install
	The 1.x yarn
			https://classic.yarnpkg.com/lang/en/

### yarn pnp (Plug'n'Play)	(https://yarnpkg.com/features/pnp)
	Yarn generates a single .pnp.cjs file instead of the usual node_modules folder containing copies of various packages. 
	The .pnp.cjs file contains various maps: one linking package names and versions to their location 
	on the disk and another one linking package names and versions to their list of dependencies.

	The packages are installed in .yarn/cache

	In pnp, when node resolves dependency packages, it relies on the .pnp.cjs to figure out the location of package on disk.
	This registration is generally transparent: any direct or indirect node command executed through one of your scripts 
	entries will automatically register the .pnp.cjs file as a runtime dependency. 

	For the vast majority of use cases, the following will work just as you would expect:
	{
		"scripts": {
			"start": "node ./server.js",
			"test": "jest"
		}
	}

	If you need to run an arbitrary Node script, use yarn node as the interpreter, instead of node. 
	This will be enough to register the .pnp.cjs file as a runtime dependency.
		yarn node ./server.js

#### Zero installs (https://yarnpkg.com/features/zero-installs)
	See the .gitignore configuration below for zero installs, which will save the necessary files to git, 
	which will avoid "yarn install" after download the source code from git.

### Setup initial yarn workspaces with pnp enabled
    * install yarn globally but only the latest 1.X version(1.22.10)
        sudo npm install -g yarn
	* create monorepo host folder
			mkdir monorepo  & cd monorepo
	* install local yarn berry 	
			yarn set version berry
                This will install a local version of the latest yarn 2+ in the host folder.
				This will download the current, actively developed version of Yarn – berry – save it to a .yarn/releases/ directory in your project, and set up a .yarnrc.yml configuration file as well.
				Your project is now set up with a project-specific version of Yarn.
				This will not impact your global yarn version at all.
	* initialize yarn workspace
		yarn init -w
			It creates a packages folder, a .gitignore, and a package.json.

		package.json
		{
		  "name": "monorepo",
		  "packageManager": "yarn@3.2.0",
		  "private": true,
		  "workspaces": [
			"packages/*"
		  ]
		}
		
		.gitignore with the content:
		
			.yarn/*
			!.yarn/patches
			!.yarn/plugins
			!.yarn/releases
			!.yarn/sdks
			!.yarn/versions

			# Swap the comments on the following lines if you don't wish to use zero-installs
			# Documentation here: https://yarnpkg.com/features/zero-installs
			!.yarn/cache
			#.pnp.*
			
		This default content is for zero-installs
				
		This ignores the entire .yarn directory, and then adds in some exceptions for important folders, including the releases directory which contains your project-specific version of Yarn.
				
				.pnp.js - This is the Plug’n’Play (PnP) file. The PnP file tells your Node app or build how to find the dependencies that are stored in .yarn/cache.
				.yarn/cache - This directory will have the dependencies that are needed to run and build your app.
				yarn.lock - The lock file still is used to lock the versions that are resolved from the package.json.

### Yarn plugins
	command to install a plugin:
		yarn plugin import <name>

    * yarn plugin import typescript
    * yarn plugin import workspace-tools

	The typescript plugin will improves the TypeScript experience (for example by automatically 
	adding @types packages as dependencies when needed).

	The workspace-tools will  adds various commands that make working with workspaces a more pleasing 
	experience (for example yarn workspaces foreach).

### vscode editor support
    * install ZipFS vscode extension
    * yarn add --dev typescript 
	* yarn add --dev eslint
	* yarn dlx @yarnpkg/sdks vscode
	* yarn install

	ZipFS vscode extension allows user to edit yarn packages in cache directly