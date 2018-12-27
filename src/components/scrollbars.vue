<template>
  <div class="scroller-wrapper" ref="wrapper">
    <div class="content-wrapper" ref="slot" @scroll="onScrollHandle">
      <slot></slot>
    </div>
    <div class="y-bar-outer bar" ref="ybar" @mouseenter="showBar" @mouseleave="yLeaveHandle">
      <div
        class="inner"
        @mousedown="yMouseDownHandle"
        v-if="displayBar"
        :class="{move:move.yMouseDown}"
        :style="`height:${yBarHeight}px;opacity:${yBarOpa};transform:translateY(${yInnerTop}px)`"
      ></div>
    </div>
    <div class="x-bar-outer bar" ref="xbar" @mouseenter="showBar" @mouseleave="xLeaveHandle">
      <div
        class="inner"
        @mousedown="xMouseDownHandle"
        v-if="displayBar"
        :class="{move:move.xMouseDown}"
        :style="`width:${xBarWidth}px;opacity:${xBarOpa};transform:translateX(${xInnerLeft}px)`"
      ></div>
    </div>
  </div>
</template>

<script>
const debounce = function(func, wait, immediate) {
  let timeout, args, context, timestamp, result;

  let later = function() {
    let last = Date.now() - timestamp; // 获取现在与上一次防抖函数的运行间隔时间

    if (last < wait && last >= 0) {
      // 间隔太小，频率过多，继续延迟
      timeout = setTimeout(later, wait - last); // wait - last为接下来不触发防抖的期望时间
    } else {
      timeout = null; // 间隔够长，运行函数
      if (!immediate) {
        result = func.apply(context, args);
        if (!timeout) context = args = null;
      }
    }
  };

  return function(..._args) {
    context = this;
    args = _args;
    timestamp = Date.now(); //刷新最新一次调用该防抖函数的时间戳
    let callNow = immediate && !timeout; // 是否需要立即调用一次，
    if (!timeout) timeout = setTimeout(later, wait); // 同一时间内只存在一个超时
    if (callNow) {
      // 立即调用一次
      result = func.apply(context, args);
      context = args = null;
    }
    // 短时间内触发多次不会调用原函数
    return result;
  };
};

