<template>
  <section aria-label="最新文章" class="latest-blog">
    <div v-if="loading" aria-hidden="true" class="skeleton-list">
      <div v-for="n in count" :key="n" class="skeleton">
        <div class="s-line title"></div>
        <div class="s-line meta"></div>
        <div class="s-line excerpt"></div>
      </div>
    </div>

    <div v-else-if="error" aria-live="polite" class="card error" role="status">
      <div class="error-label">获取文章失败</div>
      <div class="small">{{ error }}</div>
    </div>

    <ul v-else-if="items.length" class="post-list">
      <li v-for="it in items" :key="it.url" class="post card">
        <a
            :href="it.url"
            class="post-title"
            rel="noopener noreferrer"
            target="_blank"
        >{{ it.title }}</a>

        <div class="meta-row">
          <time :datetime="it.published_at" class="post-meta">{{ formatDate(it.published_at) }}</time>
        </div>

        <div class="post-body">
          <p class="post-excerpt">{{ it.excerpt }}</p>
        </div>

        <a
            :href="it.url"
            aria-label="阅读全文"
            class="read"
            rel="noopener noreferrer"
            target="_blank"
        >阅读全文 →</a>
      </li>
    </ul>

    <div v-else class="card small empty">暂无文章</div>
  </section>
</template>

<script setup>
import {onMounted, ref} from 'vue'
import Parser from 'rss-parser/dist/rss-parser.min.js'

const props = defineProps({
  rssUrl: {type: String, required: true},
  count: {type: Number, default: 4}
})

const loading = ref(false)
const error = ref('')
const items = ref([])
const PREVIEW_LEN = 150

function formatDate(d) {
  if (!d) return ''
  const dt = new Date(d)
  if (isNaN(dt)) return d
  return dt.toLocaleString()
}

function stripHtml(html = '') {
  return String(html || '')
      .replace(/<\/?[^>]+(>|$)/g, ' ')
      .replace(/\s+/g, ' ')
      .trim()
}

function normalize(feed) {
  const raw = Array.isArray(feed.items) ? feed.items : []
  raw.sort((a, b) => {
    const da = new Date(a.isoDate || a.pubDate).getTime() || 0
    const db = new Date(b.isoDate || b.pubDate).getTime() || 0
    return db - da
  })
  return raw.slice(0, props.count).map(it => {
    const html = it['content:encoded'] || it.content || it.summary || ''
    const text = stripHtml(html || it.description || '')
    return {
      title: it.title || 'Untitled',
      url: it.link || it.guid || '#',
      excerpt: text.length > PREVIEW_LEN ? text.slice(0, PREVIEW_LEN - 3) + '...' : text,
      published_at: it.isoDate || it.pubDate || ''
    }
  })
}

async function fetchAndParse(url) {
  const res = await fetch(url, {headers: {Accept: 'application/rss+xml, application/xml, text/xml'}})
  if (!res.ok) throw new Error(`RSS 请求返回 ${res.status}`)
  const xml = await res.text()
  const parser = new Parser()
  const feed = await parser.parseString(xml)
  return normalize(feed)
}

onMounted(async () => {
  loading.value = true
  error.value = ''
  items.value = []
  try {
    items.value = await fetchAndParse(props.rssUrl)
  } catch (e) {
    error.value = e.message || String(e)
  } finally {
    loading.value = false
  }
})
</script>

<style scoped>
.latest-blog {
  --bg: rgba(255, 255, 255, 0.5);
  --muted: #6b7280;
  --excerpt: #5f6c85; /* 比原先暖一点的灰蓝 */
  --accent: #4f46e5; /* 柔和蓝紫色，取自 indigo-600 */
  --accent-hover: #4338ca; /* 深一点 hover 态 */
  --card-shadow: 0 10px 26px rgba(15, 23, 42, 0.06);
  --card-border: rgba(12, 17, 24, 0.04);

  max-width: 1100px;
  margin: 0 auto;
  padding: 0.75rem 0;
  letter-spacing: 0.5px;
}

.post-list {
  list-style: none;
  padding: 0;
  margin: 0;
  display: grid;
  gap: 1.5rem;
  grid-template-columns: repeat(2, 1fr);
}

.post {
  padding: 1.25rem;
  border-radius: 14px;
  background: var(--bg);
  box-shadow: var(--card-shadow);
  border: 1px solid var(--card-border);
  display: flex;
  flex-direction: column;
  gap: 0.6rem;
  transition: transform .12s cubic-bezier(.2, .9, .3, 1), box-shadow .12s ease;
  min-height: 180px;
}

.post:hover {
  transform: translateY(-6px);
  box-shadow: 0 24px 54px rgba(15, 23, 42, 0.12);
}

