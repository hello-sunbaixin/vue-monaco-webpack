<template>
    <div :style="style"></div>
</template>
<script>
var debounce = require('lodash.debounce');
var monacoLoader = require('./MonacoLoader');

module.exports = {
    props: {
        width: { type: [String, Number], default: '100%' },
        height: { type: [String, Number], default: '100%' },
        code: { type: String, default: '// code \n' },
        srcPath: { type: String },
        language: { type: String, default: 'json' },
        theme: { type: String, default: 'vs-dark' }, // vs, hc-black
        options: { type: Object, default: () => {} },
        changeThrottle: { type: Number, default: 0 },
        jsonSchemas: { type: Array }
    },
    mounted() {
        if (this.language == 'json' && this.jsonSchemas && this.jsonSchemas.length > 0) {
            this.id = "schema";
            this.JSON_SCHEMAS = this.jsonSchemas.map((item) => ({
                fileMatch: [this.id],
                uri: item['$id'],
                schema: item
            }))
        }
        this.fetchEditor();
    },
    destroyed() {
        this.destroyMonaco();
    },
    computed: {
        style() {
            const { width, height } = this;
            const fixedWidth = width.toString().indexOf('%') !== -1 ? width : `${width}px`;
            const fixedHeight = height.toString().indexOf('%') !== -1 ? height : `${height}px`;
            return {
                width: fixedWidth,
                height: fixedHeight,
            };
        },
        editorOptions() {
            if (window.createModel) {
                window.createModel.dispose();
            }
            if(this.language=='json'&&this.jsonSchemas&&this.jsonSchemas.length>0){
              window.createModel = monaco.editor.createModel(this.code, "json", this.id);
            }else{
              window.createModel=null;
            }
            
            return Object.assign({}, this.defaults, this.options, {
                value: this.code,
                language: this.language,
                theme: this.theme,
                model: createModel
            });
        }
    },
    data() {
        return {
            defaults: {
                selectOnLineNumbers: true,
                roundedSelection: false,
                readOnly: false,
                cursorStyle: 'line',
                automaticLayout: false,
                glyphMargin: true
            }
        }
    },
    watch: {
        language() {
            window.monaco.editor.setModelLanguage(this.editor.getModel(), this.language)
        }
    },
    methods: {
        editorHasLoaded(editor, monaco) {
            this.editor = editor;
            this.monaco = monaco;
            this.editor.onDidChangeModelContent(event =>
                this.codeChangeHandler(editor, event)
            );
            this.$emit('mounted', editor);
        },
        codeChangeHandler: function(editor) {
            if (this.codeChangeEmitter) {
                this.codeChangeEmitter(editor);
            } else {
                this.codeChangeEmitter = debounce(
                    function(editor) {
                        this.$emit('codeChange', editor);
                    },
                    this.changeThrottle
                );
                this.codeChangeEmitter(editor);
            }
        },
        fetchEditor() {
            monacoLoader.load(this.srcPath,this.createMonaco);
        },
        createMonaco() {
            this.language == 'json' && monaco.languages.json.jsonDefaults.setDiagnosticsOptions({
                allowComments: true,
                schemas: this.JSON_SCHEMAS,
                validate: true
            });
            this.editor = window.monaco.editor.create(this.$el, this.editorOptions);
            this.editorHasLoaded(this.editor, window.monaco);
        },
        destroyMonaco() {
            if (typeof this.editor !== 'undefined') {
                this.editor.dispose();
            }
        }
    }
};
</script>