<template>
  <div class="containers">
    <div class="canvas" ref="canvas"></div>
    <div id="js-properties-panel" class="panel"></div>
    <div class="modal" v-if="bpmnNodeVisible" @click="close">
      <div class="modal-content">
        <div class="modal-ctx">
          <div class="modal-item">
            节点id: {{ bpmnNodeInfo.id }}
          </div>
          <div class="modal-item">
            节点type: {{ bpmnNodeInfo.type }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
// 引入相关的依赖
import { xmlStr } from '../mock/xmlStr'
import CustomModeler from './customModeler'
import { mapState, mapMutations } from 'vuex'
export default {
  name: '',
  components: {},
// 生命周期 - 创建完成（可以访问当前this实例）
  created() {},
// 生命周期 - 载入后, Vue 实例挂载到实际的 DOM 操作完成，一般在该过程进行 Ajax 交互
  mounted() {
    this.init()
  },
  data() {
    return {
        // bpmn建模器
        bpmnModeler: null,
        container: null,
        canvas: null
    }
  },
// 方法集合
  methods: {
    ...mapMutations(['TOGGLENODEVISIBLE']),
    init () {
    // 获取到属性ref为“canvas”的dom节点
    const canvas = this.$refs.canvas
    // 建模
    this.bpmnModeler = new CustomModeler({
      container: canvas,
      //添加控制板
      propertiesPanel: {
        parent: '#js-properties-panel'
      },
      additionalModules: []
    })
    this.createNewDiagram()
	},
    createNewDiagram () {
        // 将字符串转换成图显示出来
        this.bpmnModeler.importXML(xmlStr, (err) => {
            if (err) {
                // console.error(err)
            }
            else {
                // 这里是成功之后的回调, 可以在这里做一系列事情
                this.success()
            }
        })
    },
    success () {
        // console.log('创建成功!')
    },
    close () {
      // window.localStorage.setItem('nodeVisible', 'false')
      this.TOGGLENODEVISIBLE(false)
    }
  },
// 计算属性
  computed: {
    ...mapState({
      bpmnNodeInfo: state => state.bpmn.nodeInfo,
      bpmnNodeVisible: state => state.bpmn.nodeVisible
    })
    // bpmnNodeInfo () {
    //   return JSON.parse(window.localStorage.getItem('nodeInfo'))
    // },
    // bpmnNodeVisible () { // 好像不能监听到它的改变, 我就弃用了
    //   console.log(JSON.parse(window.localStorage.getItem('nodeVisible')))
    //   return JSON.parse(window.localStorage.getItem('nodeVisible'))
    // }
  }
}
</script>

<style scoped>
.containers{
	background-color: #ffffff;
	width: 100%;
	height: calc(100vh - 52px);
}
.canvas{
	width: 100%;
	height: 100%;
}
.panel{
	position: absolute;
	right: 0;
	top: 0;
	width: 300px;
}
.modal {
  background-color: rgba(0, 0, 0, 0.6);
  width: 100vw;
  height: 100vh;
  position: fixed;
  top: 0;
  left: 0;
}
.modal-content {
  width: 100%;
  height: 100%;
  position: relative;
}
.modal-ctx {
  position: absolute;
  width: 300px;
  height: 250px;
  background-color: #fff;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  box-shadow: 0px 0px 5px 2px rgba(225, 225, 225, 0.8);
}
.modal-item {
  padding: 10px;
  width: 100%;
}
</style>