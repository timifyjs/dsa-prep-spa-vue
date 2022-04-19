<template>
  <div class="container">
    <q-splitter v-model="splitterModelX" style="height:90vh">
      <template v-slot:before>
        <div
          style=" display:flex;
          flex-direction:row;
          justify-content:space-between;
          margin-top:10px"
        >
          <!-- <q-select
                  style="width:100px"
                  label="Language"
                  :options="langOptions"
                  v-model="selectedLang"
                /> -->
          <!-- <q-select
                  style="width:100px;"
                  label="Theme"
                  :options="themeOptions"
                  v-model="selectedTheme"
                  @input="setTheme"
                /> -->
          <q-btn flat icon="light_mode" style="color:">
            <q-menu transition-show="jump-down" transition-hide="jump-up">
              <q-list style="max-width:100px;">
                <q-item
                  style="display:grid; place-items:center;width: 100px;"
                  clickable
                  v-close-popup
                  @click="setTheme('naisuTheme')"
                >
                  <q-item-label>naisu</q-item-label>
                </q-item>
                <q-item
                  style="display:grid; place-items:center;width: 100px;"
                  clickable
                  v-close-popup
                  @click="setTheme('vs')"
                >
                  <q-item-label>vs</q-item-label>
                </q-item>
                <q-item
                  style="display:grid; place-items:center;width: 100px;"
                  clickable
                  v-close-popup
                  @click="setTheme('vs-dark')"
                >
                  <q-item-label>vs-dark</q-item-label>
                </q-item>
                <q-item
                  style="display:grid; place-items:center;width: 100px;"
                  clickable
                  v-close-popup
                  @click="setTheme('hc-black')"
                >
                  <q-item-label>hc-black</q-item-label>
                </q-item>
              </q-list>
            </q-menu>
          </q-btn>

          <q-btn flat>
            <img :src="langLogos[selectedLang]" style="width:50px" />
            <q-menu transition-show="jump-down" transition-hide="jump-up">
              <q-list>
                <q-item
                  style="width: max-content;"
                  clickable
                  v-close-popup
                  @click="changeLang('cpp')"
                >
                  <img src="../assets/cpp.svg" style="width:50px" />
                </q-item>
                <q-item
                  style="width: max-content;"
                  clickable
                  v-close-popup
                  @click="changeLang('c')"
                >
                  <img src="../assets/c.svg" style="width:50px" />
                </q-item>
                <q-item
                  style="width: max-content;"
                  clickable
                  v-close-popup
                  @click="changeLang('js')"
                >
                  <img src="../assets/javascript.svg" style="width:50px" />
                </q-item>
                <q-item
                  style="width: max-content;"
                  clickable
                  v-close-popup
                  @click="changeLang('python')"
                >
                  <img src="../assets/python.svg" style="width:50px" />
                </q-item>
                <q-item
                  style="width: max-content;"
                  clickable
                  v-close-popup
                  @click="changeLang('java')"
                >
                  <img src="../assets/java.svg" style="width:50px" />
                </q-item>
              </q-list>
            </q-menu>
          </q-btn>
        </div>
        <div ref="editors" class="editors">
          <div :style="`position:relative; height:${editorHeight}px`">
            <transition name="blur-fade">
              <div class="loading" v-if="isLoading">
                <q-spinner-tail color="primary" size="5em" />
                <q-tooltip :offset="[0, 8]">compiling...</q-tooltip>
              </div>
            </transition>
            <CustomEditor
              header-name="editor.exe"
              v-model="code"
              :theme="selectedTheme"
              :language="selectedLang"
              @run="run"
              :key="updateEditor"
            />
            <!-- <div class="editor-header">Editor</div> -->
            <!-- <div class="monaco-container">
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
                    theme="vs-dark"
                    v-model="code"
                    :options="{ fontSize: 20, readOnly: true }"
                    :language="selectedLang"
                    @editorDidMount="editorMounted"
                  />
                </div> -->
            <!-- <CustomEditor v-model="editorConfig" /> -->
            <q-btn
              round
              icon="play_arrow"
              color="green"
              size="lg"
              style="position:absolute; bottom:50px; right: 80px"
              @click="run"
            />
          </div>
          <!-- <template v-slot:separator>
              <q-avatar
                color="primary"
                text-color="white"
                size="40px"
                icon="drag_indicator"
              />
            </template> -->
        </div>
      </template>
      <template v-slot:after>
        <div>
          <console
            header-name="console.exe"
            style="width:100%;"
            height="80vh"
            v-model="output"
            :consoleExpanded="true"
            :key="output"
            :theme="selectedTheme"
            :headerColor="output.length >= 1 ? 'green' : 'red'"
          />
          <!-- <div class="console-header">Console</div> -->
          <!-- <div class="monaco-container">
                  <div class="monaco-frame">
                    <div class="monaco-frame-header">
                      <div class="controls">
                        <div class="control close"></div>
                        <div class="control minimize"></div>
                        <div class="control maximize"></div>
                      </div>
                      <div class="title">console.exe</div>
                    </div>
                  </div>
                  <MonacoEditor
                    class="console"
                    theme="vs-dark"
                    style="height:250px"
                    v-model="output"
                    :options="{ fontSize: 20, automaticLayout: true }"
                    :language="selectedLang"
                    @editorDidMount="editorMounted"
                  />
                </div> -->
        </div>
      </template>
    </q-splitter>
  </div>
</template>

<script>
import axios from "axios";
// import MonacoEditor from 'vue-monaco'

import cLogo from "../assets/c.svg";
import cppLogo from "../assets/cpp.svg";
import javaLogo from "../assets/java.svg";
import jsLogo from "../assets/javascript.svg";
import pythonLogo from "../assets/python.svg";

