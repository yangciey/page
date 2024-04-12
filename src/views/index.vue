<template>
  <div class="overall" @mouseleave="mouseLeave" @mouseenter="mouseEnter">
    <div
      class="dot"
      ref="dot"
      :style="{ width: `${state.dt_wh}px`, height: `${state.dt_wh}px` }"
    ></div>
    <div
      class="bck"
      ref="bck"
      :style="{ width: `${state.bk_wh}px`, height: `${state.bk_wh}px` }"
    ></div>
    <div class="box">
      <div
        :class="{ tab: state.tabSelect == index }"
        :style="{
          color: state.tabSelect == index ? '#00BFFF' : '#ffffff',
        }"
        v-for="(item, index) in state.tabList"
        @click="handleClick(index)"
      >
        <div
          class="line"
          :style="{ display: state.tabSelect == index ? '' : 'none' }"
        ></div>
        {{ item.name }}
      </div>
    </div>
    <div class="page">
      <div class="PageHeader">123123</div>
      <div class="content" ref="content" @mousewheel="handleWheel">
        <div class="content-item" ref="home">1</div>
        <div class="content-item" ref="content1">2</div>
        <div class="content-item" ref="content2">3</div>
        <div class="content-item" ref="content3">4</div>
      </div>
    </div>
  </div>