export default {
  props: {
    autoHide: {
      type: Boolean,
      default: false
    },
    hideTime: {
      type: Number,
      default: 1000
    },
    data: {
      type: [Array, String, Object, Number],
      default: function() {
        return [];
      }
    },
    displayBar: {
      type: Boolean,
      default: true
    },
    listenScrollBottom: {
      type: [Function, Boolean],
      default: false
    },
    scrollBottomHeight: {
      type: Number,
      default: 50
    },
    listenScrollRight: {
      type: [Function, Boolean],
      default: false
    },
    scrollRightWidth: {
      type: Number,
      default: 50
    }
  },
  name: "scrollbars",
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
      },
      move: {
        yMouseDown: false,
        yMouseDownOffset: 0,
        xMouseDown: false,
        xMouseOffset: 0
      }
    };
  },
  mounted() {
    this.refresh();
    // 注册节流函数
    this.debounceHide = debounce(() => {
      // 放置在此处的判断是个妥协
      // 尽管会可读性 但是这个判断过于重要 几乎所有地方都需要达成条件才触发自动隐藏
      // 并且在外部判断 由于延时的特性会导致一些同步问题
      if (this.autoHide && !this.move.xMouseDown && !this.move.yMouseDown) {
        this.hideBar();
      }
    }, this.hideTime);
    // 提示用户可以滚动
    if (this.autoHide) {
      this.showBar();
      this.debounceHide();
    }
  },
  methods: {
    refresh() {
      this.reGetInfo();
      // y-bar
      if (this.info.contentHeight <= this.info.wrapperHeight + 1) {
        this.yBarHeight = 0;
      } else {
        this.yBarHeight =
          (this.info.wrapperHeight / this.info.contentHeight) *
          this.info.yBarHeight;
      }
      // x-bar
      if (this.info.contentWidth <= this.info.wrapperWidth + 1) {
        this.xBarWidth = 0;
      } else {
        this.xBarWidth =
          (this.info.wrapperWidth / this.info.contentWidth) *
          this.info.xBarWdith;
      }
    },
    reGetInfo() {
      // 获取基本的宽高信息
      this.info.wrapperHeight = this.$refs.wrapper.clientHeight;
      this.info.wrapperWidth = this.$refs.wrapper.clientWidth;
      this.info.contentHeight = this.$refs.slot.children[0].clientHeight;
      this.info.contentWidth = this.$refs.slot.children[0].clientWidth;
      this.info.yBarHeight = this.$refs.ybar.clientHeight;
      this.info.xBarWdith = this.$refs.xbar.clientWidth;
    },
    onScrollHandle(e) {
      // 自动隐藏
      this.showBar();
      this.debounceHide();
      const yScrollPercent =
        this.$refs.slot.scrollTop / this.info.contentHeight;
      this.yInnerTop = this.info.yBarHeight * yScrollPercent;
      const xScrollPercent =
        this.$refs.slot.scrollLeft / this.info.contentWidth;
      this.xInnerLeft = this.info.xBarWdith * xScrollPercent;
      this.$emit("scroll", e);
      // 监听滚动到底部
      if (this.listenScrollBottom) {
        const bottomHeight =
          this.info.contentHeight -
          this.info.wrapperHeight -
          this.$refs.slot.scrollTop;
        if (bottomHeight <= this.scrollBottomHeight) {
          this.listenScrollBottom(bottomHeight);
        }
      }
      // 监听滚动到右侧（横向滚动）
      if (this.listenScrollRight) {
        const rightWidth =
          this.info.contentWidth -
          this.info.wrapperWidth -
          this.$refs.slot.scrollLeft;
        if (rightWidth <= this.scrollRightWidth) {
          this.listenScrollRight(rightWidth);
        }
      }
    },
    showBar() {
      this.yBarOpa = 1;
      this.xBarOpa = 1;
    },
    hideBar() {
      this.yBarOpa = 0;
      this.xBarOpa = 0;
    },
    yMouseDownHandle(e) {
      this.move.yMouseDown = true;
      this.move.yMouseDownOffset = e.screenY;
      this.showBar();
      const moveHandle = e => {
        e.preventDefault();
        // 计算出Y轴偏移和缩放比例 让content进行滚动
        let offsetY = e.screenY - this.move.yMouseDownOffset;
        this.move.yMouseDownOffset = e.screenY;
        let scale = this.info.contentHeight / this.info.yBarHeight;
        let contentOffset = offsetY * scale;
        this.$refs.slot.scrollTop += contentOffset;
      };
      document.addEventListener("mousemove", moveHandle);
      document.addEventListener("mouseup", e => {
        // 清除监听 重设默认值
        // 自动隐藏
        document.removeEventListener("mousemove", moveHandle);
        this.move.yMouseDown = false;
        this.move.yMouseDownOffset = 0;
        this.debounceHide();
      });
    },
    xMouseDownHandle(e) {
      this.move.xMouseDown = true;
      this.move.xMouseDownOffset = e.screenX;
      this.showBar();
      const moveHandle = e => {
        // 计算出X轴偏移和缩放比例 让content进行滚动
        e.preventDefault();
        let offsetX = e.screenX - this.move.xMouseDownOffset;
        this.move.xMouseDownOffset = e.screenX;
        let scale = this.info.contentWidth / this.info.xBarWdith;
        let contentOffset = offsetX * scale;
        this.$refs.slot.scrollLeft += contentOffset;
      };
      document.addEventListener("mousemove", moveHandle);
      document.addEventListener("mouseup", e => {
        document.removeEventListener("mousemove", moveHandle);
        // 清除监听 重设默认值
        // 自动隐藏
        this.move.xMouseDown = false;
        this.move.xMouseDownOffset = 0;
        this.debounceHide();
      });
    },
    xLeaveHandle() {
      if (!this.move.xMouseDown) {
        this.debounceHide();
      }
    },
    yLeaveHandle() {
      if (!this.move.yMouseDown) {
        this.debounceHide();
      }
    }
  },
  watch: {
    data(nVal) {
      this.refresh();
    }
  }
};
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
  .bar
    >.inner
      &:hover
        background-color rgba(0, 0, 0, 0.3)
      &.move
        background-color rgba(0, 0, 0, 0.3)
</style>
