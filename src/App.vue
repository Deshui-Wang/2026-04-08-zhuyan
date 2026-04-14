<script setup>
import { ref } from 'vue'
import {
  FileText,
  PenBox,
  Kanban,
  BrainCircuit,
  DatabaseZap,
  Workflow,
  ArrowRight,
  BookOpen,
  HeartPlus,
  Bot,
  GraduationCap,
  MessageSquare,
  Briefcase,
  Video,
  FileSearch,
  MapPin,
  LayoutGrid,
  ShoppingCart,
  Sparkles
} from 'lucide-vue-next'
import { ElMessage } from 'element-plus'
import PurchaseAdvisor from './components/PurchaseAdvisor.vue'

const activeCategory = ref('new AI工具')
const showPurchaseAdvisor = ref(false)

const categories = ['new AI工具', '基座系统', '智能助研', '智能助教', '智能伴学', '智能助管', '智能就业']

const productsData = {
  'new AI工具': [
    {
      id: 501,
      title: 'HappyLife AI',
      description: '多维度情感计算与生活智能辅助，为您打造更有温度的 AI 生活伴侣。',
      icon: HeartPlus,
      link: 'https://happylife.deshui27.cn/',
      stats: '情感智能驱动',
      color: '#f43f5e',
    },
    {
      id: 502,
      title: 'DS多媒体工厂',
      description: '一站式多媒体内容创作中枢，支持 AI 视频生成、智能剪辑与特效合成。',
      icon: Video,
      link: 'https://duomeiti.deshui27.cn/',
      stats: '专业创作引擎',
      color: '#8b5cf6',
    }
  ],
  '基座系统': [
    {
      id: 101,
      title: '算力管理',
      description: '统一调度与监控算力资源，支撑大规模 AI 模型训练与推理。',
      icon: Workflow,
      link: 'https://cloudmind.deshui27.cn/',
      stats: '弹性算力调度',
      color: '#475569',
    },
    {
      id: 104,
      title: '知识中心',
      description: '构建校园级私有知识库，支持非结构化数据的智能索引与知识图谱建模。',
      icon: BookOpen,
      link: 'https://knowledge.deshui27.cn/',
      stats: '向量化精准检索',
      color: '#475569',
    }
  ],
  '智能助研': [
    {
      id: 2,
      title: '论文写作助手（超智科研社）',
      description: '专业的学术与公文写作伴侣，提供段落润色、语法修正和文献引用辅助。',
      icon: PenBox,
      link: 'https://chaozhikys.deshui27.cn/',
      stats: '2.5w+ 用户使用',
      color: '#0ea5e9',
    },
    {
      id: 3,
      title: '科研项目管理',
      description: '一站式科研周期管理，协同申报、进度追踪、经费管控及结项归档。',
      icon: Kanban,
      link: 'https://xiangmu.deshui27.cn/',
      stats: '实时智能管控',
      color: '#0ea5e9',
    },
    {
      id: 4,
      title: '多媒体工坊',
      description: '深度赋能科研成果的可视化呈现，支持多模态内容智能生成。',
      icon: Video,
      link: 'https://duomeiti.deshui27.cn/',
      stats: 'AIGC 创意引擎',
      color: '#0ea5e9',
    },
    {
      id: 5,
      title: '生态价值测算',
      description: '基于地理空间数据与科研模型，提供精准的生态系统价值评估报表。',
      icon: MapPin,
      link: 'https://ncst.cailian.net/',
      stats: '量化模型驱动',
      color: '#0ea5e9',
    }
  ],
  '智能助教': [
    {
      id: 201,
      title: '数字人技术与应用',
      description: '打造可交互的 3D 数字教师，实现全天候的沉浸式智能教学体验。',
      icon: Bot,
      link: 'https://digihumanai.cailian.net/',
      stats: '虚拟仿真教学',
      color: '#8b5cf6',
    },
    {
      id: 202,
      title: '提示词实训',
      description: '系统化培训 AI 提示词工程，助力教师与学生掌握生成式 AI 核心技能。',
      icon: MessageSquare,
      link: 'https://prompt.deshui27.cn/',
      stats: '实战技能提升',
      color: '#8b5cf6',
    },
    {
      id: 203,
      title: '教师教学档案袋',
      description: '全面记录教学轨迹，支持自动化生成教学绩效评估与职称评审材料。',
      icon: FileSearch,
      link: 'https://teacher.deshui27.cn/',
      stats: '数字化成长足迹',
      color: '#8b5cf6',
    },
    {
      id: 204,
      title: '优播',
      description: '专业的直播教学与资源分发平台，集成 AI 实时翻译与智能互动。',
      icon: Video,
      link: 'https://youbo.cailian.net/',
      stats: '高清流畅直播',
      color: '#8b5cf6',
    },
    {
      id: 205,
      title: '商家管理中心（2.0）',
      description: '教育资源商城运营管理，赋能教学物资的高效流转与精准适配。',
      icon: Briefcase,
      link: 'https://aimarket.cailian.net',
      stats: '平台生态运营',
      color: '#8b5cf6',
    },
    {
      id: 206,
      title: '智能选品与营销中心',
      description: '利用大数据精准推荐教学产品，提供一站式数字化教育营销解决方案。',
      icon: Kanban,
      link: 'https://aimarketuat.cailian.net/#/smartSelection/index',
      stats: '精准数据画像',
      color: '#8b5cf6',
    },
    {
      id: 207,
      title: '智能辅助评分',
      description: '自动批改各类作业与试卷，提供多维度的反馈建议，减轻教师负担。',
      icon: PenBox,
      link: 'https://fuzhupingfen.deshui27.cn/',
      stats: '秒级自动阅卷',
      color: '#8b5cf6',
    },
    {
      id: 208,
      title: 'AI学情分析',
      description: '实时跟踪学生学习状态，通过多维建模为因材施教提供数据决策。',
      icon: GraduationCap,
      link: 'https://xueqingfenxi.deshui27.cn/',
      stats: '因材施教决策',
      color: '#8b5cf6',
    }
  ],
  '智能伴学': [
    {
      id: 7,
      title: '个化学习路径规划',
      description: '分析学生基础与目标，利用认知图谱智能生成千人千面的学习计划。',
      icon: BookOpen,
      link: 'https://xuexilujing.deshui27.cn/',
      stats: 'AI 定制导航',
      color: '#ec4899',
    },
    {
      id: 8,
      title: '心理健康顾问与生活助手',
      description: '24小时在线的数字心理伴侣，整合校园生活服务，提供全方位关怀。',
      icon: HeartPlus,
      link: 'https://health.deshui27.cn/',
      stats: '全天候心理支持',
      color: '#ec4899',
    },
    {
      id: 301,
      title: '人才成长引擎（发展管理）',
      description: '追踪学生全周期成长数据，智能匹配能力模型，驱动人才跨越式发展。',
      icon: Workflow,
      link: 'https://czyq.cailian.net',
      stats: '能力多维提升',
      color: '#ec4899',
    },
    {
      id: 302,
      title: '学生空间',
      description: '集成化的个人学习中心，整合笔记、作业、资源及社交化学习功能。',
      icon: Kanban,
      link: 'https://studentspace.deshui27.cn/',
      stats: '云端智慧空间',
      color: '#ec4899',
    },
    {
      id: 11,
      title: '学业发展预警平台',
      description: '通过数据挖掘潜在的学习危机，提供早期干预建议与补救策略。',
      icon: GraduationCap,
      link: 'https://xuexilujing.deshui27.cn/',
      stats: '护航学业轨迹',
      color: '#ec4899',
    }
  ],
  '智能助管': [
    {
      id: 401,
      title: '奖助学金申请与评审',
      description: '全流程数字化的奖助学金申报系统，确保评审过程公平、公正、透明。',
      icon: FileText,
      link: 'https://scholarship.cailian.net',
      stats: '阳光评审体系',
      color: '#10b981',
    },
    {
      id: 402,
      title: '校园设施预约与管理',
      description: '一键预约图书馆、自习室及体育场馆，提升校园公共资源利用率。',
      icon: MapPin,
      link: 'https://campusreserve.cailian.net',
      stats: '资源错峰动态调度',
      color: '#10b981',
    },
    {
      id: 403,
      title: '学校管理平台',
      description: '面向校方的数字化治理中枢，集成行政、教学、安防及舆情分析。',
      icon: LayoutGrid,
      link: 'https://schooldate.deshui27.cn/',
      stats: '智慧校园大脑',
      color: '#10b981',
    }
  ],
  '智能就业': [
    {
      id: 12,
      title: '岗位撮合与精准就业平台',
      description: '基于技能标签的双向匹配，打破信息差，让求职与招聘更智能高效。',
      icon: Briefcase,
      link: 'https://jiuye.deshui27.cn/',
      stats: '多维智能推荐',
      color: '#f59e0b',
    },
    {
      id: 13,
      title: 'AI模拟面试',
      description: '真实还原企业面试场景，提供语音对答、表情分析与结构化反馈。',
      icon: Video,
      link: 'https://mianshi.deshui27.cn/',
      stats: '专家级面试陪跑',
      color: '#f59e0b',
    },
    {
      id: 14,
      title: '简历诊断',
      description: '利用自然语言处理全面体检简历，提供针对性的内容优化与排版建议。',
      icon: FileSearch,
      link: 'https://jianli.deshui27.cn/',
      stats: '提高简历通过率',
      color: '#f59e0b',
    },
    {
      id: 15,
      title: '职业生涯规划',
      description: '深挖性格特质与行业前景，定制清晰连贯的长远职业发展蓝图。',
      icon: MapPin,
      link: 'https://guihua.deshui27.cn/',
      stats: '引领职场破局',
      color: '#f59e0b',
    }
  ]
}

