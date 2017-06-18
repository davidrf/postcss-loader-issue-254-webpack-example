# postcss-loader issue 254 webpack example

## Steps to reproduce

```
$ git clone https://github.com/davidrf/postcss-loader-issue-254-webpack-example.git
$ cd postcss-loader-issue-254-webpack-example
$ yarn install
$ yarn start
```

## Expected result

1. Bundle file created.
2. No errors shown in terminal.
3. Created bundle file contains CSS from both `src/a.css` and `src/b.css`.
4. CSS from `src/a.css` includes vendor prefixes for `display: flex`.

## Actual result

1. Bundle file created.
2. Following error is shown:

```
yarn start v0.24.5
$ node_modules/.bin/webpack
Hash: 72fe85f23e9462ff3bd1
Version: webpack 2.6.1
Time: 1036ms
    Asset     Size  Chunks             Chunk Names
bundle.js  19.5 kB       0  [emitted]  main
   [0] ./~/css-loader?{"modules":true,"importLoaders":1}!./~/postcss-loader/lib?{"plugins":[null]}!./src/b.css 725 bytes {0} [built] [failed] [1 error]
   [1] ./src/a.css 1.18 kB {0} [built]
   [2] ./~/css-loader?{"modules":true,"importLoaders":1}!./~/postcss-loader/lib?{"plugins":[null]}!./src/a.css 679 bytes {0} [built]
   [3] ./~/css-loader/lib/css-base.js 2.26 kB {0} [built]
   [4] ./~/style-loader/lib/addStyles.js 8.7 kB {0} [built]
   [5] ./~/style-loader/lib/urls.js 3.01 kB {0} [built]
   [6] ./src/entry.js 18 bytes {0} [built]

ERROR in ./~/css-loader?{"modules":true,"importLoaders":1}!./~/postcss-loader/lib?{"plugins":[null]}!./src/b.css
Module build failed: TypeError: Cannot read property 'postcss' of null
    at Processor.normalize (/Users/David/Programming/postcss-loader-issue-254-webpack-example/node_modules/postcss-loader/node_modules/postcss/lib/processor.js:136:12)
    at new Processor (/Users/David/Programming/postcss-loader-issue-254-webpack-example/node_modules/postcss-loader/node_modules/postcss/lib/processor.js:51:25)
    at postcss (/Users/David/Programming/postcss-loader-issue-254-webpack-example/node_modules/postcss-loader/node_modules/postcss/lib/postcss.js:73:10)
    at Promise.resolve.then.then (/Users/David/Programming/postcss-loader-issue-254-webpack-example/node_modules/postcss-loader/lib/index.js:137:12)
 @ ./~/css-loader?{"modules":true,"importLoaders":1}!./~/postcss-loader/lib?{"plugins":[null]}!./src/a.css 3:10-170 10:35-195
 @ ./src/a.css
 @ ./src/entry.js
error Command failed with exit code 2.
```

3. Created bundle file contains CSS from only `src/a.css`.
4. CSS from `src/a.css` includes vendor prefixes for `display: flex`.

## Other observations

* Creating a `postcss.config.js` file in the root folder with the following:

    ```js
      module.exports = {
        plugins: [require('autoprefixer')()],
      }
    ```

    And replacing

    ```js
    {
      loader: 'postcss-loader',
      options: {
        plugins: [require('autoprefixer')()],
      },
    },
    ```

    with `'postcss-loader'` does produce the expected result, but the `postcss-loader`
    README still suggests that doing the configuration via Webpack should work as well.

* Adding `ident` by changing:

    ```js
    options: {
      plugins: [require('autoprefixer')()],
    },
    ```

    to

    ```js
    options: {
      ident: 'postcss',
      plugins: [require('autoprefixer')()],
    },
    ```

    Results in the expected result, but [ident should not be needed in webpack 2.2.1 and higher](https://webpack.js.org/guides/migrating/#complex-options)

* Changing `plugins: [require('autoprefixer')()]` to `plugins: []` results in:

    1. Bundle file created.
    2. No errors shown in terminal.
    3. Created bundle file contains CSS from both `src/a.css` and `src/b.css`.

* Removing `composes: b from './b.css';` from `src/a.css` results in:

    1. Bundle file created.
    2. No errors shown in terminal.
    3. Created bundle file contains CSS from only `src/a.css`.
    4. CSS from `src/a.css` includes vendor prefixes for `display: flex`.
