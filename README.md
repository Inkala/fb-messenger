# ReactJS Facebook messenger

The goal of this exercise is to learn how to do Server Side Rendering (SSR) in React.

## To get started

### Step 1

If you haven't already set up your project, head here and follow the instructions https://github.com/leanjscom/fb-messenger/blob/master/README.md


### Step 2
```sh
 git checkout SSR
 ```

### Step 3
```sh
 yarn install
 ```

## Exercise

### Exercise part 1

#### Configure Webpack

- In config/paths.js, edit the file and add the following line after `appBuild`:
  ```serverBuild: resolveApp('build/server'),```
- In config/paths.js, edit the following export key `appIndexJs` to be:
  ```appIndexJs: resolveApp('src/client/index.js'),```
- In config/paths.js, edit the file and add the following line after `appIndexJs`:
    ```serverIndexJs: resolveApp('src/server/index-dev.js'),```
- Copy webpack.config.dev.js and name it webpack.config.server.base.js
- In config/paths.js, comment line 48: require.resolve('react-dev-utils/webpackHotDevClient'),
- In webpack.server.base.js, replace in the entry `paths.appIndexJs` for `paths.serverIndexJs`
- In webpack.server.base.js, comment the `ouput`. We are setting the output in webpack.config.server.js
- In webpack.server.base.js, comment the `plugin` ModuleScopePlugin. This is because the fake api needs to import mocks from public/static/mocks.
- In webpack.server.base.js, replace the module.rules `test: /\.css$/,` by:
```
{
  test: /\.css$/,
  use: [
    'isomorphic-style-loader',
    {
      loader: 'css-loader',
      options: {
        importLoaders: 1
      }
    }
  ]
},
```
- In webpack.server.base.js, comment all the plugins:

#### Edit scripts/start.js

Make the following edits in scripts/start.js :
- `const { exec } = require('child_process')` at the beginnig of the file
- Replace this line `const compiler = createCompiler(webpack, config, appName, urls, useYarn);` with `const compiler = webpack(config)`
- Replace
```
if (isInteractive) {
  clearConsole();
}
console.log(chalk.cyan('Starting the development server...\n'));
openBrowser(urls.localUrlForBrowser);
```
with
```
process.env.REACT_APP_CLIENT_PORT = port
process.env.REACT_APP_SERVER_PORT = 8888
const configWebpackServer = require('../config/webpack.config.server')
const compiler = webpack(configWebpackServer)
const urls = prepareUrls(protocol, HOST, process.env.REACT_APP_SERVER_PORT)
let isServerRunning

compiler.watch({}, (err, stats) => {
  if (err) console.log('error on webpack server', err)

  if (!isServerRunning) {
    isServerRunning = true
    const nodemon = exec(
      'nodemon --watch build/server build/server/index.js build/server/index.js'
    )

    // This is to outpout in the terminal the child process
    nodemon.stdout.on('data', (data) => {
      console.log(data.toString())
    })
    nodemon.on('exit', (code) => {
      console.log(`nodemon process exited with code ${code.toString()}`)
    })

    console.log(chalk.yellow(`Starting the server on port ${process.env.REACT_APP_SERVER_PORT}...\n`))
    setTimeout(
      () => {
        openBrowser(urls.localUrlForBrowser)
      },
      1000
    )
  }
})
```

### Exercise part 2

1. Edit server/render.js so it uses the renderToString function. Hint, in what part of the HTML should React render your app?

2. Edit server/render.js so it sends the CSS to the page. Hint, read how SSR works in styled-components [https://www.styled-components.com/docs/advanced#server-side-rendering](https://www.styled-components.com/docs/advanced#server-side-rendering)

3. Edit server/render.js so it sends the JavaScript bundle to the client. Hint, Webpack is not creating a file on the disk so don't search for a bundle.js file in your disk. Webpack is generating and serving the file from memory (so it's faster).

4. Can you fix this warning: `render(): Calling ReactDOM.render() to hydrate server-rendered markup will stop working in React v17. Replace the ReactDOM.render() call with ReactDOM.hydrate() if you want React to attach to the server HTML.`. Hint, you should use a function called hydrate.

5. If you disable JS in your browser, why there are no messages rendered from the server? Can you fix that so the server renders a list of messages when JS is disabled on the client?

### Exercise part 3, Redux:

Before you start part 2, please checkout branch part-2-redux

1. Please edit server/app.js so the server side passes a store to the Root component.

2. How can you send the Thread and the Conversation from the server-side so they are displayed on the client when JS is disabled on the client? Hint, you need to pass some initial state to the store on the server-side.

3. Can you fix this warning? Warning: Did not expect server HTML to contain a &lt;img&gt; in &lt;div&gt;. Hint, the problem is that the initial state on the server is different than the initial state on the client. You need to edit just this file:  src/client/index.js. Have a look at src/server/render to get some clues about what you need to add to src/client/index.js

## Bonus

Let's consolidate our knowledge by making our FB Messenger clone Server Side Rendering from scratch. Checkout this branch [https://github.com/leanjscom/fb-messenger/tree/redux-leanjs-part2](https://github.com/leanjscom/fb-messenger/tree/redux-leanjs-part2) and make the project SSR.

## Links

- [https://medium.com/leanjs/universal-create-react-app-step-by-step-b80ba68d125d](https://medium.com/leanjs/universal-create-react-app-step-by-step-b80ba68d125d)

## License

This material is available for private, non-commercial use under the [GPL version 3](http://www.gnu.org/licenses/gpl-3.0-standalone.html).