const activeId = ref(null)

const handleNavigate = (link) => {
  if (link && link !== '#') {
    // Attempt copy credentials to clipboard
    const copyText = 'chaozhiren';
    navigator.clipboard.writeText(copyText).then(() => {
      ElMessage({
        message: '账号 chaozhiren 已复制，密码为 123456',
        type: 'success',
        duration: 3000
      })
      setTimeout(() => {
        window.open(link, '_blank');
      }, 1000)
    }).catch(() => {
      window.open(link, '_blank');
    })
  }
}
</script>

<template>
  <div class="portal-container">
    <header class="navbar">
      <div class="logo">
        <BrainCircuit class="logo-icon" :size="32" />
        <span class="logo-text">智域 <span>AIOS</span></span>
      </div>
      <div class="nav-links">
        <el-button type="primary" plain class="login-btn">登录工作台</el-button>
      </div>
    </header>

    <main class="hero-section">
      <div class="hero-content">
        <h1 class="glow-text">
          科研创新与智能教育的<br/>
          <span class="gradient-text">数字超级引擎</span>
        </h1>
        <p class="hero-subtext">
          汇聚全维数据体系，打造一体化生成式工作流，解锁产学研的高效协作与深度创新。
        </p>
      </div>
    </main>

    <section class="products-section">
      <div class="tabs-container">
        <div class="category-tabs">
          <button 
            v-for="cat in categories" 
            :key="cat"
            class="tab-btn"
            :class="{ active: activeCategory === cat }"
            @click="activeCategory = cat"
          >
            {{ cat }}
          </button>
        </div>
        <div class="actions-group">
          <button class="purchase-advisor-trigger" @click="showPurchaseAdvisor = true">
            <Sparkles :size="18" class="sparkle-icon" />
            <span>采购建议</span>
          </button>
        </div>
      </div>
      
      <div class="products-list">
        <div 
          v-for="item in productsData[activeCategory]" 
          :key="item.id"
          class="product-card"
          @mouseenter="activeId = item.id"
          @mouseleave="activeId = null"
          @click="handleNavigate(item.link)"
        >
          <div class="card-glow" :style="{ background: `radial-gradient(circle at right, ${item.color}33, transparent 70%)`, opacity: activeId === item.id ? 1 : 0 }"></div>
          
          <div class="card-content">
            <div class="icon-wrapper" :style="{ backgroundColor: `${item.color}1A`, color: item.color }">
              <img v-if="item.iconUrl" :src="item.iconUrl" :alt="item.title" class="custom-icon" />
              <component v-else :is="item.icon" :size="32" />
            </div>
            
            <div class="product-info">
              <h3 class="product-title">{{ item.title }}</h3>
              <p class="product-desc">{{ item.description }}</p>
            </div>

            <div class="product-meta">
              <span class="meta-tag" :style="{ backgroundColor: item.color }">
                <span class="dot"></span>
                {{ item.stats }}
              </span>
              <el-button 
                round 
                class="enter-btn"
                :class="{ 'is-active': activeId === item.id }"
                :style="activeId === item.id ? { backgroundColor: item.color, borderColor: item.color, color: '#fff' } : {}"
                @click.stop="handleNavigate(item.link)"
              >
                进入产品
                <ArrowRight class="btn-icon" :size="16" />
              </el-button>
            </div>
          </div>
        </div>
      </div>
    </section>

    <!-- Background decoration -->
    <div class="bg-grid"></div>

    <!-- Purchase Advisor Overlay -->
    <transition name="fade">
      <PurchaseAdvisor 
        v-if="showPurchaseAdvisor" 
        :productsData="productsData" 
        @close="showPurchaseAdvisor = false"
        @navigate="handleNavigate"
      />
    </transition>

  </div>
