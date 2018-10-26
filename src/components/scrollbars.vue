<template>
  <div class="scroller-wrapper" ref="wrapper">
    <div class="content-wrapper" ref="slot" @scroll="onScrollHandle">
      <slot></slot>
    </div>
    <div class="y-bar-outer" ref="ybar">
      <div class="inner" :style="`height:${yBarHeight}px;opacity:${yBarOpa};transform:translateY(${yInnerTop}px)`"></div>
    </div>
    <div class="x-bar-outer" ref="xbar">
      <div class="inner" :style="`width:${xBarWidth}px;opacity:${xBarOpa};transform:translateX(${xInnerLeft}px)`"></div>
    </div>
  </div>
</template>

<script>
const debounce = function(func, wait, immediate) {
  let timeout, args, context, timestamp, result

  let later = function() {
    let last = Date.now() - timestamp // 获取现在与上一次防抖函数的运行间隔时间

    if (last < wait && last >= 0) {
      // 间隔太小，频率过多，继续延迟
      timeout = setTimeout(later, wait - last) // wait - last为接下来不触发防抖的期望时间
    } else {
      timeout = null // 间隔够长，运行函数
      if (!immediate) {
        result = func.apply(context, args)
        if (!timeout) context = args = null
      }
    }
  }

  return function(..._args) {
    context = this
    args = _args
    timestamp = Date.now() //刷新最新一次调用该防抖函数的时间戳
    let callNow = immediate && !timeout // 是否需要立即调用一次，
    if (!timeout) timeout = setTimeout(later, wait) // 同一时间内只存在一个超时
    if (callNow) {
      // 立即调用一次
      result = func.apply(context, args)
      context = args = null
    }
    // 短时间内触发多次不会调用原函数
    return result
  }
}

export default {
  props: {
    autoHide: {
      type: Boolean,
      default: false
    },
    hideTime: {
      type: Number,
      default: 500
    },
    data: {
      type: Array,
      default: function() {
        return []
      }
    }
  },
  name: 'scrollbars',
  data() {
    return {
      yBarHeight: 0,
      yBarOpa: 1,
      yInnerTop: 0,
      xBarWidth: 0,
      xBarOpa: 1,
      xInnerLeft: 0,
      info: {
        // 刷新的时候才更新 减少读取 增加性能
        wrapperHeight: 0,
        wrapperWidth: 0,
        contentWidth: 0,
        contentHeight: 0,
        yBarHeight: 0,
        xBarWdith: 0
      }
    }
  },
  mounted() {
    this.refresh()
    // 注册节流函数
    this.debounceHide = debounce(() => {
      this.hideBar()
    }, this.hideTime)
  },
  methods: {
    refresh() {
      this.reGetInfo()
      // y-bar
      if (this.info.contentHeight <= this.info.wrapperHeight) {
        this.yBarHeight = 0
      } else {
        this.yBarHeight =
          (this.info.wrapperHeight / this.info.contentHeight) *
          this.info.yBarHeight
      }
      // x-bar
      if (this.info.contentWidth <= this.info.wrapperWidth) {
        this.xBarWidth = 0
      } else {
        this.xBarWidth =
          (this.info.wrapperWidth / this.info.contentWidth) *
          this.info.xBarWdith
      }
    },
    reGetInfo() {
      this.info.wrapperHeight = this.$refs.wrapper.clientHeight
      this.info.wrapperWidth = this.$refs.wrapper.clientWidth
      this.info.contentHeight = this.$refs.slot.children[0].clientHeight
      this.info.contentWidth = this.$refs.slot.children[0].clientWidth
      this.info.yBarHeight = this.$refs.ybar.clientHeight
      this.info.xBarWdith = this.$refs.xbar.clientWidth
    },
    onScrollHandle(e) {
      // 自动隐藏
      if (this.autoHide) {
        this.showBar()
        this.debounceHide()
      }
      const yScrollPercent = this.$refs.slot.scrollTop / this.info.contentHeight
      this.yInnerTop = this.info.yBarHeight * yScrollPercent
      const xScrollPercent = this.$refs.slot.scrollLeft / this.info.contentWidth
      this.xInnerLeft = this.info.xBarWdith * xScrollPercent
    },
    showBar() {
      this.yBarOpa = 1
      this.xBarOpa = 1
    },
    hideBar() {
      this.yBarOpa = 0
      this.xBarOpa = 0
    }
  },
  watch: {
    data(nVal) {
      this.refresh()
    }
  }
}
</script>

<style lang="stylus">
.scroller-wrapper
  position relative
  overflow hidden
  .content-wrapper
    position absolute
    top 0
    left 0
    right 0
    bottom 0
    margin-right -15px
    margin-bottom -15px
    overflow scroll
  .y-bar-outer
    position absolute
    width 6px
    transition opacity 200ms ease 0s
    opacity 1
    right 2px
    bottom 2px
    top 2px
    border-radius 3px
    .inner
      position relative
      display block
      width 100%
      cursor pointer
      border-radius inherit
      background-color rgba(0, 0, 0, 0.2)
      transition opacity 200ms ease 0s
  .x-bar-outer
    position absolute
    height 6px
    transition opacity 200ms ease 0s
    opacity 1
    right 2px
    bottom 2px
    left 2px
    border-radius 3px
    .inner
      position relative
      display block
      height 100%
      cursor pointer
      border-radius inherit
      background-color rgba(0, 0, 0, 0.2)
      width 0px
      transition opacity 200ms ease 0s
</style>
