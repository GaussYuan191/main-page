<template>
    <div class="album-container">
      <!-- 顶部标题 -->
      <header class="album-header">
        <h1>我们的浪漫回忆</h1>
        <p>珍藏每一刻心动瞬间</p>
      </header>
      
      <!-- 分类导航 -->
      <div class="category-nav">
        <div 
          v-for="category in categories" 
          :key="category" 
          class="category-item"
          :class="{ active: activeCategory === category }"
          @click="activeCategory = category"
        >
          {{ category }}
        </div>
      </div>
      
      <!-- 相册网格 -->
      <div class="photo-grid">
        <div 
          v-for="photo in filteredPhotos" 
          :key="photo.id" 
          class="photo-item"
          @click="selectPhoto(photo)"
        >
          <div class="photo-wrapper">
            <div class="photo-placeholder" :style="{ background: `hsl(${photo.id * 20}, 70%, 85%)` }">
              <div class="image-content">
                <i class="fas fa-heart"></i>
              </div>
            </div>
            <div class="photo-meta">
              <h3>{{ photo.title }}</h3>
              <p>{{ photo.description }}</p>
              <div class="photo-footer">
                <span>{{ photo.date }}</span>
                <i 
                  class="like-icon fas fa-heart" 
                  :class="{ liked: photo.liked }" 
                  @click.stop="toggleLike(photo, $event)"
                ></i>
              </div>
            </div>
          </div>
        </div>
      </div>
      
      <!-- 大图查看 -->
      <div v-if="isFullscreen" class="fullscreen-view" @click.self="closeFullscreen">
        <div class="fullscreen-content">
          <div class="image-preview" :style="{ background: `hsl(${selectedPhoto.id * 20}, 70%, 85%)` }">
            <div class="image-content">
              <i class="fas fa-heart"></i>
            </div>
          </div>
          <div class="photo-details">
            <h2>{{ selectedPhoto.title }}</h2>
            <div class="tags">
              <span class="tag">{{ selectedPhoto.category }}</span>
              <span class="date">{{ selectedPhoto.date }}</span>
            </div>
            <p class="description">{{ selectedPhoto.description }}</p>
            <div class="actions">
              <button @click="toggleLike(selectedPhoto, $event)" :class="{ liked: selectedPhoto.liked }">
                <i class="fas fa-heart"></i>
                {{ selectedPhoto.liked ? '已喜欢' : '喜欢' }}
              </button>
              <button @click="closeFullscreen">
                <i class="fas fa-times"></i> 关闭
              </button>
            </div>
          </div>
        </div>
      </div>
      
      <!-- 心形动画 -->
      <div v-if="showHearts" class="hearts-container">
        <div 
          v-for="heart in hearts" 
          :key="heart.id"
          class="heart"
          :style="{
            left: heart.left + 'vw',
            fontSize: heart.size + 'px',
            animationDuration: heart.duration + 's',
            animationDelay: heart.delay + 's'
          }"
        >
          ❤️
        </div>
      </div>
      
      <!-- 底部装饰 -->
      <div class="bottom-decor">
        <div class="pink-line"></div>
        <div class="footer-text">我们的故事 • 未完待续</div>
      </div>
    </div>
  </template>
  
  <script lang="ts">
  import { defineComponent, ref, computed } from 'vue';
  
  interface Photo {
    id: number;
    title: string;
    description: string;
    category: string;
    date: string;
    liked: boolean;
  }
  
  interface Heart {
    id: number;
    left: number;
    size: number;
    duration: number;
    delay: number;
  }
  
  export default defineComponent({
    name: 'RomanticAlbum',
    setup() {
      // 照片数据
      const photos = ref<Photo[]>([
        { id: 1, title: "日落时刻", description: "与你一起看过的日落，是最美的风景", category: "日落", date: "2025-05-20", liked: true },
        { id: 2, title: "海边漫步", description: "沙滩上的脚印，是我们爱的证明", category: "海边", date: "2025-06-15", liked: false },
        { id: 3, title: "生日惊喜", description: "你惊喜的表情，是我最大的幸福", category: "惊喜", date: "2025-07-08", liked: true },
        { id: 4, title: "周年纪念", description: "365天的爱意，比昨天更深", category: "纪念日", date: "2025-08-12", liked: true },
        { id: 5, title: "樱花树下", description: "樱花飘落的速度，是每秒五厘米", category: "约会", date: "2025-04-02", liked: false },
        { id: 6, title: "烛光晚餐", description: "烛光中的你，格外温柔", category: "约会", date: "2025-09-03", liked: true },
        { id: 7, title: "巴黎之约", description: "在埃菲尔铁塔下，许下爱的诺言", category: "旅行", date: "2025-10-22", liked: false },
        { id: 8, title: "晨间咖啡", description: "和你分享的每一杯咖啡都格外香甜", category: "日常", date: "2025-11-05", liked: true },
        { id: 9, title: "冬日暖阳", description: "寒冷的冬天，有你就是春天", category: "日常", date: "2025-12-18", liked: false },
        { id: 10, title: "山顶日出", description: "等待日出的时刻，心跳为你加速", category: "旅行", date: "2025-03-14", liked: true },
        { id: 11, title: "星空之下", description: "满天繁星，不及你眼中的光芒", category: "夜晚", date: "2025-07-30", liked: true },
        { id: 12, title: "雨中漫步", description: "雨中的伞下，是我们的小世界", category: "日常", date: "2025-06-27", liked: false }
      ]);
  
      const selectedPhoto = ref<Photo | null>(null);
      const activeCategory = ref<string>('all');
      const isFullscreen = ref<boolean>(false);
      const showHearts = ref<boolean>(false);
      const categories = ref<string[]>(['all', '日落', '旅行', '海边', '纪念日', '约会', '惊喜', '日常', '夜晚']);
      
      // 筛选后的照片
      const filteredPhotos = computed(() => {
        if (activeCategory.value === 'all') return photos.value;
        return photos.value.filter(p => p.category === activeCategory.value);
      });
      
      // 选择照片
      const selectPhoto = (photo: Photo) => {
        selectedPhoto.value = photo;
        isFullscreen.value = true;
      };
      
      // 关闭大图查看
      const closeFullscreen = () => {
        isFullscreen.value = false;
        selectedPhoto.value = null;
      };
      
      // 切换喜欢状态
      const toggleLike = (photo: Photo, event: Event) => {
        event.stopPropagation();
        photo.liked = !photo.liked;
        if (photo.liked) {
          showHearts.value = true;
          setTimeout(() => showHearts.value = false, 1500);
        }
      };
      
      // 生成随机心形
      const generateHearts = (): Heart[] => {
        const hearts: Heart[] = [];
        for (let i = 0; i < 20; i++) {
          hearts.push({
            id: i,
            left: Math.random() * 100,
            size: Math.random() * 20 + 10,
            duration: Math.random() * 3 + 2,
            delay: Math.random() * 2
          });
        }
        return hearts;
      };
      
      const hearts = ref<Heart[]>(generateHearts());
      
      return {
        photos,
        selectedPhoto,
        activeCategory,
        isFullscreen,
        showHearts,
        categories,
        hearts,
        filteredPhotos,
        selectPhoto,
        closeFullscreen,
        toggleLike
      };
    }
  });
  </script>
  
  <style lang="less" scoped>
  @import url('https://fonts.googleapis.com/css2?family=Dancing+Script:wght@700&family=Playfair+Display:ital@1&display=swap');
  @import url('https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css');
  
  * {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    -webkit-tap-highlight-color: transparent;
  }
  
  .album-container {
    max-width: 428px; /* iPhone 14 Pro Max宽度 */
    width: 100%;
    background: rgba(255, 255, 255, 0.9);
    border-radius: 30px;
    box-shadow: 0 10px 40px rgba(255, 150, 200, 0.3);
    overflow: hidden;
    position: relative;
    padding-bottom: 60px;
    margin: 20px auto;
    border: 1px solid rgba(255, 182, 193, 0.3);
  }
  
  .album-header {
    text-align: center;
    padding: 30px 20px 20px;
    background: linear-gradient(to right, #ffafbd, #ffc3a0);
    position: relative;
    overflow: hidden;
    
    &::before {
      content: "";
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      background: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 100 100"><path fill="rgba(255,255,255,0.2)" d="M20,20 Q40,5 50,30 T80,20 Q95,30 90,60 Q85,90 60,90 Q35,90 20,70 Q5,50 20,20Z"/></svg>');
      background-size: 200px;
      opacity: 0.3;
    }
    
    h1 {
      font-family: 'Dancing Script', cursive;
      font-size: 2.8rem;
      color: #fff;
      text-shadow: 0 2px 4px rgba(122, 59, 90, 0.3);
      position: relative;
      margin-bottom: 8px;
    }
    
    p {
      font-family: 'Playfair Display', serif;
      font-style: italic;
      font-size: 1.1rem;
      color: rgba(255, 255, 255, 0.9);
      position: relative;
    }
  }
  
  .category-nav {
    display: flex;
    overflow-x: auto;
    padding: 15px 10px;
    background: #fff;
    border-bottom: 1px solid #ffe5f0;
    scrollbar-width: none;
    
    &::-webkit-scrollbar {
      display: none;
    }
    
    .category-item {
      flex: 0 0 auto;
      padding: 8px 20px;
      margin: 0 5px;
      border-radius: 20px;
      background: #fff5f9;
      color: #e86a9f;
      font-size: 0.95rem;
      cursor: pointer;
      transition: all 0.3s ease;
      border: 1px solid #ffd0e0;
      
      &.active {
        background: linear-gradient(to right, #ff8fab, #ff5c8a);
        color: white;
        font-weight: 600;
        box-shadow: 0 4px 10px rgba(255, 92, 138, 0.3);
        border: 1px solid #ff5c8a;
      }
    }
  }
  
  .photo-grid {
    display: grid;
    grid-template-columns: repeat(2, 1fr);
    gap: 15px;
    padding: 20px;
    
    @media (min-width: 400px) {
      grid-template-columns: repeat(3, 1fr);
    }
  }
  
  .photo-item {
    border-radius: 18px;
    overflow: hidden;
    box-shadow: 0 4px 15px rgba(255, 150, 180, 0.2);
    transition: transform 0.3s ease, box-shadow 0.3s ease;
    background: white;
    cursor: pointer;
    
    &:hover {
      transform: translateY(-5px);
      box-shadow: 0 8px 25px rgba(255, 150, 180, 0.3);
    }
    
    .photo-wrapper {
      display: flex;
      flex-direction: column;
      height: 100%;
    }
  }
  
  .photo-placeholder {
    width: 100%;
    padding-top: 100%; /* 1:1 比例 */
    position: relative;
    border-radius: 18px 18px 0 0;
    overflow: hidden;
    
    .image-content {
      position: absolute;
      top: 0;
      left: 0;
      width: 100%;
      height: 100%;
      display: flex;
      justify-content: center;
      align-items: center;
      color: rgba(255, 255, 255, 0.7);
      
      .fa-heart {
        font-size: 2.5rem;
      }
    }
  }
  
  .photo-meta {
    padding: 12px;
    flex-grow: 1;
    display: flex;
    flex-direction: column;
    
    h3 {
      font-size: 1rem;
      margin-bottom: 5px;
      color: #7a3b5a;
    }
    
    p {
      font-size: 0.8rem;
      color: #e86a9f;
      flex-grow: 1;
      margin-bottom: 10px;
      line-height: 1.4;
    }
    
    .photo-footer {
      display: flex;
      justify-content: space-between;
      align-items: center;
      font-size: 0.75rem;
      color: #c97b9d;
      
      .like-icon {
        color: #e0e0e0;
        cursor: pointer;
        transition: all 0.3s ease;
        font-size: 1.1rem;
        
        &.liked {
          color: #ff5c8a;
          text-shadow: 0 0 8px rgba(255, 92, 138, 0.5);
        }
        
        &:hover {
          transform: scale(1.2);
        }
      }
    }
  }
  
  .fullscreen-view {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(122, 59, 90, 0.95);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 1000;
    padding: 20px;
    
    .fullscreen-content {
      max-width: 500px;
      width: 100%;
      background: white;
      border-radius: 25px;
      overflow: hidden;
      box-shadow: 0 20px 50px rgba(122, 59, 90, 0.4);
      display: flex;
      flex-direction: column;
      
      .image-preview {
        width: 100%;
        padding-top: 100%;
        position: relative;
        
        .image-content {
          position: absolute;
          top: 0;
          left: 0;
          width: 100%;
          height: 100%;
          display: flex;
          justify-content: center;
          align-items: center;
          
          .fa-heart {
            font-size: 4rem;
            color: rgba(255, 255, 255, 0.8);
          }
        }
      }
      
      .photo-details {
        padding: 25px;
        
        h2 {
          font-size: 1.8rem;
          color: #7a3b5a;
          margin-bottom: 10px;
          font-family: 'Dancing Script', cursive;
        }
        
        .tags {
          display: flex;
          gap: 10px;
          margin-bottom: 20px;
          
          .tag {
            background: #fff0f5;
            color: #e86a9f;
            padding: 5px 15px;
            border-radius: 20px;
            font-size: 0.9rem;
          }
          
          .date {
            color: #c97b9d;
            font-size: 0.9rem;
            display: flex;
            align-items: center;
          }
        }
        
        .description {
          font-size: 1.1rem;
          line-height: 1.6;
          color: #7a3b5a;
          margin-bottom: 25px;
          font-style: italic;
        }
        
        .actions {
          display: flex;
          gap: 15px;
          
          button {
            flex: 1;
            padding: 12px;
            border: none;
            border-radius: 50px;
            font-weight: 600;
            cursor: pointer;
            display: flex;
            justify-content: center;
            align-items: center;
            gap: 8px;
            transition: all 0.3s ease;
            
            &:first-child {
              background: #fff0f5;
              color: #e86a9f;
              
              &.liked {
                background: linear-gradient(to right, #ff8fab, #ff5c8a);
                color: white;
                box-shadow: 0 4px 15px rgba(255, 92, 138, 0.4);
              }
              
              &:hover {
                background: #ffe0ed;
              }
            }
            
            &:last-child {
              background: #f8f8f8;
              color: #888;
              
              &:hover {
                background: #eee;
              }
            }
          }
        }
      }
    }
  }
  
  .hearts-container {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    pointer-events: none;
    z-index: 2000;
    
    .heart {
      position: absolute;
      bottom: -50px;
      animation: floatUp linear forwards;
      color: #ff5c8a;
      opacity: 0.7;
    }
  }
  
  .bottom-decor {
    position: absolute;
    bottom: 0;
    left: 0;
    width: 100%;
    display: flex;
    flex-direction: column;
    align-items: center;
    
    .pink-line {
      width: 100%;
      height: 5px;
      background: linear-gradient(to right, #ffafbd, #ffc3a0);
    }
    
    .footer-text {
      padding: 15px 0;
      font-size: 0.9rem;
      color: #c97b9d;
      letter-spacing: 1px;
      font-family: 'Playfair Display', serif;
      font-style: italic;
    }
  }
  
  @keyframes floatUp {
    0% {
      transform: translateY(0) scale(0.5);
      opacity: 0.7;
    }
    100% {
      transform: translateY(-100vh) scale(1.2);
      opacity: 0;
    }
  }
  
  /* 全局背景样式 */
  body {
    font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
    background: linear-gradient(135deg, #ffd6e7 0%, #ffeff6 100%);
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
    color: #7a3b5a;
    overflow-x: hidden;
    padding: 20px;
  }
  </style>