</template>

<style scoped>
.portal-container {
  display: flex;
  flex-direction: column;
  min-height: 100vh;
  position: relative;
  z-index: 1;
}

/* Navbar - Apple Style */
.navbar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.8rem 2vw;
  background: var(--glass-bg);
  backdrop-filter: var(--glass-blur);
  -webkit-backdrop-filter: var(--glass-blur);
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  z-index: 50;
  transition: all 0.3s ease;
  border-bottom: none;
}

.logo {
  display: flex;
  align-items: center;
  gap: 8px;
}

.logo-icon {
  color: var(--text-primary);
}

.logo-text {
  font-size: 1.1rem;
  font-weight: 600;
  color: var(--text-primary);
  letter-spacing: -0.01em;
}

.logo-text span {
  font-weight: 400;
  color: var(--text-secondary);
}

.login-btn {
  border-radius: 980px !important;
  font-size: 0.8rem !important;
  padding: 8px 16px !important;
  background-color: var(--text-primary) !important;
  color: #fff !important;
  border: none !important;
}
.login-btn:hover {
  background-color: rgba(0,0,0,0.8) !important;
}

/* Hero Section */
.hero-section {
  margin-top: 80px;
  padding: 5rem 2rem 3rem 2rem;
  text-align: center;
  position: relative;
}

