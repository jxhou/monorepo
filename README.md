# monorepo
monorepo exercise
## Setup monorepo using yarn berry + pnp + typescript

	### Setup initial yarn workspaces with pnp enabled
		* create monorepo host folder
			mkdir monorepo  & cd monorepo
		* install local yarn berry 	
			yarn set version berry
		* initialize yarn workspace
			yarn init -w

    ### Yarn plugins
        * yarn plugin import typescript
        * yarn plugin import workspace-tools

    ### vscode editor support
        * install ZipFS vscode extension
        * yarn add --dev typescript 
		* yarn add --dev eslint
		* yarn dlx @yarnpkg/sdks vscode
		* yarn install