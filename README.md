# vue-monaco-webpack

## 简介
基于monaco-editor改造成支持vue版本，并且使用webpack通用打包工具，最重要是如果编辑的是json，
支持json-schema的校验。
## 安装

``` bash
npm install vue-monaco-webpack --save
```

## 基础用法

```js
import MonacoEditor from 'vue-monaco-webpack'

// use in component
export default {
  components: {
    MonacoEditor
  }
}
```

## 组件支持传递的属性

| Option        | Type          | Default | Description
|:-------------|:-------------|:-------|:-------|
| language      | String        | `javascript` | |
| height        | Number/String | `100%` ||
| width | Number/String | `100%` ||
| code | String | `// code \n` | Initial code to show |
| theme | String | `vs-dark` | vs, hc-black, or vs-dark |
| jsonSchemas | Array[Object] | schema的格式见代码实例 | json-schemas|
|srcPath|String|/dist|monoca-editor最终被打包到的文件夹
| changeThrottle | Number(ms) | `0` |  throttle `codeChange` emit |
| editorOptions | Object | Merged with defaults below | See [Monaco Editor Options](https://microsoft.github.io/monaco-editor/api/interfaces/monaco.editor.ieditorconstructionoptions.html) |

### 编辑器默认选项
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

## 组件支持的事件


| Event        | Returns          | Description
|:-------------|:-------------|:-------|
|mounted|`editor`[editor instance]|Emitted when editor has mounted|
|codeChange|`editor`[editor instance]|Emitted when code has changed|

## 使用实例

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
      jsonSchemas//不是必传，用于json校验
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


## Webpack使用

默认情况下，monaco-editor通过cdn的方式require，如果使用webpack的话，我们需要把依赖的monaco-editor拷贝到最终打包的目录，保证能正确引用到。

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

`srcPath` 注意填上最终打包后的文件夹名称

## 开发方式

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