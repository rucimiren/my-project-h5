<template>
  <div class="demo-drag">
    <drag
      class="drag-component"
      :movesList="movesList"
      :iconTextStyle="iconTextStyle"
      :iconStyle="iconStyle"
      :itemContentStyle="itemContentStyle"
      @moveData="moveData"
    />
  </div>
</template>

<script>
import drag from '@/components/drag/drag.vue'
export default {
  name: 'demo-drag',
  components: {
    drag,
  },
  props: {},
  data() {
    return {
      iconTextStyle: {
        // fontSize: '20px',
        color: 'blue',
      },
      iconStyle: {
        width: '50%',
      },
      itemContentStyle: {
        // background: 'pink',
      },
      movesList: [
        {
          src: 'http://yaru.vip:8080/images/icon/ES6.png',
          text: 'es6',
        },
        {
          src: 'http://yaru.vip:8080/images/icon/js.png',
          text: 'javaScript',
        },
        {
          src: 'http://yaru.vip:8080/images/icon/git.png',
          // text: 'git',
        },
        {
          src: 'http://yaru.vip:8080/images/icon/ts.png',
          text: 'typeScript',
        },
        {
          src: 'http://yaru.vip:8080/images/icon/vue.png',
          text: 'vue',
        },
        {
          src: 'http://yaru.vip:8080/images/icon/more.png',
          text: 'more',
        },
        {
          src: 'http://yaru.vip:8080/images/icon/other.png',
          text: 'other',
        },
        {
          src: 'http://yaru.vip:8080/images/icon/react.svg',
          text: 'react',
        },
      ],
    }
  },
  methods: {
    moveData(data) {
      console.log('拖动后的数据', data)
    },
  },
}
</script>
<style lang="less" scoped>
.demo-drag {
  position: relative;
  margin-top: 100px;
  padding: 15px;
}
.drag-component {
  // background: chartreuse;
}
</style>
