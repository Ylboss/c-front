<!--
 * @Author: XUNYUYU
 * @Date: 2024-03-27 17:56:55
 * @LastEditors: Do not edit
 * @LastEditTime: 2024-03-27 18:18:59
 * @FilePath: \graduation_design\src\components\HelloWorld.vue
-->
<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    
  </div>
</template>

<template>
  <div class="hello">
    <h1>{{ msg }}</h1>
    <el-button>{{ msg }}</el-button>
  </div>
</template>
<script>
export default {
  name: 'HelloWorld',
  props: {
    msg: String
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
