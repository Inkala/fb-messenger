# ReactJS Facebook messenger

The goal of this exercise is to learn how to start testing in JavaScript.

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

### Part 1

#### Configure Webpack

- In config/paths.js, edit the file and add the following line after `appBuild`
    `serverBuild: resolveApp('build/server'),`
- In config/paths.js, edit the following export key `appIndexJs` to be `appIndexJs: resolveApp('src/client/index.js'),`
- In config/paths.js, edit the file and add the following line after `appIndexJs`
    `serverIndexJs: resolveApp('src/server/index-dev.js'),`

- Copy webpack.config.dev.js and name it webpack.config.server.base.js
- Comment line 48: require.resolve('react-dev-utils/webpackHotDevClient'),
- In webpack.config.dev.js, replace in the entry `paths.appIndexJs` for `paths.serverIndexJs`
- In webpack.config.dev.js, comment the `ouput`. We are setting the output in webpack.config.server.js
- In webpack.config.dev.js, comment the `plugin` ModuleScopePlugin. This is because the fake api needs to import mocks from public/static/mocks.
- In webpack.config.dev.js, replace the module.rules `test: /\.css$/,` by:
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
- In webpack.config.dev.js, comment all the plugins:

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


## Bonus

...


## Links

-[https://martinfowler.com/bliki/TestPyramid.html](https://martinfowler.com/bliki/TestPyramid.html)
- [https://facebook.github.io/jest/docs/en/expect.html#content](https://facebook.github.io/jest/docs/en/expect.html#content)
- [https://martinfowler.com/articles/mocksArentStubs.html](https://martinfowler.com/articles/mocksArentStubs.html)
- [https://medium.com/javascript-scene/mocking-is-a-code-smell-944a70c90a6a](https://medium.com/javascript-scene/mocking-is-a-code-smell-944a70c90a6a)
- [https://www.youtube.com/watch?v=EZ05e7EMOLM](https://www.youtube.com/watch?v=EZ05e7EMOLM)

## License

This material is available for private, non-commercial use under the [GPL version 3](http://www.gnu.org/licenses/gpl-3.0-standalone.html).
