<template>
    <div class="container">


        <div class="result-view-1">{{ request }}</div>

        <!-- 替换 src -->
        <iframe class="ideogrpah-view" src="/ideograph.html" width="100%" height="100%" />

        <!-- 从后端返回的子图 JSON -->
        <div class="result-view-2">{{ response }}</div>


    </div>
</template>

<script>

import axios from 'axios'

const ideographApi = "/api/solveCompositePattern"

export default {
    data() {
        return {
            request: "从 iframe 收到的消息",
            response: "从后端接口返回的结果"
        }
    },
    mounted() {
        // 挂载 message 监听函数
        window.addEventListener("message", this.handleMessage)
    },
    methods: {
        // message 类型：MessageEvent<Solution.CompositePattern>
        // 见 ideograph-redux/src/services/PatternSolution.ts
        // 
        async handleMessage(message) {
            this.request = JSON.stringify(message.data)

            // POST 获取结果
            const solutions = (await axios.post(ideographApi, message.data)).data;

            this.response = JSON.stringify(solutions);
        }
    },
    unmounted() {
        // 回收 message 监听函数
        window.removeEventListener("message", this.handleMessage)
    }
}
</script>

<style>
.container {
    width: 100vw;
    height: 100vh;
    display: grid;
    grid-template-columns: 1fr 3fr;
    grid-template-rows: 1fr 3fr;
    font-size: 13px;
    font-family: ui-monospace, monospace;
}

.ideogrpah-view {
    grid-row-start: 1;
    grid-row-end: 3;
    grid-column: 2;
    border: none;
    appearance: none;
}

.result-view-1 {
    padding: 16px;
    overflow: auto;
    border-bottom: 1px solid #eee;
    border-right: 1px solid #eee;
}

.result-view-2 {
    padding: 16px;
    overflow: auto;
    border-right: 1px solid #eee;
}
</style>