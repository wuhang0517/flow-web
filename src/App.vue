<template>
  <div id="app">
    <div style="width: 400px">
      <el-button @click="toggleTab('sign')"><a>渠道维护</a></el-button>
      <el-button @click="toggleTab('attr')"><a>属性维护</a></el-button>
      <el-button @click="toggleTab('module')"><a>模式维护</a></el-button>
    </div>
    <div style="width: 100%;height: 80%;margin-top: 30px;">
      <!--    // keep-alive 将切换出去的组件保留在内存中-->
      <prince :is="currentTab" keep-alive></prince>
    </div>
  </div>
</template>

<script>
import sign from "@/components/sign"
import attr from "@/components/attr";
import module from "@/components/module";

export default {
  name: 'App',
  data() {
    return {
      currentTab: 'sign'
    }
  },
  components: {  // 声明子组件
    sign,
    attr,
    module
  },
  methods: {
    toggleTab: function (tab) {
      this.currentTab = tab;  // tab 为当前触发标签页的组件名
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
