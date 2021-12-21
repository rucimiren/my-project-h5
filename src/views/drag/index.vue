<template>
  <div class="demo-drag">
    <drag :movesList="movesList" />
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
  methods: {},
}
</script>
<style lang="less" scoped>
.demo-drag {
  position: relative;
  margin-top: 200px;
  padding: 15px;
}
</style>
