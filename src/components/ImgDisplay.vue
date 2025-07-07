<template>
  <div class="romantic-carousel-container">
    <!-- 顶部装饰元素 -->
    <div class="decoration-top">
      <div
        class="heart"
        v-for="n in 20"
        :key="'top-' + n"
        :style="heartStyle(n)"
      ></div>
    </div>

    <!-- 轮播主体 -->
    <div class="romantic-carousel">
      <!-- 图片展示区域 -->
      <transition :name="transitionName" mode="out-in">
        <div
          class="carousel-image"
          :key="currentIndex"
          :style="{
            backgroundImage: `url(${images[currentIndex].url})`,
            width: '400px',
          }"
        >
          <div class="image-overlay"></div>
          <div class="image-caption">
            <div class="caption-title">{{ images[currentIndex].title }}</div>
            <div class="caption-quote">{{ images[currentIndex].quote }}</div>
          </div>
        </div>
      </transition>

      <!-- 左右控制按钮 -->
      <div class="carousel-control prev" @click="prevSlide">
        <div class="arrow"></div>
      </div>
      <div class="carousel-control next" @click="nextSlide">
        <div class="arrow"></div>
      </div>

      <!-- 底部指示器 -->
      <div class="carousel-indicators">
        <div
          v-for="(item, index) in images"
          :key="index"
          class="indicator"
          :class="{ active: currentIndex === index }"
          @click="goToSlide(index)"
        >
          <div class="progress" v-if="currentIndex === index"></div>
        </div>
      </div>
    </div>

    <!-- 底部装饰元素 -->
    <div class="decoration-bottom">
      <div
        class="heart"
        v-for="n in 20"
        :key="'bottom-' + n"
        :style="heartStyle(n)"
      ></div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { ref, computed, onMounted, onUnmounted } from "vue";

// 图片数据
const images = ref([
  {
    url: "https://picsum.photos/800/600?random=1",
    title: "浪漫时刻",
    quote: "在每一个日落里，我都想与你共度",
  },
  {
    url: "https://picsum.photos/800/600?random=2",
    title: "爱的旅程",
    quote: "与你同行，每一步都是风景",
  },
  {
    url: "https://picsum.photos/800/600?random=3",
    title: "永恒誓言",
    quote: "在星空下，许下永恒的承诺",
  },
  {
    url: "https://picsum.photos/800/600?random=4",
    title: "甜蜜回忆",
    quote: "每一刻的甜蜜，都值得珍藏",
  },
  {
    url: "https://picsum.photos/800/600?random=5",
    title: "温柔相伴",
    quote: "在温柔的时光里，与你相伴到老",
  },
]);

const currentIndex = ref(0);

const direction = ref("next");
const autoPlay = ref(true);
const transitionName = computed(() => `slide-${direction.value}`);

// 切换上一张
const prevSlide = () => {
  direction.value = "prev";
  currentIndex.value =
    currentIndex.value === 0 ? images.value.length - 1 : currentIndex.value - 1;
};

// 切换下一张
const nextSlide = () => {
  direction.value = "next";
  currentIndex.value = (currentIndex.value + 1) % images.value.length;
};

// 跳转到指定幻灯片
const goToSlide = (index: number) => {
  direction.value = index > currentIndex.value ? "next" : "prev";
  currentIndex.value = index;
};

// 自动播放逻辑
let timer: number | null = null;

const startAutoPlay = () => {
  if (autoPlay.value) {
    timer = setInterval(() => {
      nextSlide();
    }, 5000);
  }
};

const stopAutoPlay = () => {
  if (timer) {
    clearInterval(timer);
    timer = null;
  }
};

// 随机心形样式
const heartStyle = (index: number) => {
  const sizes = ["small", "medium", "large"];
  const opacities = [0.3, 0.5, 0.7];
  const rotations = [0, 15, 30, -15, -30];

  return {
    "--size": `${Math.random() * 20 + 10}px`,
    "--opacity": opacities[Math.floor(Math.random() * opacities.length)],
    "--rotation": `${
      rotations[Math.floor(Math.random() * rotations.length)]
    }deg`,
    "--delay": `${index * 0.1}s`,
    "--left": `${Math.random() * 100}%`,
    "--animation-duration": `${Math.random() * 5 + 5}s`,
  };
};

// 生命周期钩子
onMounted(() => {
  startAutoPlay();
});

onUnmounted(() => {
  stopAutoPlay();
});
</script>

<style lang="less">
@primary-pink: #ff7eb9;
@light-pink: #ffc2d6;
@dark-pink: #e75480;
@white: #fff;
@shadow: rgba(0, 0, 0, 0.1);

