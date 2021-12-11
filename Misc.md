# Misc

## npm vs npx
npm:
	* dependency/package manager you get out of the box when you install Node.js. 
	* Can be installed in two places
		1. **local** - links created at `./node_modules/.bin/`
		2. **global** - links created fromn the global `bin/` directory (`/usr/local/bin` on Linux or `%AppData/npm` windows)

npx:
	* An npm package runner
	* Makes it easy to use CLI tools and other executables hosted on the registry in one step

* So if you ever wanted to run a script, you'd first have to download it with npm. Then run it. npx is the alternative to that
