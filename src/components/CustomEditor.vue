<template>
  <div class="monaco-container" ref="container">
    <div class="monaco-frame">
      <div class="monaco-frame-header">
        <div class="controls">
          <div class="control close"></div>
          <div class="control minimize"></div>
          <div class="control maximize"></div>
        </div>
        <div class="title">editor.exe</div>
      </div>
    </div>
    <MonacoEditor
      class="editor"
      :theme="theme"
      :style="`height:${height}`"
      v-model="cCode"
      :options="{ fontSize: fontSize, readOnly: readOnly }"
      :language="language"
      @editorDidMount="editorMounted"
    />
  </div>
</template>

<script>
import MonacoEditor from "vue-monaco";

export default {
  components: {
    MonacoEditor
  },
  props: {
    value: {
      type: String,
      required: true
    },
    height: {
      type: String,
      required: true
    },
    theme: {
      type: String,
      required: true
    },
    language: {
      type: String,
      required: true
    },
    width: {
      type: String,
      required: false
    },
    fontSize: {
      type: Number,
      default: 20
    },
    readOnly: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      cCode: "",
      editor: null
    };
  },
  mounted() {
    // literally took 3h to find out about ResizeOberver
    this.RO = new ResizeObserver(() => {
      this.editor.layout();
    }).observe(this.$refs.container);

    this.cCode = this.value;


     monaco.editor.defineTheme("naisuTheme", {
        base: "hc-black", // can also be vs-dark or hc-black
        inherit: true, // can also be false to completely replace the builtin rules
        colors: {
          "editor.background": "#00000055"
        },
        rules: [
          {
            token: "comment",
            foreground: "ffa500",
            fontStyle: "italic underline"
          },
          { token: "comment.js", foreground: "008800", fontStyle: "bold" },
          { token: "comment.css", foreground: "0000ff" } // will inherit fontStyle from `comment` above
        ]
      });
      monaco.editor.setTheme("naisuTheme");
      console.log(monaco.editor)
  },
  methods: {
    editorMounted(editor) {
      this.editor = editor;

      this.editor.onDidChangeModelContent(e => {
        this.$emit("update", this.cCode);
      });
    }
  }
};
</script>

<style scoped>
.monaco-container {
  /* background: linear-gradient(140deg, rgb(76, 200, 200), rgb(32, 32, 51)); */
  /* background: linear-gradient(150deg, #00d2ff 0%, #3a47d5 100%);; */
  padding:20px;
  padding-top:0px ;
}
.monaco-frame {
  position: relative;
  height: 100%;
  width: 100%;
  background-color: #000000cc;
  border-radius: 12px 12px 0px 0px;
  padding: 16px;
  transform-style: preserve-3d;
}
.monaco-frame-header {
  display: grid;
  grid-template-columns: 60px auto 60px;
  height: 15px;
}

.controls {
  display: flex;
  z-index: 2;
}
.control {
  width: 12px;
  height: 12px;
  border-radius: 6px;
  background-color: rgba(255, 255, 255, 0.17);
  margin-right: 8px;
}
.title {
  text-align: center;
  color: white;
}
.editor {
  border-radius: 0px 0px 10px 10px;
}
.editor-header {
  height: 50px;
  max-width: 300px;
  margin-top: 30px;
  border: 3px solid black;
  background: orange;
  font-size: 2em;
  margin: 10px 5px;
  box-shadow: -5px 7px 0px 1px rgba(0, 0, 0, 1);
  -webkit-box-shadow: -5px 7px 0px 1px rgba(0, 0, 0, 1);
  -moz-box-shadow: -5px 7px 0px 1px rgba(0, 0, 0, 1);
}
.monaco-editor-background{
  backdrop-filter: blur(4px);
}
</style>
