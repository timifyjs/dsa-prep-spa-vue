<template>
  <div class="container">
    <q-splitter v-model="splitterModelX" style="height:90vh">
      <template v-slot:before>
        <div>
          <h1>left Section</h1>
          <q-btn label="run" @click="isLoading = !isLoading" />
        </div>
      </template>
      <template v-slot:after>
        <div ref="editors" class="editors">
          <q-splitter horizontal v-model="splitterModelY">
            <template v-slot:before>
              <div
                style=" display:flex; flex-direction:row; justify-content:space-between"
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
                <q-btn-dropdown color="primary" icon="light_mode">
                  <q-list>
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
                </q-btn-dropdown>
                <q-btn-dropdown color="primary" label="Language">
                  <q-list>
                    <q-item clickable v-close-popup @click="changeLang('c++')">
                      <q-item-section>
                        <img src="../assets/c++.svg" style="width:50px" />
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

                    <q-item clickable v-close-popup @click="changeLang('java')">
                      <q-item-section>
                        <img src="../assets/java.svg" style="width:50px" />
                      </q-item-section>
                    </q-item>
                  </q-list>
                </q-btn-dropdown>
              </div>
              <div>
                <transition name="blur-fade">
                  <div class="loading" v-if="isLoading">
                    <q-spinner-tail color="primary" size="5em" />
                    <q-tooltip :offset="[0, 8]">compiling...</q-tooltip>
                  </div>
                </transition>
                <MonacoEditor class="editor" theme="vs-dark" automaticLayout:
                true v-model="code" :options="{ fontSize: 20, readOnly: true }"
                :language="selectedLang" @editorDidMount="editorMounted" />
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
            <template v-slot:separator>
              <q-avatar
                color="primary"
                text-color="white"
                size="40px"
                icon="drag_indicator"
              />
            </template>
            <template v-slot:after>
              <div>
                <transition name="blur-fade">
                  <div class="loading" v-if="isLoading">
                    <q-spinner-tail color="primary" size="5em" />
                    <q-tooltip :offset="[0, 8]">compiling...</q-tooltip>
                  </div>
                </transition>
                <MonacoEditor
                  class="console"
                  theme="vs-dark"
                  style="height:250px"
                  v-model="output"
                  :options="{ fontSize: 20, automaticLayout: true }"
                  :language="selectedLang"
                  @editorDidMount="editorMounted"
                />
              </div>
            </template>
          </q-splitter>
        </div>
      </template>
    </q-splitter>
  </div>
</template>

<script>
import MonacoEditor from "vue-monaco";
import axios from "axios";

export default {
  components: {
    MonacoEditor
  },

  data() {
    return {
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

      isLoading: true,
      output: "",
      RO: null
    };
  },
  mounted() {
    // literally took 3h to find out out ResizeOberver
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
  max-height: 100% !important;
  margin: 0;
  padding: 0;
  overflow: hidden;
}
.console {
  overflow: hidden;
}
.options {
  display: flex;
  flex-direction: row;
}
/* .blur-fade-enter, .blur-fade-leave-to{
  background: rgba(255, 255, 255, 0);
  backdrop-filter: blur(0px);
}
.blur-fade-enter-to, .blur-fade-leave{
  background: rgba(255, 255, 255, 0.5);
  backdrop-filter: blur(10px);
}
.blur-fade-enter-active .blur-fade-leave-active{
  transition: all .3s ease;
} */
.blur-fade-enter-active,
.blur-fade-leave-active {
  transition: opacity 0.5s ease;
}
.blur-fade-enter, .blur-fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
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
