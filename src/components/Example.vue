<template>
  <div class="container">
    <div>
      <div>Input:</div>
      <div style="padding-left:20px">{{ testCase.input }}</div>
      <div>Output:</div>
      <div style="padding-left:20px">{{ testCase.output }}</div>
      <div v-if="testCase.explaination">Explaination</div>
      <div>{{ testCase.explaination }}</div>
    </div>
  </div>
</template>

<script>
export default {
  props: ["testCase"]
};
</script>

<style scoped>
.container {
  height: 200px;
  width: 100%;
  font-size: 20px;
  padding: 20px;
  backdrop-filter: blur(4px) saturate(180%);
  -webkit-backdrop-filter: blur(16px) saturate(180%);
  background-color: rgba(17, 25, 40, 0.75);
  border-radius: 12px;
  border: 1px solid rgba(255, 255, 255, 0.125);
}
</style>
