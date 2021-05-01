1. Read about webpack on [webpack.js.org](https://webpack.js.org/)
2. Init npm project: `npm init -y`
3. Install webpack `npm i -D webpack webpack-cli`
4. Install TypeScript or Babel `npm i -D typescript ts-loader`
5. Add tsconfig.json `tsc --init` (read about tsconfig options [here](https://www.typescriptlang.org/tsconfig))
6. Create webpack.config.js [webpack with ts](https://webpack.js.org/guides/typescript/)
```js
  const path = require('path');

  module.exports = {
    entry: './src/index.ts',
    output: {
      filename: 'bundle.js',
      path: path.resolve(__dirname, 'dist'),
    },
    module: {
      rules: [
        {
          test: /\.[tj]s$/,
          use: 'ts-loader',
          exclude: /node_modules/,
        },
      ],
    },
    resolve: {
      extensions: ['.ts', '.js'],
    },
  };
```
7. Configure assets
```js
module.exports = {
  output: {
    filename: '[name].[contenthash].js',
    path: path.resolve(__dirname, 'dist'),
    assetModuleFilename: 'assets/[hash][ext]',
  },
  module: {
    rules: [
      {
        test: /\.(?:ico|gif|png|jpg|jpeg|svg)$/i,
        type: 'asset/resource',
      },
      {
        test: /\.(woff(2)?|eot|ttf|otf)$/i,
        type: 'asset/resource',
      },
    ],
  },
}
```
8. Add _HtmlWebpackPlugin_ `npm i -D html-webpack-plugin` [plugin documentation](https://github.com/jantimon/html-webpack-plugin#options)
```js
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  plugins: [
    new HtmlWebpackPlugin({
      title: 'Hello world',
      // template: './src/index.html',
    }),
  ]
}
```
9. Add CSS and SASS `npm i -D css-loader sass-loader sass mini-css-extract-plugin`
```js
  const MiniCssExtractPlugin = require('mini-css-extract-plugin');

  module.exports = {
    module: {
      rules: [
        {
          test: /\.css$/i,
          use: [MiniCssExtractPlugin.loader, 'css-loader'],
        },
        {
          test: /\.s[ac]ss$/i,
          use: [MiniCssExtractPlugin.loader, 'css-loader', 'sass-loader']
        }
      ],
    },
    plugins: [
      new MiniCssExtractPlugin({ filename: '[name].[contenthash].css' }),
    ],
  }
```
10. Add Clean Webpack Plugin `npm i -D clean-webpack-plugin`
```js
  const { CleanWebpackPlugin } = require('clean-webpack-plugin');

  module.exports = {
    plugins: [
      new CleanWebpackPlugin({ cleanStaleWebpackAssets: false }),
    ]
  };
```
11. Add environment variables ([guide](https://webpack.js.org/guides/environment-variables/))
```js
  module.exports = (env) => ({
    mode: env.development ? 'development' : 'production',
  });
```
Also add new script to package.json: `"dev": "webpack --env development"`.
12. Configure source maps ([documentation](https://webpack.js.org/configuration/devtool/))
```js
  module.exports = ({ development }) => ({
    devtool: development ? 'inline-source-map' : false,
  }
```
13. Add dev server ([documentation](https://webpack.js.org/configuration/dev-server/))
```js
  const devServer = (isDev) => !isDev ? {} : {
    devServer: {
      open: true,
      hot: true,
      port: 8080,
      contentBase: path.join(__dirname, 'public'),
    },
  };

  module.exports = (env) => ({
    ...devServer(env.development)
  });
```
14. Add Copy webpack plugin `npm i -D copy-webpack-plugin`
```js
  const CopyPlugin = require('copy-webpack-plugin');

  module.exports = {
    plugins: [
      new CopyPlugin({
        patterns: [
          { from: 'public' },
        ],
      }),
    ]
  };
```
15. Install [es-lint plugin](https://webpack.js.org/plugins/eslint-webpack-plugin/): `npm i -D eslint-webpack-plugin eslint`

+ Also install es-lint plugin for typescript and air-bnb config: `npm i -D eslint-plugin-import eslint-config-airbnb-typescript @typescript-eslint/parser @typescript-eslint/eslint-plugin`

+ .eslintrc:
```json
  {
    "env": {
      "browser": true,
      "es2021": true
    },
    "extends": [
      "airbnb-typescript/base",
      "eslint:recommended",
      "plugin:@typescript-eslint/recommended"
    ],
    "parser": "@typescript-eslint/parser",
    "parserOptions": {
      "project": "./tsconfig.json"
    },
    "plugins": [
      "@typescript-eslint"
    ]
  }
```
+ Add script to package.json: `"lint": "eslint src"`
+ webpack.config.js:
```js
  const ESLintPlugin = require('eslint-webpack-plugin');
  const esLintPlugin = (isDev) => isDev ? [] : [ new ESLintPlugin({ extensions: ['ts', 'js'] }) ];

  module.exports = ({ development }) => ({
    plugins: [
      ...esLintPlugin(development),
    ]
  });
```

16. Finally, we have next webpack config:
```js
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const { CleanWebpackPlugin } = require('clean-webpack-plugin');
const CopyPlugin = require('copy-webpack-plugin');
const ESLintPlugin = require('eslint-webpack-plugin');

const devServer = (isDev) => !isDev ? {} : {
  devServer: {
    open: true,
    hot: true,
    port: 8080,
    contentBase: path.join(__dirname, 'public'),
  },
};

const esLintPlugin = (isDev) => isDev ? [] : [ new ESLintPlugin({ extensions: ['ts', 'js'] }) ];

module.exports = ({ development }) => ({
  mode: development ? 'development' : 'production',
  devtool: development ? 'inline-source-map' : false,
  entry: {
    main: './src/index.ts',
  },
  output: {
    filename: '[name].[contenthash].js',
    path: path.resolve(__dirname, 'dist'),
    assetModuleFilename: 'assets/[hash][ext]',
  },
  module: {
    rules: [
      {
        test: /\.[tj]s$/,
        use: 'ts-loader',
        exclude: /node_modules/,
      },
      {
        test: /\.(?:ico|gif|png|jpg|jpeg|svg)$/i,
        type: 'asset/resource',
      },
      {
        test: /\.(woff(2)?|eot|ttf|otf)$/i,
        type: 'asset/resource',
      },
      {
        test: /\.css$/i,
        use: [MiniCssExtractPlugin.loader, 'css-loader'],
      },
      {
        test: /\.s[ac]ss$/i,
        use: [MiniCssExtractPlugin.loader, 'css-loader', 'sass-loader']
      }
    ],
  },
  plugins: [
    ...esLintPlugin(development),
    new MiniCssExtractPlugin({ filename: '[name].[contenthash].css' }),
    new HtmlWebpackPlugin({ title: 'Hello World' }),
    new CopyPlugin({
      patterns: [
        { from: 'public' },
      ],
    }),
    new CleanWebpackPlugin({ cleanStaleWebpackAssets: false }),
  ],
  resolve: {
    extensions: ['.ts', '.js'],
  },
  ...devServer(development)
});

```
