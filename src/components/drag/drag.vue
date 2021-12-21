<template>
  <div class="drag__content" ref="dragContent">
    <div
      class="drag__content-item"
      :style="contentItemStyle"
      v-for="(v, i) in movesList"
      :key="i"
      @touchmove="touchmoveHandle(i, $event)"
      @touchend="touchendHandle"
      @touchstart="touchstartHandle(i, $event)"
      ref="dragContentItem"
    >
      <div class="drag__content-item-container">
        <img :src="v.src" alt="" />
        <span v-if="v.text">{{ v.text }}</span>
      </div>
    </div>
    <!-- 动画的效果 -->
    <div
      class="drag__content-animation"
      ref="dragContentAnimation"
      :style="contentAnimationStyle"
    >
      <div
        class="drag__content-animation-container"
        ref="dragContentAnimationContainer"
      >
        <img src="" alt="" />
        <span></span>
      </div>
    </div>
  </div>
</template>

<script>
// 递归获取元素到body的距离
function offset(ele) {
  let left = 0,
    top = 0
  function fn(e) {
    if (e) {
      if (e.offsetParent === document.body) {
        left += e.offsetLeft
        top += e.offsetTop
      } else {
        left += e.offsetLeft
        top += e.offsetTop
        fn(ele.offsetParent)
      }
    }
  }
  fn(ele)
  return {
    left,
    top,
  }
}
export default {
  name: 'drag',
  props: {
    // 图标数据
    movesList: Array,
    // 列
    col: {
      type: Number,
      default: 3,
    },
  },
  data() {
    return {
      movesList2: [],
      time: null,
      tuodong: {
        timer: null, // 定时器的开关
        startBoo: false, // 代表长按事件的开关
        index: '',
        boo: false, // 控制只有触发touchmove后，才会触发touchend里的代码
        arrI: [],
        opacityBoo: true,
      },
    }
  },
  computed: {
    // 展示的图标元素style
    // 根据col列数和父元素的宽度计算得出图标的宽度，然后让高度和宽度相等
    contentItemStyle() {
      return {
        width: `${100 / this.col}%`,
        paddingTop: `${100 / this.col}%`,
      }
    },
    // 动画元素style
    contentAnimationStyle() {
      return {
        width: `${100 / this.col}%`,
        paddingTop: `${100 / this.col}%`,
      }
    },
    // row行数根据数据的数量和col列数计算得出
    row() {
      let row = 1
      if (this.movesList && Array.isArray(this.movesList) && this.col) {
        row = Math.ceil(this.movesList.length / this.col)
      }
      return row
    },
  },
  methods: {
    // touchstart
    touchstartHandle(i, e) {
      e.preventDefault()
      clearTimeout(this.tuodong.timer)
      // 定时器的作用是为了达到长按的效果
      this.tuodong.timer = setTimeout(() => {
        // 拷贝一份数据 制作动画时用
        this.movesList2 = JSON.parse(JSON.stringify(this.movesList))
        // 设为true 代表已经达到的长按的效果，可以执行touchmove里面的代码了
        this.tuodong.startBoo = true
        // 调不调用这个函数，都能达到拖动效果，只是调用这个函数，长按达标后，会有动画浮动的效果
        this.handle(i, e)
      }, 500)
    },
    // touchmove
    touchmoveHandle(i, e) {
      // 取消浏览器默认事件 （有时在屏幕上移动会触发滚动条）
      e.preventDefault()
      // 判断是否符合长按标准，达标后可执行拖动
      if (this.tuodong.startBoo) {
        this.handle(i, e)
      }
    },
    // touchend
    touchendHandle() {
      /*
        这个tuodong.boo 是在touchmove事件里面设为true的，
        只有为true，在松开手指时，才会触发下面if语句里的代码
      */
      if (this.tuodong.boo) {
        this.$refs.dragContentItem.forEach(v => {
          v.style.opacity = 1
        })

        // 清除动画效果 （动画元素初始css样式 opacity为0）
        let dragContentAnimationEle = this.$refs.dragContentAnimation
        dragContentAnimationEle.style.opacity = 0
        dragContentAnimationEle.style.left = ''
        dragContentAnimationEle.style.top = ''
        dragContentAnimationEle.style.transition = ''
        // 执行完if语句代码 将开关置为false 只有再次满足长按事件才会触发这里的代码
        this.tuodong.boo = false
        // !!!打印下拖动后的数据 最后要把这个数据返出去
        console.log(this.movesList)
      }
      clearTimeout(this.tuodong.timer)
      // 松开手指后，将长按事件的开关设为false
      this.tuodong.startBoo = false
      this.tuodong.opacityBoo = true
    },
    handle(i, e) {
      // 父盒子
      let dragContentEle = this.$refs.dragContent
      // 子盒子(需要移动的盒子)
      let dragContentItemEle = this.$refs.dragContentItem[i]
      // 子盒子自身的高度
      let childH = dragContentItemEle.offsetHeight
      // 子盒子自身的宽度
      let childW = dragContentItemEle.offsetWidth
      // 父盒子距离顶部的距离
      let parentY = offset(dragContentEle).top
      // 父盒子距离顶部的距离
      let parentX = offset(dragContentEle).left
      // 行数
      let row = this.row
      // 列数
      let col = this.col
      // 手指在盒子中x轴的位置
      let touchX = e.touches[0].pageX - parentX
      // 手指在盒子中Y轴的位置
      let touchY = e.touches[0].pageY - parentY
      // 动画及其样式开始
      let dragContentAnimationEle = this.$refs.dragContentAnimation
      let cirX = touchX - dragContentAnimationEle.offsetWidth / 2
      let cirY = touchY - dragContentAnimationEle.offsetHeight / 2
      if (cirX >= childW * (col - 1)) {
        cirX = childW * (col - 1)
      } else if (cirX <= 0) {
        cirX = 0
      }
      if (cirY >= childH * (row - 1)) {
        cirY = childH * (row - 1)
      } else if (cirY <= 0) {
        cirY = 0
      }
      dragContentAnimationEle.style.opacity = 0.8
      dragContentAnimationEle.style.left = cirX + 'px'
      dragContentAnimationEle.style.top = cirY + 'px'
      dragContentAnimationEle.style.transition = 'all 0.04s'
      if (this.tuodong.opacityBoo) {
        dragContentItemEle.style.opacity = 0
      }
      // 动画里的子元素
      let dragContentAnimationContainerEle =
        this.$refs.dragContentAnimationContainer
      let myImg = dragContentAnimationContainerEle.firstElementChild
      let mySpan = dragContentAnimationContainerEle.lastElementChild
      myImg.src = this.movesList2[i].src
      mySpan.innerHTML = ''
      this.movesList2[i].text && (mySpan.innerHTML = this.movesList2[i].text)
      // 动画小圆点及其样式结束
      // 换成坐标索引
      let indexX = parseInt(touchX / childW)
      let indexY = parseInt(touchY / childH)
      // 边界
      if (indexY >= row - 1) {
        indexY = row - 1
      } else if (indexY <= 0) {
        indexY = 0
      }
      if (indexX >= col - 1) {
        indexX = col - 1
      } else if (indexX <= 0) {
        indexX = 0
      }
      // 最终索引
      let indexEnd = indexX + indexY * col
      this.tuodong.index = indexEnd
      this.tuodong.boo = true
      clearTimeout(this.time)
      this.time = setTimeout(() => {
        if (this.tuodong.boo) {
          if (this.tuodong.opacityBoo) {
            this.tuodong.arrI = this.movesList.splice(i, 1)
          } else {
            let hh = this.movesList.findIndex(
              v => v.text === this.tuodong.arrI[0].text,
            )
            this.movesList.splice(hh, 1)
          }
          this.movesList.splice(this.tuodong.index, 0, ...this.tuodong.arrI)
          dragContentItemEle.style.opacity = 1
          this.tuodong.opacityBoo = false
          this.$refs.dragContentItem.forEach((v, i) => {
            if (i === indexEnd) {
              v.style.opacity = 0
            } else {
              v.style.opacity = 1
            }
          })
        }
      }, 40)
    },
  },
}
</script>
<style lang="less" scoped>
.flex-center() {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
}
/* 拖动部分样式开始 */
.drag__content {
  background: #fff;
  border-radius: 4px;
  box-shadow: 0 16px 32px 0 rgba(0, 0, 0, 0.06);
  overflow: hidden;
  position: relative;

  &-item {
    float: left;
    position: relative;

    &-container {
      position: absolute;
      width: 100%;
      height: 100%;
      left: 0;
      top: 0;
      .flex-center();
    }
  }

  &-animation {
    position: absolute;
    background-color: #cbdacb67;
    border-radius: 4px;
    z-index: 9999;
    opacity: 0;
    pointer-events: none;

    &-container {
      position: absolute;
      width: 100%;
      height: 100%;
      left: 0;
      top: 0;
      .flex-center();
    }
  }

  &-item,
  &-animation {
    img {
      width: 33px;
      user-select: none;
    }

    span {
      font-size: 14px;
      color: #6d7582;
      width: 100%;
      text-align: center;
      white-space: nowrap;
      text-overflow: ellipsis;
      overflow: hidden;
      user-select: none;
    }
  }
}
</style>
