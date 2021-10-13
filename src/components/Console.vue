<template>
  <div class="monaco-container" ref="container">
    <div class="monaco-frame" @click="handleExpandBtn">
      <div class="monaco-frame-header">
        <div class="controls">
          <div class="control close"></div>
          <div class="control minimize"></div>
          <div class="control maximize"></div>
        </div>
        <div class="title">{{ headerName }}</div>
        <div>
          <transition name="fade" mode="out-in">
            <q-btn
              flat
              v-if="consoleExpanded"
              round
              key="1"
              icon="expand_more"
            />
            <q-btn flat round key="2" v-else icon="expand_less" />
          </transition>
        </div>
      </div>
    </div>
    <q-slide-transition :duration='200' appear >
      <div v-show="consoleExpanded">
        <MonacoEditor
          class="editor"
          :theme="theme"
          :style="`height:${cHeight}`"
          v-model="cCode"
          :options="{
            fontSize: fontSize,
            readOnly: true,
            minimap: { enabled: false }
          }"
          :key="cHeight"
          @editorDidMount="editorMounted"
        />
      </div>
    </q-slide-transition>
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
    headerName: {
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
    width: {
      type: String,
      required: false
    },
    fontSize: {
      type: Number,
      default: 18
    },
    headerColor: {
      type: String,
      required: false,
      default: "#000000aa"
    }
  },
  data() {
    return {
      cCode: "",
      cHeight: null,
      editor: null,
      consoleExpanded: true
    };
  },
  mounted() {
    // literally took 3h to find out about ResizeOberver
    this.RO = new ResizeObserver(() => {
      this.editor.layout();
    }).observe(this.$refs.container);

    this.cCode = this.value;
    this.cHeight = this.height;
  },
  methods: {
    editorMounted(editor) {
      this.editor = editor;
      this.editor.minimap = { enabled: false };
      monaco.editor.defineTheme("naisuTheme", {
        base: "hc-black", // can also be vs-dark or hc-black
        inherit: true, // can also be false to completely replace the builtin rules
        colors: {
          "editor.background": "#00000050",
          "minimap.background": "#FF0000"
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
    },
    handleExpandBtn() {
      this.consoleExpanded = !this.consoleExpanded;
      if (this.consoleExpanded) {
        this.cHeight = 200;
        this.$emit("expanded", true);
      } else {
        this.cHeight = 0;
        this.$emit("expanded", false);
      }
    }
  }
};
</script>

<style scoped>
* {
  overflow: hidden;
}
.monaco-editor {
  border-radius: 30px;
}
.editor {
  height: 200px;
  border-radius: 0px 0px 10px 10px;
  /* border: 1px solid rgba(255, 255, 255, 0.125);
  border-top: 0px; */
}
.monaco-container {
  backdrop-filter: blur(4px);
  padding: 0px 15px;
  /* background: linear-gradient(140deg, rgb(76, 200, 200), rgb(32, 32, 51)); */
  /* background: linear-gradient(150deg, #00d2ff 0%, #3a47d5 100%);; */
}
.monaco-frame {
  position: relative;
  width: 100%;
  background-color: #000000aa;
  border-radius: 12px 12px 0px 0px;
  height: 45px;
  display: flex;
  align-items: center;
  /* border: 1px solid rgba(255, 255, 255, 0.25);
  border-bottom: 0px; */
}
.monaco-frame-header {
  width: 100%;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0px 16px;
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

/* .rotate-enter,
.rotate-leave-to {
  transform: rotate(90deg);
}
.rotate-enter-to,
.rotate-leave {
  transform: rotate(-90deg);
}
.rotate-enter-active,
.rotate-leave-active {
  transition: all 0.5s ease;
} */
.fade-enter-active,
.fade-leave-active {
  transition: all 0.2s;
}
.fade-enter {
  opacity: 0;
  transform: rotate(-90deg);
}
.fade-leave-to {
  opacity: 0;
  transform: rotate(90deg);
}
</style>