.glow-text {
  font-size: 3.8rem;
  font-weight: 700;
  line-height: 1.05;
  color: var(--text-primary);
  margin: 0 0 1rem 0;
  letter-spacing: -0.015em;
}

.gradient-text {
  color: var(--primary);
  background: none;
  -webkit-text-fill-color: initial;
}

.hero-subtext {
  font-size: 1.3rem;
  font-weight: 400;
  color: var(--text-secondary);
  margin: 0 auto;
  line-height: 1.4;
  letter-spacing: -0.01em;
}

/* Products Section */
.products-section {
  margin: 0 auto 6rem auto;
  padding: 0 2rem;
  width: 100%;
}

.section-title {
  font-size: 1.5rem;
  font-weight: 600;
  color: var(--text-primary);
  letter-spacing: -0.01em;
  margin-bottom: 2.5rem;
  text-align: center;
}

.section-title::after {
  display: none;
}

/* Tabs Container */
.tabs-container {
  display: flex;
  justify-content: center;
  align-items: center;
  margin-bottom: 3.5rem;
  width: 100%;
  position: relative;
}

/* Category Tabs */
.category-tabs {
  display: flex;
  justify-content: center;
  align-items: center;
  gap: 0.5rem;
  background: #f1f1f2;
  padding: 6px;
  border-radius: 98px;
  width: fit-content;
}

.tab-btn {
  background: transparent;
  border: none;
  border-radius: 98px;
  padding: 10px 24px;
  font-size: 1.05rem;
  font-weight: 500;
  color: var(--text-secondary);
  cursor: pointer;
  transition: all 0.3s ease;
  font-family: inherit;
  letter-spacing: -0.01em;
}

.tab-btn:hover {
  color: var(--text-primary);
}

.tab-btn.active {
  background: #fff;
  color: var(--text-primary);
  font-weight: 600;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04), 0 1px 2px rgba(0, 0, 0, 0.02);
}

