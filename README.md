# Observations

1. Original at [Create a Walt Project From Scratch in 5 Minutes](https://github.com/ballercat/walt/wiki/Walt-In-5-Minutes)
2. Everything went according to the tutorial. Only that I skipped the step to install first:

    ```
    npm i walt-loader
    npm install --save walt-compiler
    ```
3. Walt is written using [nearley](https://nearley.js.org/)
4. Worked with both v12 and v14 of node.js

# Create a Walt Project From Scratch in 5 Minutes
Walt's ultimate goal is to make WebAssembly accessible to regular JavaScript programmers and to do so in a sane and simple fashion. There are only a few prerequisites and most of the necessary tools you most likely have already installed.

Here is a small step-by-step tutorial to get from an empty project directory to loading WebAssembly in about five minutes:

## Prerequisites: node 8+ and npm

There are many different ways you could install these and manage the rapidly changing versions. I personally recommend installing `n` (https://github.com/tj/n) as a node version manager.

## Prepare the project structure

In a new empty folder create a simple folder structure for your new project

```
├── dist
└─┬ src
  └── walt
```

## Create a `package.json` file

```
npm init -y
```

This will create a new package.json file. npm will prefill it with the most useful default values.

## Install Webpack

```
npm install --save-dev webpack
```

While it is possible to create a Walt project without Webpack, this will make your life much easier. Walt comes with a Webpack loader that makes regular recompilation a breeze :wind_face:

## Install the Walt compiler and Webpack loader

```
npm install --save-dev walt-compiler walt-loader
```

## Webpack Configuration

Create a new file in your project folder and save it as `webpack.config.js`

```js
const path = require('path')

module.exports = {
  resolve: {
    extensions: [".walt"]
  },
  module: {
    rules: [
      { test: /\.walt$/, use: 'walt-loader' }
    ]
  },
  entry: './src/index.js',
  mode: 'production',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist')
  }
}

```

## Create the landing page

Create a new file in your project folder and save it as `index.html`

```html
<html>
<head>
  <title>Getting Started</title>
</head>
<body>
  <script src="dist/bundle.js"></script>
</body>
</html>
```

## Add the JavaScript scaffolding

Create a new file in your project folder and save it as `src/index.js`

```js
import makeCounter from './walt/counter.walt'

makeCounter().then(wasmModule => {
  console.log(wasmModule.instance.exports.increment()) // 1
  console.log(wasmModule.instance.exports.increment()) // 2
  console.log(wasmModule.instance.exports.decrement()) // 1
})
```

## Add a Walt file

Create a new file in the src/walt folder of your project and save it as counter.walt

```walt
let counter: i32 = 0;

export function decrement(): i32 {
  counter -= 1;
  return counter;
}

export function increment(): i32 {
  counter += 1;
  return counter;
}
```

This would probably also be a good time to register `.walt` files in your OS to open with your favorite editor.

## Review

At this point you should have files in directories arranged like this

```
├── dist
├── node_modules
├─┬ src
│ ├─┬ walt
│ │ └── counter.walt
│ └── index.js
├── index.html
├── package-lock.json
├── package.json
└── webpack.config.js
```

## Build the project

Use Webpack to create the `bundle.js` file in the `dist` folder

```
npx webpack --config webpack.config.js
```

## Load your results

When you open `index.html` in a browser it will load the `bundle.js` and execute it. Open the JavaScript console and you should see

```
1
2
1
```

Congratulations! You just ran your first WebAssembly script!
