# Options Reference

## Usage Difference Between Webpack 1 & 2

For Webpack 1.x: add a root `vue` block in your Webpack config:

``` js
module.exports = {
  // ...
  vue: {
    // vue-loader options
  }
}
```

For Webpack 2 (^2.1.0-beta.25):

``` js
module.exports = {
  // ...
  module: {
    rules: [
      {
        test: /\.vue$/,
        loader: 'vue',
        options: {
          // vue-loader options
        }
      }
    ]
  }
}
```

### loaders

- type: `Object`

  An object specifying Webpack loaders to use for language blocks inside `*.vue` files. The key corresponds to the `lang` attribute for language blocks, if specified. The default `lang` for each type is:

  - `<template>`: `html`
  - `<script>`: `js`
  - `<style>`: `css`

  For example, to use `babel-loader` and `eslint-loader` to process all `<script>` blocks:

  ``` js
  // ...
  vue: {
    loaders: {
      js: 'babel!eslint'
    }
  }
  ```

### postcss

- type: `Array` or `Function` or `Object`
- `Object` format only supported in ^8.5.0

  Specify custom PostCSS plugins to be applied to CSS inside `*.vue` files. If using a function, the function will called using the same loader context and should return an Array of plugins.

  ``` js
  // ...
  vue: {
    // note: do not nest the `postcss` option under `loaders`
    postcss: [require('postcss-cssnext')()],
    loaders: {
      // ...
    }
  }
  ```

  This option can also be an object that contains options to be passed to the PostCSS processor. This is useful when you are using PostCSS projects that relies on custom parser/stringifiers:

  ``` js
  postcss: {
    plugins: [...], // list of plugins
    options: {
      parser: sugarss // use sugarss parser
    }
  }
  ```

### cssSourceMap

- type: `Boolean`
- default: `true`

  Whether to enable source maps for CSS. Disabling this can avoid some relative path related bugs in `css-loader` and make the build a bit faster.

  Note this is automatically set to `false` if the `devtool` option is not present in the main Webpack config.

### esModule

- type: `Boolean`
- default: `undefined`

  Whether to emit esModule compatible code. By default vue-loader will emit default export in commonjs format like `module.exports = ....`. When `esModule` is set to true, default export will be transpiled into `exports.__esModule = true; exports = ...`. Useful for interoperating with transpiler other than Babel, like TypeScript.

### preserveWhitespace

- type: `Boolean`
- default: `true`

  If set to `false`, the whitespaces between HTML tags in templates will be ignored.

### transformToRequire

- type: `{ [tag: string]: string | Array<string> }`
- default: `{ img: 'src' }`

  During template compilation, the compiler can transform certain attributes, such as `src` URLs, into `require` calls, so that the target asset can be handled by Webpack. The default config transforms the `src` attribute on `<img>` tags.

### buble

- type: `Object`
- default: `{}`

  Configure options for `buble-loader` (if present). For example, to enable Object spread operator:

  ``` js
  vue: {
    buble: {
      objectAssign: 'Object.assign'
    }
  }
  ```

### templateBuble

- type: `Object`
- default:
  ``` js
  {
    target: { chrome: 52 },
    transforms: {
      stripWith: true, // vue only
      computedProperty: true,
      conciseMethodProperty: true,
      templateString: true
    }
  }
  ```

  Configure options for a [Buble](https://buble.surge.sh/) transpile pass applied to raw render functions. The purpose of this additional pass is to:

  1. add selective support to a few ES2015 features that are handy in template expressions (whitelisted in default transforms);

  2. remove the `with` block inside render functions to make it strict-mode compliant and a bit more performant. This is enabled by the custom `stripWith` transform, and applied only at build time so that the base template compiler can be extremely small and lightweight.

  With this option you can turn on additional [transforms](https://buble.surge.sh/guide/#supported-features) (e.g. arrow functions) or turn off `with` removal based on your specific needs. Example config with Webpack 2:

  ``` js
  module: {
    rules: [
      {
        test: /\.vue$/,
        loader: 'vue',
        options: {
          templateBuble: {
            objectAssign: 'Object.assign',
            // transforms object is merged with the defaults
            transforms: {
              stripWith: false,
              arrow: true
            }
          }
        }
      }
    ]
  }
  ```
