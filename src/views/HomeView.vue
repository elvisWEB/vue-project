<script setup lang="ts">
import { computed, onMounted, onUnmounted, ref } from 'vue'

const ranges = [
  { label: '月度', runway: '18.7', capital: '3.26', confidence: '72%' },
  { label: '季度', runway: '21.4', capital: '3.88', confidence: '78%' },
  { label: '年度', runway: '26.1', capital: '4.52', confidence: '83%' },
]

const activeRange = ref(0)
const activeSection = ref('product')
let lockedSection = ''
const selectedRisk = ref(0)
const demoStatus = ref('预约演示')

const currentRange = computed(() => ranges[activeRange.value] ?? ranges[0]!)

const risks = [
  { level: '高', name: '现金流风险', desc: '未来 45 天现金余额可能低于安全阈值', amount: '-4,560 万', time: '刚刚' },
  { level: '中', name: '客户信用风险', desc: 'A 级客户账期超出授信周期 15 天', amount: '980 万', time: '25 分钟前' },
  { level: '中', name: '预算偏差风险', desc: '销售费用预算消耗连续 2 个月超 10%', amount: '860 万', time: '1 小时前' },
  { level: '低', name: '利率波动风险', desc: '浮动利率敞口占比 32%，利率上升趋势', amount: '120 万', time: '3 小时前' },
]

const bars = [46, 68, 55, 72, 48, 30, 44, 70, 52, 35, 28, 22]
const costs = [18, 30, 24, 20, 42, 48, 39, 32, 28, 34, 40, 36]

const reserveDemo = () => {
  demoStatus.value = '已收到'
  window.setTimeout(() => {
    demoStatus.value = '预约演示'
  }, 1800)
}

const selectSection = (id: string) => {
  lockedSection = id
  activeSection.value = id
  window.setTimeout(() => {
    if (lockedSection === id) {
      lockedSection = ''
      updateActiveSection()
    }
  }, 900)
}

const updateActiveSection = () => {
  if (lockedSection) {
    activeSection.value = lockedSection
    return
  }

  const sections = ['product', 'risk', 'metrics', 'solution']
  let current = activeSection.value
  let distance = Number.POSITIVE_INFINITY
  sections.forEach((id) => {
    const el = document.getElementById(id)
    if (!el) return
    const rect = el.getBoundingClientRect()
    if (rect.bottom > 88 && rect.top < window.innerHeight) {
      const nextDistance = Math.abs(rect.top - 120)
      if (nextDistance < distance) {
        distance = nextDistance
        current = id
      }
    }
  })

  const solution = document.getElementById('solution')
  if (solution && solution.getBoundingClientRect().top <= 220) {
    current = 'solution'
  }

  activeSection.value = current
}

onMounted(() => {
  updateActiveSection()
  window.addEventListener('scroll', updateActiveSection, { passive: true })
})

onUnmounted(() => {
  window.removeEventListener('scroll', updateActiveSection)
})
</script>

