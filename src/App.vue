<template>
  <div id="bg" style="background-image: url('/bg.webp')"></div>
  <div id="bg-layer"></div>
  <div class="container">
    <main>
      <section aria-label="介绍" class="hero">
        <!-- 头像在左，文本在右（默认桌面布局） -->
        <img id="avatar" alt="SugarMGP 头像" src="/icon.png"/>

        <div class="left">
          <h1>你好，我是 SugarMGP</h1>
          <p class="small">后端开发工程师 · 大二学生</p>

          <!-- 新增：社交链接 -->
          <div class="social-links">
            <a
                aria-label="Bilibili"
                href="https://space.bilibili.com/129922746"
                target="_blank"
            >
              <i class="icon bilibili"></i>
            </a>
            <a
                aria-label="GitHub"
                href="https://github.com/SugarMGP"
                target="_blank"
            >
              <i class="icon github"></i>
            </a>
            <a
                aria-label="Blog"
                href="https://blog.sugarmgp.cn/"
                target="_blank"
            >
              <i class="icon blog"></i>
            </a>
          </div>
        </div>
      </section>

      <section id="blog">
        <h2>最新博客</h2>
        <latest-blog rss-url="https://blog.sugarmgp.cn/rss2.xml"/>
      </section>
    </main>

    <footer class="footer">
      © {{ new Date().getFullYear() }} · SugarMGP |
      <a href="https://beian.miit.gov.cn/" target="_blank">浙ICP备2025196958号</a>
    </footer>
  </div>
</template>

<script lang="ts" setup>
import LatestBlog from './components/LatestBlog.vue'
</script>

<style scoped>
/* ========= 基础样式 ========= */
* {
  box-sizing: border-box;
}

#bg {
  z-index: -10;
  width: 100vw;
  height: 100vh;
  top: 0;
  left: 0;
  position: fixed;
  background: none no-repeat center / cover;
}

#bg-layer {
  z-index: -9;
  width: 100vw;
  height: 100vh;
  top: 0;
  left: 0;
  position: fixed;
  backdrop-filter: brightness(1.01);
  -webkit-backdrop-filter: brightness(1.01);
  background-color: #f7f9fcee;
}

.container {
  max-width: 1100px;
  margin: 0 auto;
  padding: 1rem;
}

/* ========= Hero 区域 ========= */
.hero {
  display: flex;
  align-items: center;
  gap: 3rem;
  padding: 2.5rem 0;
}

.hero .left {
  flex: 1;
  min-width: 0;
  display: flex;
  flex-direction: column;
  justify-content: center;
  text-align: left;
}

/* 头像 */
#avatar {
  flex: 0 0 auto;
  width: 22vw;
  max-width: 220px;
  height: 22vw;
  max-height: 220px;
  object-fit: cover;
  border-radius: 50%;
  border: 2px solid #fff;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.08);
  transition: box-shadow .25s ease, transform .18s ease;
  display: block;
}

#avatar:hover {
  box-shadow: 0 8px 28px rgba(0, 0, 0, 0.12);
  transform: translateY(-3px);
}

/* 标题与小字 */
h1 {
  margin: 0.5rem 0 0.5rem 0;
  font-size: clamp(1.6rem, 4vw, 2.2rem);
  line-height: 1.25;
  font-weight: 700;
}

h2 {
  font-size: 1.6rem;
}

.small {
  margin: 0.5rem 0 0.5rem 0;
  font-size: clamp(1.1rem, 4vw * 1.3 / 2.2, 1.3rem);
  color: #6b7280;
}

/* ========= 社交链接 ========= */
.social-links {
  display: flex;
  gap: 0.9rem; /* ✅ 增加间距让 icon 更呼吸 */
  margin: 0.5rem 0 0.5rem 0;
}

.social-links a {
  display: flex;
  align-items: center;
  justify-content: center;
  width: clamp(2.4rem, 4vw * 2.8 / 2.2, 2.8rem);
  height: clamp(2.4rem, 4vw * 2.8 / 2.2, 2.8rem);
  border-radius: 25%;
  background: rgba(255, 255, 255, 0.7);
  color: #333;
  text-decoration: none;
  transition: background .2s, transform .2s;
}

.social-links a:hover {
  background: rgba(255, 255, 255, 1);
  color: #fff;
  transform: translateY(-2px);
}

.icon {
  width: clamp(1.3rem, 4vw / 2.2 * 1.6, 1.6rem);
  height: clamp(1.3rem, 4vw / 2.2 * 1.6, 1.6rem);
  display: inline-block;
  background-size: cover;
}

.icon.github {
  background-image: url("https://cdn.jsdelivr.net/gh/simple-icons/simple-icons/icons/github.svg");
}

.icon.bilibili {
  background-image: url("https://cdn.jsdelivr.net/gh/simple-icons/simple-icons/icons/bilibili.svg");
}

.icon.blog {
  background-image: url("https://cdn.jsdelivr.net/gh/simple-icons/simple-icons/icons/hexo.svg");
}

/* ========= Footer ========= */
.footer {
  margin-top: 1.55rem;
  text-align: center;
  color: #6b7280;
  font-size: 0.8rem;
}

.footer > a {
  margin-top: 1.55rem;
  text-align: center;
  color: #6b7280;
  font-size: 0.8rem;
  text-decoration: none;
}

/* ========= 响应式 ========= */

/* 小屏幕 */
@media (max-width: 680px) {
  .hero {
    flex-direction: column;
    align-items: center;
    gap: 1rem;
    padding: 1.6rem 0;
  }

  .hero .left {
    width: 100%;
    text-align: center;
    align-items: center;
    padding: 0 0.5rem;
  }

  #avatar {
    margin: 0 auto;
  }

  .social-links {
    justify-content: center;
  }

  h1 {
    font-size: 1.6rem;
  }

  h2 {
    font-size: 1.4rem;
  }

  .small {
    font-size: 1.1rem;
  }
}
</style>
