<!--
 * @Author: XUNYUYU
 * @Date: 2024-03-27 17:56:55
 * @LastEditors: Do not edit
 * @LastEditTime: 2024-04-22 13:20:19
 * @FilePath: \graduation_design\src\App.vue
-->

<template>
  <!--
  在template标签中写内容的时候，一定要先写一个标签。仔仔合格标签中写内容
  如果含有HTML标签，需要使用v-html指令
  v-html指令的作用是：向指定节点中渲染包含HTML标签的文本内容
  -->
  <div id="app">
    <router-view></router-view>
  </div>
</template>

<script>

// 导入HelloWorld组件
export default {
  name: 'App',
  components: {
    
  },
}
</script>

<style>
.layout {
    height: 100%;
}
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  /* text-align: center; */
  color: #2c3e50;
  margin: -8px -8px auto;
  height: 100%;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
}

</style>