.actions-group {
  position: absolute;
  right: 0;
  display: flex;
  align-items: center;
  gap: 12px;
}

.purchase-advisor-trigger {
  display: flex;
  align-items: center;
  gap: 8px;
  background: linear-gradient(135deg, #0f172a, #334155);
  color: #fff;
  border: none;
  border-radius: 98px;
  padding: 10px 20px;
  font-size: 0.95rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.04);
}

.purchase-advisor-trigger .sparkle-icon {
  color: #fbbf24;
}

.purchase-advisor-trigger:hover {
  transform: translateY(-2px);
  box-shadow: 0 8px 20px rgba(0, 0, 0, 0.1);
  opacity: 0.95;
}

.fade-enter-active,
.fade-leave-active {
  transition: opacity 0.3s ease;
}

.fade-enter-from,
.fade-leave-to {
  opacity: 0;
}

/* Product Cards */
.products-list {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 1.5rem;
}

.product-card {
  position: relative;
  background: var(--bg-surface);
  border: 1px solid rgba(0,0,0,0.04);
  padding: 3rem 2.5rem;
  overflow: hidden;
  transition: all 0.3s cubic-bezier(0.25, 0.8, 0.25, 1);
  cursor: pointer;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.03);
  display: flex;
  flex-direction: column;
  justify-content: space-between;
  min-height: 480px;
}

.product-card:hover {
  transform: scale(1.01);
  box-shadow: 0 16px 40px rgba(0, 0, 0, 0.08);
  border-color: rgba(0,0,0,0.08);
}

.card-content {
  position: relative;
  z-index: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  text-align: center;
  gap: 1.5rem;
  height: 100%;
}

.icon-wrapper {
  width: 80px;
  height: 80px;
  border-radius: 20px;
  display: flex;
  align-items: center;
  justify-content: center;
  flex-shrink: 0;
  box-shadow: inset 0 2px 4px rgba(255,255,255,0.5), 0 2px 8px rgba(0,0,0,0.04);
  margin-bottom: 0.5rem;
  overflow: hidden;
}

.custom-icon {
  width: 100%;
  height: 100%;
  object-fit: cover;
}

.product-info {
  flex: 1;
  display: flex;
  flex-direction: column;
  align-items: center;
  max-width: 85%;
}

.product-title {
  font-size: 1.8rem;
  font-weight: 600;
  color: var(--text-primary);
  margin: 0 0 0.8rem 0;
  letter-spacing: -0.015em;
  line-height: 1.1;
}

.product-desc {
  font-size: 1.1rem;
  color: var(--text-secondary);
  margin: 0;
  line-height: 1.4;
  letter-spacing: -0.01em;
}

.product-meta {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 1.5rem;
  margin-top: auto;
  padding-top: 2rem;
}

.meta-tag {
  display: flex;
  align-items: center;
  gap: 6px;
  font-size: 0.8rem;
  font-weight: 500;
  color: #fff;
  padding: 4px 10px;
  border-radius: 980px;
}

.dot {
  width: 6px;
  height: 6px;
  border-radius: 50%;
  background-color: #fff !important;
}

.enter-btn {
  background: #f5f5f7;
  border-color: transparent;
  color: var(--text-primary);
  font-size: 0.85rem;
  font-weight: 500;
  padding: 8px 16px;
  border-radius: 980px;
  transition: all 0.3s ease;
  display: flex;
  align-items: center;
  gap: 4px;
}

.enter-btn:hover {
  background: var(--text-primary) !important;
  color: #fff !important;
}

.btn-icon {
  transition: transform 0.3s ease;
}

.enter-btn.is-active .btn-icon {
  transform: translateX(3px);
}

/* Grid Decoration */
.bg-grid {
  display: none;
}

@media (max-width: 768px) {
  .navbar {
    padding: 1rem 2rem;
  }
  
  .glow-text {
    font-size: 2.5rem;
  }
  
  .card-content {
    flex-direction: column;
    align-items: flex-start;
    gap: 1.25rem;
  }
  
  .product-meta {
    width: 100%;
    flex-direction: row;
    justify-content: space-between;
    align-items: center;
  }
}
</style>
