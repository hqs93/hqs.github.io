---
title: Vue拖动组件封装
categories: 
- [web前端, Vue3, Vue拖动组件封装]
---

# Vue拖动组件封装

<!-- more -->

移动端拖拽解决方案:

**移动端无法使用drag来进行组件的拖拽操作!**

那么移动端如何实现拖拽效果呢？了解到移动端有`touch`事件。`touch`与`click`事件触发的先后顺序如下所示：

```ruby
touchstart => touchmove => touchend => click
```

通过`vue`为我们提供的`ref`属性,拿到dom,再为组件注册监听touch事件.



.vue代码:

```JavaScript
<template>
  <transition name="el-fade-in-linear">
    <div class="ys-float-btn" :style="{'width':itemWidth+'px','height':itemHeight+'px','left':left+'px','top':top+'px'}"
        ref="div"
        v-show="show"
        @click ="onBtnClicked">
        <img src="../../../static_ipad/img/annualReport/floatIcon.png" alt="" style="width: 100%; height: 100%;">
        <el-button type="text" class="el-icon-close close" @click.stop="onBtnClose" size="medium"></el-button>
      <!-- <slot name="icon"></slot>
      <p>{{text}}</p>
      <el-button class="el-icon-close" round type="text" @click.stop="onBtnClose"></el-button> -->
    </div>
  </transition>
</template>

<script>
export default {
  name: 'FloatBtn',
  props: {
    text: {
      type: String,
      default: '默认文字'
    },
    itemWidth: {
      type: Number,
      default: 100
    },
    itemHeight: {
      type: Number,
      default: 100
    },
    gapWidth: {
      type: Number,
      default: 10
    },
    coefficientHeight: {
      type: Number,
      default: 0.8
    }
  },
  created () {
    this.clientWidth = document.documentElement.clientWidth
    this.clientHeight = document.documentElement.clientHeight
    this.left = this.clientWidth - this.itemWidth - this.gapWidth
    this.top = this.clientHeight * this.coefficientHeight
  },
  mounted () {
    window.addEventListener('scroll', this.handleScrollStart)
    this.$nextTick(() => {
      const div = this.$refs.div
      div.addEventListener('touchstart', () => {
        div.style.transition = 'none'
      })
      div.addEventListener('touchmove', (e) => {
        if (e.targetTouches.length === 1) {
          let touch = event.targetTouches[0]
          this.left = touch.clientX - this.itemWidth / 2
          this.top = touch.clientY - this.itemHeight / 2
        }
      })
      div.addEventListener('touchend', () => {
        div.style.transition = 'all 0.3s'
        if (this.left > this.clientWidth / 2) {
          this.left = this.clientWidth - this.itemWidth - this.gapWidth
        } else {
          this.left = this.gapWidth
        }
      })
    })
  },
  beforeDestroy () {
    window.removeEventListener('scroll', this.handleScrollStart)
  },
  methods: {
    onBtnClicked () {
      this.$emit('onFloatBtnClicked')
    },
    onBtnClose () {
      // this.$refs.div.style.display = 'none'
      this.show = false
    },
    handleScrollStart () {
      this.timer && clearTimeout(this.timer)
      this.timer = setTimeout(() => {
        this.handleScrollEnd()
      }, 300)
      this.currentTop = document.documentElement.scrollTop || document.body.scrollTop
      if (this.left > this.clientWidth / 2) {
        this.left = this.clientWidth - this.itemWidth / 2
      } else {
        this.left = -this.itemWidth / 2
      }
    },
    handleScrollEnd () {
      let scrollTop = document.documentElement.scrollTop || document.body.scrollTop
      if (scrollTop === this.currentTop) {
        if (this.left > this.clientWidth / 2) {
          this.left = this.clientWidth - this.itemWidth - this.gapWidth
        } else {
          this.left = this.gapWidth
        }
        clearTimeout(this.timer)
      }
    }
  },
  data () {
    return {
      timer: null,
      currentTop: 0,
      clientWidth: 0,
      clientHeight: 0,
      left: 0,
      top: 0,
      show: true
    }
  }
}
</script>

<style lang="scss" scoped>
@import '../../assets/scss/rem.scss';
  .ys-float-btn{
    /* box-shadow:0 2px 10px 0 rgba(0,0,0,0.1); */
    border-radius:50%;
    color: #666666;
    z-index: 20;
    transition: all 0.3s;
    box-sizing: border-box;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;

    position: fixed;
    bottom: 20vw;

    button {
      padding: px2rem(5);
    }

    img{
      width: 50%;
      height: 50%;
      object-fit: contain;
      margin-bottom: 3px;
    }

    p{
      font-size: px2rem(20);
      margin-bottom: px2rem(10);
    }
    .close {
      position: absolute;
      right: 0;
      top: -2%;
    }
  }
</style>
```

