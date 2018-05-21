# vue-monaco-webpack

> Based off [React Monaco Editor](https://github.com/superRaytin/react-monaco-editor)

> [![experimental](http://badges.github.io/stability-badges/dist/experimental.svg)](http://github.com/badges/stability-badges)

## Setup

``` bash
npm install vue-monaco-webpack --save
```

## Simple Vue Use

```js
import MonacoEditor from 'vue-monaco-webpack'

// use in component
export default {
  components: {
    MonacoEditor
  }
}
```

## Component Props

| Option        | Type          | Default | Description
|:-------------|:-------------|:-------|:-------|
| language      | String        | `javascript` | |
| height        | Number/String | `100%` ||
| width | Number/String | `100%` ||
| code | String | `// code \n` | Initial code to show |
| theme | String | `vs-dark` | vs, hc-black, or vs-dark |
| jsonSchemas | Array[Object] |见代码| json-schemas|
| changeThrottle | Number(ms) | `0` |  throttle `codeChange` emit |
| editorOptions | Object | Merged with defaults below | See [Monaco Editor Options](https://microsoft.github.io/monaco-editor/api/interfaces/monaco.editor.ieditorconstructionoptions.html) |

### Editor Default Options
```js
defaults: {
  selectOnLineNumbers: true,
  roundedSelection: false,
  readOnly: false,
  cursorStyle: 'line',
  automaticLayout: false,
  glyphMargin: true
}
```

## Component Events

*These events are available to parent component*

| Event        | Returns          | Description
|:-------------|:-------------|:-------|
|mounted|`editor`[editor instance]|Emitted when editor has mounted|
|codeChange|`editor`[editor instance]|Emitted when code has changed|

## Example

*Component Implementation*
```vue
<MonacoEditor
    height="600"
    language="typescript"
    :code="code"
    :editorOptions="options"
    :jsonSchemas="jsonSchemas"
    @mounted="onMounted"
    @codeChange="onCodeChange"
    >
</MonacoEditor>
```

*Parent*
```js
//如果需要json-schemas来校验，需要引入json-schema,json-schema中必须有$id属性
const jsonSchemas=[require('test1.json'),require('test2.json')]
module.exports = {
  components: {
    Monaco
  },
  data() {
    return {
      code: '// Type away! \n',
      options: {
      },
      jsonSchemas
    };
  },
  methods: {
    onMounted(editor) {
      this.editor = editor;
    },
    onCodeChange(editor) {
      console.log(editor.getValue());
    }
  }
};
```


## Webpack Use

By default, monaco-editor is loaded from a cdn asyncronously using `require`. To use a local copy of `monaco-editor` with webpack, we need to expose the dependency in our build directory:

`npm install copy-webpack-plugin --save-dev`

Add this to your webpack.config.js:

```js
const CopyWebpackPlugin = require('copy-webpack-plugin');
module.exports = {
  plugins: [
    new CopyWebpackPlugin([
      {
        from: 'node_modules/monaco-editor/min/vs',
        to: 'vs',
      }
    ])
  ]
};
```

Then, specify the build directory path in the `srcPath` prop. See `src/App.vue` for an example.

## Dev Use

```
git clone [this repo] .
npm install
npm run dev
```

Edit `src/App.vue`


## 说明
使用方法，可参见src／Monaco.vue 中使用方式

## 特色
支持可通过配置jsonSchemas，对编辑的json进行校验
