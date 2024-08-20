<!--
 * @Author: XUNYUYU
 * @Date: 2024-04-13 23:39:05
 * @LastEditors: Do not edit
 * @LastEditTime: 2024-04-18 14:18:18
 * @FilePath: \graduation_design\src\views\Home.vue
-->
<template>
    <div>
        <div id="main" style="width: 500px; height: 400px;"></div>
    </div>
</template>
<script>
import * as echarts from 'echarts';
export default {
    name: 'Home',
    data() {
        return {

        }
    },
    mounted() {
        var myChart = echarts.init(document.getElementById('main'));
        myChart.setOption({
            title: {
                text: 'ECharts 入门示例'
            },
            tooltip: {},
            xAxis: {
                data: ['衬衫', '羊毛衫', '雪纺衫', '裤子', '高跟鞋', '袜子']
            },
            yAxis: {},
            series: [
                {
                    name: '销量',
                    type: 'bar',
                    data: [5, 20, 36, 10, 10, 20]
                }
            ]
        })
    }
}
</script>
<style scoped></style>