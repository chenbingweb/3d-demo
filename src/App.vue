<script setup lang="ts">
// This starter template is using Vue 3 <script setup> SFCs
// Check out https://vuejs.org/api/sfc-script-setup.html#script-setup
import HelloWorld from "./components/HelloWorld.vue";
import Demo1 from "./components/demo-1.vue";
import Demo2 from "./components/demo-2.vue";
import Demo3 from "./components/demo-3.vue";
import { reactive, ref } from "vue";
interface IDemo {
  [key: string]: boolean;
}
let obj = reactive<IDemo>({
  one: false,
  two: false,
  three: true,
});
const onchange = (type: string) => {
  for (let key in obj) {
    obj[key] = false;
  }
  obj[type] = true;
};
</script>

<template>
  <div class="box">
    <div class="demo_panel">
      <Demo1 v-if="obj.one"></Demo1>
      <Demo2 v-if="obj.two"></Demo2>
      <Demo3 v-if="obj.three"></Demo3>
    </div>
    <div class="btn_box">
      <div class="button" @click="onchange('one')">Demo-1</div>
      <div class="button" @click="onchange('two')">Demo-2</div>
      <div class="button" @click="onchange('three')">Demo-3</div>
    </div>
  </div>
</template>

<style scoped>
.box {
  display: flex;
  justify-content: center;
  align-content: center;
  width: 100%;
  height: 100vh;
}
.demo_panel {
  width: 70%;
  height: 100vh;
  background: red;
}
.btn_box {
  width: 30%;
  height: 100vh;
  /* background: yellow; */
  overflow-y: scroll;
  box-sizing: border-box;
  padding: 20px;
}
.button {
  width: 100%;
  line-height: 80px;
  text-align: center;
  color: #333;
  font-size: 30px;
  background-color: #fff;
  border: 1px solid #333333;
  margin-bottom: 20px;
}
</style>
