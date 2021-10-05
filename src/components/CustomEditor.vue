<template>
  <MonacoEditor
    class="editor"
    theme="vs-dark"
    v-model="code"
    :options="options"
    :language="selectedLang"
    @editorDidMount="editorMounted"
  />
</template>

<script>
export default {
  props: ["selectedLang", 'options'],
  data(){
    return{
      code:''
    }
  },
  methods: {
    editorMounted() {
      this.$emit("editorMounted");
    }
  }
};
</script>

<style></style>