</template>
<script setup>
import gsap from "gsap";
const dot = ref(null);
const bck = ref(null);
const content = ref(null);
const home = ref(null);
const content1 = ref(null);
const content2 = ref(null);
const content3 = ref(null);
const state = reactive({
  tabList: [
    { index: 0, name: "首页" },
    { index: 1, name: "内容1" },
    { index: 2, name: "内容2" },
    { index: 3, name: "内容3" },
  ],
  tabSelect: 0,
  isScroll: null,
  isClick: null,
  w: window.innerWidth,
  h: window.innerHeight,
  dt_wh: 6,
  bk_wh: 38,
});
const handleClick = (v) => {
  state.tabSelect = v;
  switch (v) {
    case 0:
      home.value.scrollIntoView({
        block: "end",
        behavior: "smooth",
      });
      break;
    case 1:
      content1.value.scrollIntoView({
        block: "end",
        behavior: "smooth",
      });
      break;
    case 2:
      content2.value.scrollIntoView({
        block: "end",
        behavior: "smooth",
      });
      break;
    case 3:
      content3.value.scrollIntoView({
        block: "end",
        behavior: "smooth",
      });
      break;

    default:
      break;
  }
};
const MouseMove = () => {
  let dt = document.querySelector(".dot");
  let bk = document.querySelector(".bck");
  let dt_wh = state.dt_wh / 2;
  let bk_wh = state.bk_wh / 2;
  window.addEventListener("mousemove", function (e) {
    let x = e.clientX;
    let y = e.clientY;
    window.localStorage.setItem("position", JSON.stringify({ x, y }));
    gsap.to(dt, {
      duration: 0,
      x: x - dt_wh,
      y: y - dt_wh,
    });
    gsap.to(bk, {
      duration: 0.4,
      x: x - bk_wh - state.w / 2,
      y: y - bk_wh - state.h / 2,
    });
    if (state.w > x + 6 || state.h > y + 6 || x >= 5 || y >= 5) {
      dt.style.opacity = 1;
      bk.style.opacity = 1;
    }
  });
};
const mouseClick = () => {
  let dt = document.querySelector(".dot");
  let bk = document.querySelector(".bck");
  let dt_wh = state.dt_wh / 2;
  let bk_wh = state.bk_wh / 2;
  window.addEventListener("mousedown", function (e) {
    // console.log("?????", state.isClick);
    if (!state.isClick) {
      // console.log("执行点击效果");
      let x = e.clientX;
      let y = e.clientY;
      var box = document.createElement("div");
      box.style.width = "0px";
      box.style.height = "0px";
      box.style.borderRadius = "50%";
      box.style.border = "rgb(255, 255, 255,.7) 3px solid";
      box.style.position = "fixed";
      box.style.top = y - dt_wh + "px"; //减去元素的宽高，使元素中心位于鼠标点击位置
      box.style.left = x - dt_wh + "px";
      box.style.zIndex = 9998;
      box.style.opacity = 1;
      box.style.pointerEvents = "none";
      document.querySelector(".overall").appendChild(box);
      let timer = setInterval(() => {
        box.style.opacity -= 0.07;
        box.style.width = parseInt(box.style.width) + 8 + "px";
        box.style.height = parseInt(box.style.height) + 8 + "px";
        box.style.left = parseInt(box.style.left) - 4 + "px";
        box.style.top = parseInt(box.style.top) - 4 + "px";
        if (box.style.opacity <= 0) {
          this.clearInterval(timer);
          box.parentNode.removeChild(box);
        }
      }, 50);
      // console.log("开启__ms后关闭");
      state.isClick = setTimeout(() => {
        clearTimeout(state.isClick);
        state.isClick = null;
        // console.log("关闭定时器");
      }, 500);
    }
  });
};
const mouseLeave = () => {
  let dt = document.querySelector(".dot");
  dt.style.opacity = 0;
};
const mouseEnter = () => {
  let dt = document.querySelector(".dot");
  dt.style.opacity = 1;
};
const handleWheel = (v) => {
  // console.log("定时器状态--false", state.isScroll);
  // console.log("已触发滚轮事件");
  if (!state.isScroll) {
    // console.log("滚动div");
    if (state.tabSelect < state.tabList.length - 1 && v.deltaY > 0) {
      state.tabSelect += 1;
      switch (state.tabSelect) {
        case 1:
          content1.value.scrollIntoView({
            block: "end",
            behavior: "smooth",
          });
          break;
        case 2:
          content2.value.scrollIntoView({
            block: "end",
            behavior: "smooth",
          });
          break;
        case 3:
          content3.value.scrollIntoView({
            block: "end",
            behavior: "smooth",
          });
          break;

        default:
          break;
      }
    } else if (state.tabSelect > 0 && v.deltaY < 0) {
      state.tabSelect -= 1;
      switch (state.tabSelect) {
        case 0:
          home.value.scrollIntoView({
            block: "end",
            behavior: "smooth",
          });
          break;
        case 1:
          content1.value.scrollIntoView({
            block: "end",
            behavior: "smooth",
          });
          break;
        case 2:
          content2.value.scrollIntoView({
            block: "end",
            behavior: "smooth",
          });
          break;

        default:
          break;
      }
    }
    // console.log("开启__ms后关闭定时器");
    state.isScroll = setTimeout(() => {
      clearTimeout(state.isScroll);
      state.isScroll = null;
    }, 1000);
  }
};
onMounted(() => {
  MouseMove();
  let dt = document.querySelector(".dot");
  dt.style.opacity = 1;
  mouseClick();
});
</script>
<style lang="scss" scoped>
.overall {
  width: 100%;
  height: 100%;
  cursor: none;
  user-select: none; //禁止文本选中
  .dot,
  .bck {
    position: fixed;
    pointer-events: none; //穿透div（点击事件穿透）
    left: 0;
    top: 0;
    border-radius: 50%;
    z-index: 9999;
    opacity: 0;
  }
  .dot {
    background: #fff;
    // transform: translate(-50%, -50%);
    // transition: opacity 0.2s;
  }
  .bck {
    left: 50%;
    top: 50%;
    border: 1px solid #fff;
    box-sizing: border-box;
    // transform: translate(-50%, -50%);
    transition: border 0.5s, opacity 0.6s, background 0.8s;
  }
  .box {
    display: flex;
    flex-direction: column;
    justify-content: space-evenly;
    align-items: center;
    position: fixed !important;
    top: calc(50% - 60px);
    right: 0;
    width: 80px;
    height: 120px;
    background-color: rgba(0, 0, 0, 0.5);
    .tab {
      position: relative;
      display: flex;
      justify-content: center;
      align-content: center;
      width: 100%;
      font-size: 15px;
      .line {
        position: absolute;
        left: 0px;
        width: 5px;
        height: 100%;
        background: #00bfff;
      }
    }
  }
  .page {
    width: 100%;
    height: 100%;
    display: flex;
    flex-direction: column;
    .PageHeader {
      width: 100%;
      height: 45px;
      position: fixed !important;
      background: #292222;
      color: #fff;
    }
    .content {
      // display: flex;
      // flex-direction: column;
      // justify-content: center;
      // align-items: center;
      width: 100%;
      height: 100%;
      padding: 45px 0 0 0;
      .content-item {
        display: flex;
        width: 100%;
        height: 100%;
        background: #000000;
        color: #fff;
      }
      &::-webkit-scrollbar {
        display: none;
      }
    }
  }
}
</style>