/* 明确文章标题左对齐（防止被 .card 的样式影响） */
.post-title {
  font-weight: 700;
  color: #071128;
  text-decoration: none;
  font-size: 1.1rem;
  line-height: 1.28;
  transition: color .12s ease, transform .12s ease;
  letter-spacing: 0.2px;
  text-align: left;
  word-break: break-word;
}

.post-title:hover {
  color: var(--accent);
  transform: translateY(-2px);
}

.post-title:focus-visible {
  outline: 3px solid rgba(124, 58, 237, 0.12);
  outline-offset: 4px;
  border-radius: 6px;
}

.meta-row {
  display: flex;
  gap: 0.6rem;
  align-items: center;
}

.post-meta {
  color: #6b7280;
  font-size: 0.88rem;
  margin-top: -2px;
  letter-spacing: 1px;
}

.post-body {
  display: block;
  flex: 1 1 auto;
  min-height: 2.6em;
}

.post-excerpt {
  margin: 0;
  color: var(--excerpt);
  font-size: 0.95rem;
  line-height: 1.65;
  overflow: hidden;
  text-overflow: ellipsis;
  max-height: calc(1.55rem * 3.2);
  text-align: left;
}

.read {
  margin-top: auto;
  color: var(--accent);
  text-decoration: none;
  font-weight: 600;
  align-self: flex-end;
  padding: 7px 12px;
  border-radius: 10px;
  background: linear-gradient(
      180deg,
      rgba(79, 70, 229, 0.06),
      rgba(79, 70, 229, 0.02)
  );
  transition: background .12s ease, transform .12s ease, box-shadow .12s ease;
}

.read:hover {
  transform: translateY(-2px);
  background: linear-gradient(
      180deg,
      rgba(67, 56, 202, 0.12),
      rgba(67, 56, 202, 0.04)
  );
}

/* 通用 card 基本样式（不再强制居中） */
.card {
  padding: 1rem;
  border-radius: 12px;
  background: var(--bg);
  box-shadow: var(--card-shadow);
}

/* 仅将错误 / 空状态文本居中，避免影响 .post 卡片 */
.card.error,
.empty {
  text-align: center;
}

.small {
  color: #6b7280;
  font-size: 0.95rem;
}

.error {
  color: crimson;
}

.error-label {
  font-weight: 700;
  margin-bottom: 0.35rem;
}

/* skeleton */
.skeleton-list {
  display: grid;
  gap: 1.15rem;
  grid-template-columns: repeat(2, 1fr);
}

.skeleton {
  padding: 1rem;
  border-radius: 12px;
  background: linear-gradient(180deg, #fbfbfb, #fff);
  box-shadow: var(--card-shadow);
  border: 1px solid var(--card-border);
}

.s-line {
  height: 12px;
  border-radius: 8px;
  background: linear-gradient(90deg, #eee, #ddd, #eee);
  animation: shimmer 1.2s linear infinite;
  margin-bottom: 10px;
}

.s-line.title {
  height: 16px;
  width: 64%;
}

.s-line.meta {
  width: 36%;
  height: 12px;
}

.s-line.excerpt {
  width: 100%;
  height: 44px;
}

@keyframes shimmer {
  0% {
    background-position: -200px 0
  }
  100% {
    background-position: 200px 0
  }
}

@media (max-width: 800px) {
  .post-list {
    grid-template-columns: repeat(1, 1fr);
    gap: 1.3rem;
  }

  .post {
    padding: 1rem;
    min-height: 150px;
  }

  .post-title {
    font-size: 1rem;
    line-height: 1.35;
  }

  .post-excerpt {
    font-size: 0.92rem;
    line-height: 1.55;
    max-height: calc(1.45rem * 3);
  }

  .read {
    font-size: 0.88rem;
    padding: 6px 10px;
    border-radius: 9px;
    align-self: flex-end;
  }
}

/* 小屏幕（<=680px） */
@media (max-width: 680px) {
  .post-list {
    grid-template-columns: 1fr;
    gap: 1.1rem;
  }

  .post {
    padding: 0.85rem;
    min-height: 130px;
    gap: 0.45rem;
  }

  .post-title {
    font-size: 1rem;
    line-height: 1.3;
  }

  .post-excerpt {
    font-size: 0.9rem;
    line-height: 1.45;
    max-height: calc(1.35rem * 3);
  }

  .read {
    font-size: 0.85rem;
    padding: 5px 8px;
    border-radius: 7px;
    align-self: flex-end;
  }

  .empty {
    font-size: 0.88rem;
    padding: 1rem 0.5rem;
  }
}
</style>