import CustomEditor from "../components/CustomEditor.vue";
import Console from "../components/Console.vue";

export default {
  components: {
    CustomEditor,
    Console
    // MonacoEditor
  },

  data() {
    return {
      langLogos: {
        c: cLogo,
        cpp: cppLogo,
        java: javaLogo,
        python: pythonLogo,
        js: jsLogo
      },
      updateEditor: 0,
      splitterModelX: 50,
      splitterModelY: 70,
      editors: [],
      code: "",
      langOptions: ["c", "cpp", "python", "java", "javascript"],
      selectedLang: "cpp",

      themeOptions: ["vs", "vs-dark", "hc-black"],
      selectedTheme: "hc-black",

      isLoading: false,
      output: "",

      editorHeight: 750,
      consoleExpanded: false,

      thumbStyle: {
        right: "4px",
        borderRadius: "4px",
        backgroundColor: "#027be3",
        width: "15px",
        opacity: 0.75
      },

      barStyle: {
        right: "2px",
        borderRadius: "4px",
        backgroundColor: "#027be3",
        width: "15px",
        opacity: 0.2
      },

      RO: null
    };
  },
  watch: {
    code(val) {
      localStorage.setItem("code", val);
    },
    selectedLang(val) {
      localStorage.setItem("lang", val);
      console.log(val);
    }
  },
  mounted() {
    //get code from localstorage
    this.code = localStorage.getItem("code");
    this.selectedLang = localStorage.getItem("lang")
      ? localStorage.getItem("lang")
      : "c";
    this.updateEditor++;

    // literally took 3h to find out about ResizeOberver
    this.RO = new ResizeObserver(() => {
      this.editors.forEach(editor => {
        editor.layout();
      });
    }).observe(this.$refs.editors);
  },
  // beforeDestroy() {
  //   this.RO.unObserve(this.$refs.editors);
  // },
  methods: {
    changeLang(lang) {
      this.selectedLang = lang;
    },
    editorMounted(editor) {
      this.editors.push(editor);
      editor.onDidPaste(() => {
        console.log("what did you paste 👀");
      });
    },
    setTheme(theme) {
      this.selectedTheme = theme;
      monaco.editor.setTheme(this.selectedTheme);
    },
    run() {
      this.output = "";
      this.isLoading = true;
      axios
        .post("https://compiler.panipuri.tech/playground", {
        // .post("http://localhost:3333/playground", {
          code: this.code,
          lang: this.selectedLang
        })
        .then(res => {
          console.log(res);
          // this.output = res.data.stdout || res.data.stderr || "";
          this.output = JSON.stringify(res.data.stdout);
          // console.log(this.output)
          this.isLoading = false;

          if (res.data.error) {
            this.output = `error: ${res.data.error}`;
            return;
          }
        })
        .catch(err => {
          console.log(err);
          this.isLoading = false;
          this.output = "error: " + err.message;
        });
    },
    handleConsoleExpansion(isExpanded) {
      this.consoleExpanded = isExpanded;
      this.editorHeight = isExpanded ? 550 : 750;
    }
  }
};
</script>

<style scoped>
.container {
  color: white;
}
.row {
  width: 100vw;
  display: flex;
  flex-direction: row;
}
.col {
  display: flex;
  flex: 2;
  justify-content: center;
}
.left-section {
  padding: 20px;
}
.editors {
  height: 90%;
}
.monaco-container {
  /* background: linear-gradient(140deg, rgb(76, 200, 200), rgb(32, 32, 51)); */
  /* background: linear-gradient(150deg, #00d2ff 0%, #3a47d5 100%);; */
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
/* .theme-btn {
  border: 3px solid black;
  background: skyblue;
  margin: 5px;
  box-shadow: -5px 7px 0px 1px rgba(0, 0, 0, 1);
  -webkit-box-shadow: -5px 7px 0px 1px rgba(0, 0, 0, 1);
  -moz-box-shadow: -5px 7px 0px 1px rgba(0, 0, 0, 1);
}
.theme-btn:hover {
  box-shadow: -2px 4px 0px 1px rgba(0, 0, 0, 1);
} */
/* .language-btn {
  border: 3px solid black;
  margin: 5px;
  box-shadow: -5px 7px 0px 1px rgba(0, 0, 0, 1);
  -webkit-box-shadow: -5px 7px 0px 1px rgba(0, 0, 0, 1);
  -moz-box-shadow: -5px 7px 0px 1px rgba(0, 0, 0, 1);
}
.language-btn:hover {
  box-shadow: -2px 4px 0px 1px rgba(0, 0, 0, 1);
} */

.blur-fade-enter-active,
.blur-fade-leave-active {
  transition: opacity 0.2s ease;
}
.blur-fade-enter, .blur-fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
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
.console-header {
  height: 50px;
  max-width: 300px;
  margin-top: 30px;
  border: 3px solid black;
  background: yellowgreen;
  font-size: 2em;
  margin: 10px 5px;
  box-shadow: -5px 7px 0px 1px rgba(0, 0, 0, 1);
  -webkit-box-shadow: -5px 7px 0px 1px rgba(0, 0, 0, 1);
  -moz-box-shadow: -5px 7px 0px 1px rgba(0, 0, 0, 1);
}
.q-menu {
  background: rgba(252, 0, 0, 0.596);
  backdrop-filter: blur(4px);
}
.loading {
  background: rgba(255, 255, 255, 0.05);
  backdrop-filter: blur(2px);
  position: absolute;
  width: 100%;
  height: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 10;
}
</style>