.romantic-carousel-container {
  position: relative;
  width: 100%;
  max-width: 900px;
  margin: 0 auto;
  padding: 30px 0;
  overflow: hidden;
  font-family: "Arial", sans-serif;

  .decoration-top,
  .decoration-bottom {
    position: absolute;
    left: 0;
    width: 100%;
    height: 40px;
    z-index: 10;

    .heart {
      position: absolute;
      top: 0;
      left: var(--left);
      width: var(--size);
      height: var(--size);
      opacity: var(--opacity);
      animation: float var(--animation-duration) infinite var(--delay)
        ease-in-out;

      &::before,
      &::after {
        content: "";
        position: absolute;
        width: 100%;
        height: 100%;
        background: @primary-pink;
        border-radius: 50%;
      }

      &::before {
        left: -50%;
      }

      &::after {
        top: -50%;
      }
    }
  }

  .decoration-top {
    top: 0;
  }

  .decoration-bottom {
    bottom: 0;
    transform: rotate(180deg);
  }
}

.romantic-carousel {
  position: relative;
  width: 100%;
  height: 500px;
  border-radius: 20px;
  overflow: hidden;
  box-shadow: 0 10px 30px rgba(231, 84, 128, 0.3);
  background: linear-gradient(135deg, @light-pink, @primary-pink);

  .carousel-image {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-size: cover;
    background-position: center;
    display: flex;
    align-items: flex-end;

    .image-overlay {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: linear-gradient(to top, rgba(0, 0, 0, 0.7), transparent 60%);
      z-index: 1;
    }

    .image-caption {
      position: relative;
      z-index: 2;
      padding: 30px;
      color: @white;
      width: 100%;
      text-align: center;

      .caption-title {
        font-size: 2.5rem;
        font-weight: bold;
        margin-bottom: 10px;
        text-shadow: 0 2px 4px @shadow;
      }

      .caption-quote {
        font-size: 1.2rem;
        font-style: italic;
        max-width: 600px;
        margin: 0 auto;
        line-height: 1.6;
      }
    }
  }

  .carousel-control {
    position: absolute;
    top: 50%;
    transform: translateY(-50%);
    width: 50px;
    height: 50px;
    background: rgba(255, 255, 255, 0.2);
    border-radius: 50%;
    display: flex;
    justify-content: center;
    align-items: center;
    cursor: pointer;
    z-index: 10;
    transition: all 0.3s ease;
    backdrop-filter: blur(5px);

    &:hover {
      background: rgba(255, 255, 255, 0.4);
      transform: translateY(-50%) scale(1.1);
    }

    .arrow {
      width: 15px;
      height: 15px;
      border-top: 3px solid @white;
      border-right: 3px solid @white;
    }

    &.prev {
      left: 20px;

      .arrow {
        transform: rotate(-135deg);
        margin-right: 5px;
      }
    }

    &.next {
      right: 20px;

      .arrow {
        transform: rotate(45deg);
        margin-left: 5px;
      }
    }
  }

  .carousel-indicators {
    position: absolute;
    bottom: 20px;
    left: 0;
    width: 100%;
    display: flex;
    justify-content: center;
    gap: 10px;
    z-index: 10;

    .indicator {
      width: 14px;
      height: 14px;
      border-radius: 50%;
      background: rgba(255, 255, 255, 0.4);
      cursor: pointer;
      position: relative;
      overflow: hidden;
      transition: all 0.3s ease;

      &:hover {
        transform: scale(1.2);
        background: rgba(255, 255, 255, 0.7);
      }

      &.active {
        background: transparent;
        border: 2px solid @white;

        .progress {
          position: absolute;
          top: 0;
          left: 0;
          height: 100%;
          width: 100%;
          background: @white;
          animation: progress 5s linear forwards;
        }
      }
    }
  }
}

/* 过渡动画 */
.slide-next-enter-active,
.slide-next-leave-active,
.slide-prev-enter-active,
.slide-prev-leave-active {
  transition: all 1s ease;
}

.slide-next-enter-from {
  transform: translateX(100%);
  opacity: 0;
}

.slide-next-leave-to {
  transform: translateX(-100%);
  opacity: 0;
}

.slide-prev-enter-from {
  transform: translateX(-100%);
  opacity: 0;
}

.slide-prev-leave-to {
  transform: translateX(100%);
  opacity: 0;
}

/* 关键帧动画 */
@keyframes float {
  0% {
    transform: translateY(0) rotate(var(--rotation));
  }
  50% {
    transform: translateY(-20px) rotate(var(--rotation));
  }
  100% {
    transform: translateY(0) rotate(var(--rotation));
  }
}

@keyframes progress {
  0% {
    width: 0;
  }
  100% {
    width: 100%;
  }
}

/* 响应式设计 */
@media (max-width: 768px) {
  .romantic-carousel {
    height: 400px;

    .image-caption {
      .caption-title {
        font-size: 2rem !important;
      }
      .caption-quote {
        font-size: 1rem !important;
      }
    }
  }
}

@media (max-width: 480px) {
  .romantic-carousel {
    height: 300px;

    .image-caption {
      padding: 15px !important;

      .caption-title {
        font-size: 1.5rem !important;
      }
      .caption-quote {
        font-size: 0.9rem !important;
      }
    }

    .carousel-control {
      width: 40px;
      height: 40px;
    }
  }
}
</style>
