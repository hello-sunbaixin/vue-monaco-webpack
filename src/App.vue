<template>
  <div>
    <!-- Load from webpack (note the srcPath="dist" prop) -->
    <Monaco
        height="600"
        language="json"
        srcPath="/dist"
        :code="code"
        :options="options"
        :changeThrottle="500"
        theme="vs"
        @mounted="onMounted"
        @codeChange="onCodeChange"
        :jsonSchemas="jsonSchemas"
        >
    </Monaco>
  </div>
</template>

<script>
const Monaco = require('./Monaco.vue')
const jsonSchemas=[require('./schemas/test1.json'),require('./schemas/test2.json')]
module.exports = {
  components: {
    Monaco
  },
  data() {
    return {
      code: '// type your code \n',
      jsonSchemas
    };
  },
  methods: {
    onMounted(editor) {
      console.log('after mount!', editor, editor.getValue(), editor.getModel());
      this.editor = editor;
    },
    onCodeChange(editor) {
      console.log('code changed!', 'code:' + this.editor.getValue());
    }
  },
  created() {
    this.options = {
      selectOnLineNumbers: false
    };
  }
};
</script>

<style media="screen">
  .secondary-highlighted-line {
    background: green;
  }
  .primary-highlighted-line {
    background: blue;
  }
</style>