<template>
  <main class="finance-page">
    <nav class="site-nav">
      <a class="brand" href="#top" aria-label="LedgerFlow 首页">
        <span class="brand-mark">LF</span>
        <span>
          <strong>LedgerFlow</strong>
          <small>CFO 数据 cockpit</small>
        </span>
      </a>

      <div class="nav-links" aria-label="主导航">
        <a href="#product" :class="{ active: activeSection === 'product' }" @click="selectSection('product')">产品</a>
        <a href="#solution" :class="{ active: activeSection === 'solution' }" @click="selectSection('solution')">解决方案</a>
        <a href="#risk" :class="{ active: activeSection === 'risk' }" @click="selectSection('risk')">风险中心</a>
        <a href="#metrics" :class="{ active: activeSection === 'metrics' }" @click="selectSection('metrics')">关键指标</a>
      </div>

      <button class="ghost-button">登录</button>
      <button class="primary-button" @click="reserveDemo">{{ demoStatus }}</button>
    </nav>

    <section id="top" class="hero-section">
      <div class="hero-copy">
        <p class="eyebrow">实时现金流 · 预算执行 · 风险预警</p>
        <h1>掌控全局财务，<br />驱动企业稳健增长</h1>
        <p class="hero-text">
          LedgerFlow 为 CFO 打造一块会思考的经营驾驶舱，把现金、预算、风险与预测压缩到一个实时决策平面。
        </p>

        <div class="hero-actions">
          <button class="primary-button large" @click="reserveDemo">{{ demoStatus }}</button>
          <a class="outline-button" href="#product">观看概览</a>
        </div>

        <div class="trust-row" aria-label="客户数量">
          <span>全球 500+ 企业 CFO 的共同选择</span>
          <strong>Hisense</strong>
          <strong>SANY</strong>
          <strong>ANTA</strong>
          <strong>SF Holding</strong>
        </div>
      </div>

      <section id="product" class="cockpit" aria-label="CFO 数据驾驶舱">
        <div class="cockpit-head">
          <div>
            <p>CFO 数据驾驶舱</p>
            <small>最后更新 · 2026-06-28 09:41</small>
          </div>
          <div class="range-tabs" aria-label="时间范围">
            <button
              v-for="(range, index) in ranges"
              :key="range.label"
              :class="{ active: activeRange === index }"
              @click="activeRange = index"
            >
              {{ range.label }}
            </button>
          </div>
        </div>

        <div class="kpi-grid">
          <article>
            <span>现金跑道</span>
            <strong>{{ currentRange.runway }} <small>个月</small></strong>
            <em>较上月 +2.3 月</em>
          </article>
          <article>
            <span>营运资金</span>
            <strong>￥{{ currentRange.capital }} <small>亿</small></strong>
            <em>较上月 +8.7%</em>
          </article>
          <article class="negative">
            <span>预算达成率</span>
            <strong>-6.3%</strong>
            <em>较上月 -1.8 pp</em>
          </article>
          <article>
            <span>预测置信度</span>
            <strong>{{ currentRange.confidence }}</strong>
            <em>较上月 +6 pp</em>
          </article>
        </div>

        <div class="chart-panel">
          <div class="chart-head">
            <strong>现金流预测（未来 12 个月）</strong>
            <span>单位：百万</span>
          </div>

          <div class="bars" aria-label="现金流柱状图">
            <span
              v-for="(bar, index) in bars"
              :key="index"
              class="bar"
              :style="{ '--bar': `${bar}%`, '--cost': `${costs[index]}%` }"
            ></span>
          </div>
          <div class="flow-line"></div>
          <div class="chart-tip">2026-01 现金缺口 <strong>￥236M</strong></div>
        </div>
      </section>
    </section>

    <section id="risk" class="insight-grid">
      <div class="risk-panel">
        <div class="section-head">
          <h2>风险预警</h2>
          <a href="#metrics">重测全部（12）</a>
        </div>

        <button
          v-for="(risk, index) in risks"
          :key="risk.name"
          class="risk-row"
          :class="{ active: selectedRisk === index }"
          @click="selectedRisk = index"
        >
          <span :class="['risk-badge', risk.level === '高' ? 'hot' : risk.level === '低' ? 'safe' : 'mid']">
            {{ risk.level }}
          </span>
          <span>
            <strong>{{ risk.name }}</strong>
            <small>{{ risk.desc }}</small>
            <em>预计影响：￥{{ risk.amount }}</em>
          </span>
          <time>{{ risk.time }}</time>
        </button>
      </div>

      <div class="budget-panel">
        <div class="section-head">
          <h2>预算执行概览</h2>
          <a href="#solution">查看详情</a>
        </div>

        <div class="donut-wrap">
          <div class="donut"><span>总预算<br />￥18.6 亿</span></div>
          <ul>
            <li><span></span>已完成 <strong>48.7%</strong></li>
            <li><span></span>已承诺 <strong>24.3%</strong></li>
            <li><span></span>预测完成 <strong>17.1%</strong></li>
            <li><span></span>未执行 <strong>9.9%</strong></li>
          </ul>
        </div>

        <table>
          <thead>
            <tr>
              <th>项目</th>
              <th>预算</th>
              <th>实际</th>
              <th>偏差率</th>
            </tr>
          </thead>
          <tbody>
            <tr>
              <td>营业收入</td>
              <td>10.80</td>
              <td>10.18</td>
              <td class="bad">-5.7%</td>
            </tr>
            <tr>
              <td>销售费用</td>
              <td>2.15</td>
              <td>2.48</td>
              <td class="bad">+15.3%</td>
            </tr>
            <tr>
              <td>管理费用</td>
              <td>1.25</td>
              <td>1.08</td>
              <td class="good">-13.6%</td>
            </tr>
            <tr>
              <td>资本支出</td>
              <td>2.55</td>
              <td>1.94</td>
              <td class="good">-23.9%</td>
            </tr>
          </tbody>
        </table>
      </div>
    </section>

    <section id="metrics" class="metric-section">
      <div class="section-head">
        <h2>关键财务指标</h2>
        <a href="#top">返回驾驶舱</a>
      </div>

      <div class="metric-grid">
        <article>
          <span>现金及等价物</span>
          <strong>￥2.48 亿</strong>
          <em>较上月 +6.3%</em>
          <i></i>
        </article>
        <article class="danger">
          <span>经营活动现金流</span>
          <strong>￥5,620 万</strong>
          <em>较上月 -8.7%</em>
          <i></i>
        </article>
        <article>
          <span>毛利率</span>
          <strong>23.6%</strong>
          <em>较上月 +1.2 pp</em>
          <i></i>
        </article>
        <article>
          <span>资产负债率</span>
          <strong>42.8%</strong>
          <em>较上月 -2.1 pp</em>
          <i></i>
        </article>
        <article>
          <span>应收账款周转天数</span>
          <strong>56 天</strong>
          <em>较上月 -6 天</em>
          <i></i>
        </article>
      </div>
    </section>

    <section id="solution" class="signal-section">
      <div>
        <p class="eyebrow">从数据到决策</p>
        <h2>让财务成为<strong>增长引擎</strong></h2>
        <p>
          打通数据孤岛，构建实时、协同、智能的财务中枢。让 CFO 与管理团队在不确定性中把握确定增长。
        </p>
        <button class="primary-button large" @click="reserveDemo">预约专项演示</button>
      </div>

      <div class="signal-map" aria-label="经营信号网络">
        <span>预测准确度提升 <strong>28%</strong></span>
        <span>财务月结效率提升 <strong>65%</strong></span>
        <span>风险响应时间缩短 <strong>70%</strong></span>
      </div>
    </section>
  </main>
</template>
