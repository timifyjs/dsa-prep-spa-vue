<template>
  <div class="container">
    <q-splitter v-model="splitterModelX" style="height:90vh">
      <template v-slot:before>
        <q-btn icon="undo" color="primary" @click="questionNum--" />
        <q-btn icon="forward" color="primary" @click="questionNum++" />
        <!-- <CustomEditor
          class="custom"
          :theme="selectedTheme"
          v-model="code"
          height="250px"
          @update="e => (code = e)"
        /> -->

        <div>
          <h3>
            #{{ questions[questionNum].num }}.
            {{ questions[questionNum].title }}
          </h3>
          <h5>Difficulty: {{ questions[questionNum].diffLevel }}</h5>
          <h4>{{ questions[questionNum].desc }}</h4>

          <q-btn label="run" @click="isLoading = !isLoading" />
        </div>
      </template>
      <template v-slot:after>
        <div ref="editors" class="editors">
          <q-splitter horizontal v-model="splitterModelY">
            <template v-slot:before>
              <div
                style=" display:flex; flex-direction:row; justify-content:space-between; margin-top:10px"
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
                <q-btn icon="light_mode" style="color:white">
                  <q-menu transition-show="jump-down" transition-hide="jump-up">
                    <q-list style="max-width:100px">
                      <q-item clickable v-close-popup @click="setTheme('vs')">
                        <q-item-section>
                          <q-item-label>vs</q-item-label>
                        </q-item-section>
                      </q-item>
                      <q-item
                        clickable
                        v-close-popup
                        @click="setTheme('vs-dark')"
                      >
                        <q-item-section>
                          <q-item-label>vs-dark</q-item-label>
                        </q-item-section>
                      </q-item>
                      <q-item
                        clickable
                        v-close-popup
                        @click="setTheme('hc-black')"
                      >
                        <q-item-section>
                          <q-item-label>hc-black</q-item-label>
                        </q-item-section>
                      </q-item>
                    </q-list>
                  </q-menu>
                </q-btn>

                <q-btn>
                  <img :src="langLogos[selectedLang]" style="width:50px" />
                  <q-menu transition-show="jump-down" transition-hide="jump-up">
                    <q-list style="max-width:100px">
                      <q-item
                        clickable
                        v-close-popup
                        @click="changeLang('cpp')"
                      >
                        <q-item-section>
                          <img src="../assets/cpp.svg" style="width:50px" />
                        </q-item-section>
                      </q-item>

                      <q-item clickable v-close-popup @click="changeLang('c')">
                        <q-item-section>
                          <img src="../assets/c.svg" style="width:50px" />
                        </q-item-section>
                      </q-item>

                      <q-item clickable v-close-popup @click="changeLang('js')">
                        <q-item-section>
                          <img
                            src="../assets/javascript.svg"
                            style="width:50px"
                          />
                        </q-item-section>
                      </q-item>

                      <q-item
                        clickable
                        v-close-popup
                        @click="changeLang('python')"
                      >
                        <q-item-section>
                          <img src="../assets/python.svg" style="width:50px" />
                        </q-item-section>
                      </q-item>

                      <q-item
                        clickable
                        v-close-popup
                        @click="changeLang('java')"
                      >
                        <q-item-section>
                          <img src="../assets/java.svg" style="width:50px" />
                        </q-item-section>
                      </q-item>
                    </q-list>
                  </q-menu>
                </q-btn>
              </div>
              <div>
                <transition name="blur-fade">
                  <div class="loading" v-if="isLoading">
                    <q-spinner-tail color="primary" size="5em" />
                    <q-tooltip :offset="[0, 8]">compiling...</q-tooltip>
                  </div>
                </transition>
                  <CustomEditor
                  v-model="code"
                  height="550px"
                  :theme="selectedTheme"
                  :language="selectedLang"
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
            </template>
            <!-- <template v-slot:separator>
              <q-avatar
                color="primary"
                text-color="white"
                size="40px"
                icon="drag_indicator"
              />
            </template> -->
            <template v-slot:after>
              <div>
                <transition name="blur-fade">
                  <div class="loading" v-if="isLoading">
                    <q-spinner-tail color="primary" size="5em" />
                    <q-tooltip :offset="[0, 8]">compiling...</q-tooltip>
                  </div>
                </transition>
                <CustomEditor
                  v-model="output"
                  height="250px"
                  readOnly
                  :theme="selectedTheme"
                  :language="selectedLang"
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
    </q-splitter>
  </div>
</template>

<script>
import axios from "axios";

import cLogo from "../assets/c.svg";
import cppLogo from "../assets/cpp.svg";
import javaLogo from "../assets/java.svg";
import jsLogo from "../assets/javascript.svg";
import pythonLogo from "../assets/python.svg";

import questions from "../questions";

import CustomEditor from "../components/CustomEditor.vue";

export default {
  components: {
    CustomEditor
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
      questions: questions,
      questionNum: 0,
      splitterModelX: 50,
      splitterModelY: 70,
      editors: [],
      code: `#include<stdio.h>
int main(){
for(int i=0;i<10;i++){
  printf("%d, ",i);
}
return 0;
}`,
      langOptions: ["c", "cpp", "python", "java", "javascript"],
      selectedLang: "c",

      themeOptions: ["vs", "vs-dark", "hc-black"],
      selectedTheme: "hc-black",

      isLoading: false,
      output: '',
      RO: null
    };
  },
  mounted() {
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
    },
    setTheme(theme) {
      this.selectedTheme = theme;
      monaco.editor.setTheme(this.selectedTheme);
    },
    run() {
      // this.isLoading = !this.isLoading;
      this.output = "";
      this.isLoading = true;
      // axios.post('http://localhost:3000/', { code: this.code, lang:this.selectedLang }).then((res) => {
      axios
        .post("https://compiler.plasmatch.in/", {
          code: this.code,
          lang: this.selectedLang
        })
        .then(res => {
          this.output = res.data.stdout || res.data.stderr;
          this.isLoading = false;
          console.log(res);
        });
    }
  }
};
</script>

<style scoped>
* {
  overflow: hidden;
}
.container {
  background-image: url("../assets/animated-bg.svg");
  background-repeat: no-repeat;
  background-size: 1920px 1080px;
  position: absolute;
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
.editors {
  overflow: hidden;
}
.editor {
  height: 550px;
  border-radius: 0px 0px 10px 10px;
}
.console {
  overflow: hidden;
  border-radius: 0px 0px 10px 10px;
}
.options {
  display: flex;
  flex-direction: row;
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
