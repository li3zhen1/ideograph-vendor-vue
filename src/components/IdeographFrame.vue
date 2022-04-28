<template>
    <div class="container">

        <!-- 从 iframe 传递过来的 JSON -->
        <div class="result-view-1">{{ request }}</div>

        <!-- 替换 src -->
        <iframe class="ideogrpah-view" src="/ideograph.html" width="100%" height="100%" />

        <!-- 从后端返回的子图 JSON，需要开启后端 ideograph-server （默认的 8080 端口）-->
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
            /** solutions 结果类型:
             *  @type SolvePatternResponse = {
                    solutions: Solution.PatternSolution[],
                    elapsedTimeInMillis: number,
                    message: string | null | undefined
                }

                @export interface PatternSolution {
                    nodes: Record<string, WorkspaceNode>;
                    edges: Record<string, WorkspaceEdge>;
                }
             */

            this.response = JSON.stringify(solutions);
        }
    },
    unmounted() {
        // 回收 message 监听函数
        window.removeEventListener("message", this.handleMessage)
    }
}
/**
 * @description
 * 仓库内 public 目录下资源都是从 ideograph-redux 仓库 ** vendor ** 分支 build 产物 (dist目录) 下拷贝
 * 需要将 dist/index.html 更名为其他名字 *.html（对应 <iframe src="*"/> ）
 */
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