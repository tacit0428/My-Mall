<template>
  <div id="cce-swiper">
    <div
      class="swiper"
      @touchstart="touchstart"
      @touchmove="touchmove"
      @touchend="touchend"
    >
      <slot></slot>
    </div>
    <slot name="indicator"></slot>
    <div class="indicator">
      <slot name="indicator" v-if="showIndicator && slideCount > 1">
        <div
          v-for="(item, index) in slideCount"
          class="indi-item"
          :class="{ active: index === currentIndex - 1 }"
          :key="index"
        ></div>
      </slot>
    </div>
  </div>
</template>

<script>
  export default {
    name: "Swiper",
    props: {
      interval: {
        type: Number,
        default: 3000,
      },
      animDuration: {
        type: Number,
        default: 300,
      },
      moveRatio: {
        type: Number,
        default: 0.25,
      },
      showIndicator: {
        type: Boolean,
        default: true,
      },
    },
    data: function () {
      return {
        slideCount: 0, // 元素
        totalWidth: 0, // swiper的宽度
        swiperStyle: {}, // swiper样式
        currentIndex: 1, // 当前的index
        scrolling: false, // 是否正在滚动
      };
    },
    mounted: function () {
      // 1.操作DOM, 在前后添加Slide
      setTimeout(() => {
        this.handleDom();
        this.startTimer(); // 开启定时器
      }, 100);
    },
    methods: {
      // 定时器操作
      startTimer: function () {
        this.playTimer = window.setInterval(() => {
          this.currentIndex++; // 每隔3s 切换图片
          this.scrollContent(-this.currentIndex * this.totalWidth);
        }, this.interval);
      },
      stopTimer: function () {
        window.clearInterval(this.playTimer);
      },
      // 滚到正确的位置
      scrollContent: function (currentPosition) {
        // 0. 设置正在滚动
        this.scrolling = true;

        // 1. 开始滚动动画
        this.swiperStyle.transition = "transform" + this.animDuration + "ms";
        this.setTransform(currentPosition);

        // 2. 判断滚动到的位置
        this.checkPosition();

        // 4. 滚动完成
        this.scrolling = false;
      },
      // 校验正确的位置
      checkPosition: function () {
        window.setTimeout(() => {
          // 1.校验正确的位置
          this.swiperStyle.transition = "0ms";
          if (this.currentIndex >= this.slideCount + 1) {
            this.currentIndex = 1;
            this.setTransform(-this.currentIndex);
          }
        });
      },
    },
  };
</script>

<style scoped>
</style>