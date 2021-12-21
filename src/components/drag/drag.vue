<template>
  <div class="drag" ref="drag">
    <div class="drag__content" ref="dragContent">
      <div
        class="drag__content-item"
        v-for="(v, i) in movesList"
        :key="i"
        @touchmove="touchmoveHandle(i, $event)"
        @touchend="touchendHandle"
        @touchstart="touchstartHandle(i, $event)"
        ref="dragContentItem"
      >
        <img :src="v.src" alt="" />
        <span v-if="v.text">{{ v.text }}</span>
      </div>
      <!-- 动画的效果 -->
      <div class="drag__content-animation" ref="dragContentAnimation">
        <img src="" alt="" />
        <span></span>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'drag',
  props: {
    movesList: Array,
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
  methods: {
    // touchstart
    touchstartHandle(i, e) {
      clearTimeout(this.tuodong.timer)
      // 定时器的作用是为了达到长按的效果
      this.tuodong.timer = setTimeout(() => {
        this.movesList2 = JSON.parse(JSON.stringify(this.movesList))
        //
        this.tuodong.startBoo = true
        this.handle(i, e)
      }, 500)
    },
    // touchmove
    touchmoveHandle(i, e) {
      e.preventDefault()
      if (this.tuodong.startBoo) {
        this.handle(i, e)
      }
    },
    // touchend
    touchendHandle() {
      if (this.tuodong.boo) {
        this.$refs.dragContentItem.forEach(v => {
          v.style.opacity = 1
        })
        let dragContentAnimationEle = this.$refs.dragContentAnimation
        dragContentAnimationEle.style = ''
        this.tuodong.boo = false
        console.log(this.movesList)
      }
      clearTimeout(this.tuodong.timer)
      this.tuodong.startBoo = false
      this.tuodong.opacityBoo = true
    },
    handle(i, e) {
      // 最外层元素
      let dragEle = this.$refs.drag
      // 父盒子
      let dragContentEle = this.$refs.dragContent
      // 子盒子(需要移动的盒子) dragContentItemEle
      let dragContentItemEle = this.$refs.dragContentItem[i]
      // 左的padding
      let paddingX = parseInt(window.getComputedStyle(dragEle)['paddingLeft'])
      // 子盒子自身的高度
      let childH = dragContentItemEle.offsetHeight
      // 子盒子自身的宽度
      let childW = dragContentItemEle.offsetWidth
      // 父盒子距离顶部的距离
      let parentY = dragContentEle.offsetTop
      // 行数
      let row = 2
      // 列数
      let col = 4
      // 手指在盒子中x轴的位置
      let touchX = e.touches[0].pageX - paddingX
      // 手指在盒子中Y轴的位置
      let touchY = e.touches[0].pageY - parentY
      // 动画及其样式开始 dragContentAnimationEle
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
      dragContentAnimationEle.style.transition = 'all 0.06s'
      if (this.tuodong.opacityBoo) {
        dragContentItemEle.style.opacity = 0
      }
      // 动画里的子元素
      let myImg = dragContentAnimationEle.firstElementChild
      let mySpan = dragContentAnimationEle.lastElementChild
      myImg.src = this.movesList2[i].src
      mySpan.innerHTML = this.movesList2[i].text
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
      let indexEnd = ''
      if (indexY === 0) {
        indexEnd = indexX
      } else if (indexY === 1) {
        indexEnd = indexX + col
      }
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
      }, 50)
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
.drag {
  padding: 15px;

  &__content {
    background: #fff;
    /* !!! */
    height: calc(145rem / 37.5);
    border-radius: 0.10667rem;
    box-shadow: 0 0.21333rem 0.42667rem 0 rgba(0, 0, 0, 0.06);
    overflow: hidden;
    position: relative;

    &-item {
      float: left;
      width: 25%;
      height: 50%;
      .flex-center();
    }

    &-animation {
      position: absolute;
      width: 25%;
      height: 50%;
      background-color: #cbdacb67;
      border-radius: 15px;
      z-index: 9999;
      opacity: 0;
      pointer-events: none;
      .flex-center();
    }

    &-item,
    &-animation {
      img {
        width: 33px;
      }

      span {
        font-size: 14px;
        color: #6d7582;
        width: 100%;
        text-align: center;
        white-space: nowrap;
        text-overflow: ellipsis;
        overflow: hidden;
      }
    }
  }
}
</style>
