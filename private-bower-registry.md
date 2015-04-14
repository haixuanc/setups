## Prerequisites

- NPM

## Step 1. Create a Bower Package

Install Bower:

```bash
$ sudo npm install -g npm // update npm
$ sudo npm install -g bower
```

Create a sample bower project which provides a single CSS file:

```bash
$ mkdir path/bower-sample
$ cd path/bower-sample

// This is what our sample bower package provides
$ vim main.css
>> add some content

// Create bower.json file
$ bower init
>> set version to `1.0.0`
>> select `globals` for moduleType if you don't know what to choose
>> set main to `./main.css`
...

// Add some dependencies from public source to mimic real-world settings
$ bower install jquery --save
$ bower install angular --save
```

The final `bower.json` file shoul look like this:

```json
{
  "name": "bower-sample",
  "version": "1.0.0",
  "description": "a bower demo package",
  "main": "./main.css",
  "moduleType": [
    "globals"
  ],
  "keywords": [
    "demo"
  ],
  "authors": [
    "Haixuan"
  ],
  "license": "MIT",
  "ignore": [
    "**/.*",
    "node_modules",
    "bower_components",
    "test",
    "tests"
  ],
  "dependencies": {
    "jquery": "~2.1.3",
    "angular": "~1.3.15"
  }
}
```

## Step 2. Upload your Bower Package to a Git Repository


For example, you can upload it to Github or Gitlab hosted privately. We will configure bower to pull packages from this repository later.

You can add a [.gitignore](https://github.com/bower/bower/blob/master/.gitignore) file to your project.


### Add version tag to package repository

When installing packages from a repository, bower will use **version tags** to find the paticular revision of code to pull down. 

```bash
$ git tag -a v1.0.0 -m 'version 1.0.0'
$ git tag // verify tags added
$ git push origin master --tags // push tags to remote repository
```

## Step 3. Register Bower Package to a Private Registry

[Bower-private](https://github.com/Hacklone/private-bower) is a node application that provides convenient commandline and RESTful APIs to work with a private Bower registry.

```bash
$ sudo npm install -g bower-private
$ bower-private // start private registry server
```

The private bower registry server will run at http://localhost:5678. You can open it in the browser and see an empty list of packages.

Configure your bower package:

```bash
$ cd /path/to/bower/package
$ vim .bowerrc
```

Add the following content to `.bowerrc` file:

``` json
{
	"registry": "http://localhost:5678",
	"timeout": 300000
}
```

Register your bower package privately:

``` bash
$ bower register bower-sample http://your-git-host.com/bower-sample.git
```

You should be able to see your package now appearing in the web UI.

## Step 4. Install a Privately Registered Package

Create a sample consumer project:

``` bash
$ mkdir /path/bower-consumer
$ cd /path/bower-consumer
$ bower init
```

Add entry `\"bower-demo\": \"1.0.0\"` to the `dependencies` section in the `bower.json` file.

Configure bower to use your private registry:

``` bash
$ vim .bowerrc
```

Add the following content to the `.bowerrc` file:

``` json
{
	"registry": "http://localhost:5678",
	"timeout": 300000
}

```

Install all dependencies:

``` bash
$ bower install
```

Verify that packages are installed at `./bower_components`.
