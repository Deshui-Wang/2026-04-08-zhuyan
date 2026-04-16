<script setup>
import { ref, computed } from 'vue'
import { 
  X, 
  Check, 
  Sparkles, 
  ArrowRight, 
  Zap,
  Briefcase,
  GraduationCap,
  BrainCircuit,
  DatabaseZap,
  Workflow,
  Search,
  MessageSquareText,
  UserCircle,
  Stethoscope,
  ChevronLeft
} from 'lucide-vue-next'

const props = defineProps({
  productsData: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['close', 'navigate'])

// --- Quick Match Data ---
const needs = [
  { id: 'research', label: '科研攻关与论文润色', icon: BrainCircuit, category: '智能助研' },
  { id: 'teaching', label: '智慧课堂与自动阅卷', icon: Zap, category: '智能助教' },
  { id: 'studying', label: '学情监测与心理健康', icon: GraduationCap, category: '智能伴学' },
  { id: 'management', label: '校园数字化管理', icon: DatabaseZap, category: '智能助管' },
  { id: 'employment', label: '模拟面试与精准就业', icon: Briefcase, category: '智能就业' },
  { id: 'foundation', label: '算力底座与架构支撑', icon: Workflow, category: '基座系统' }
]

const keywordMap = {
  research: ['论文', '写作', '科研', '专利', '数据分析', '查新', 'AIGC', '可视化', '润色', '助研', '创新'],
  teaching: ['阅卷', '直播', '教学', '课程', '班级', '教师', '实训', '档案', '助教', '工作台'],
  studying: ['学生', '心理', '健康', '学习计划', '习惯', '空间', '路径', '伴学', '评价'],
  management: ['奖助学金', '校园', '设施', '预约', '管理', '行政', '数据中台', '助管', '治理', '中枢', '决策', '安防'],
  employment: ['面试', '岗位', '求职', '招聘', '简历', '职业', '规划', '就业', '撮合'],
  foundation: ['算力', '调度', '云', '底座', '系统', '基础', '基座', 'OS']
}

// --- Diagnostic Mode Data ---
const diagnosticRoles = [
  {
    id: 'infoCenter',
    name: '信息中心/网络中心',
    desc: '关注底座与数据资产',
    symptoms: [
      { id: 'scat_comp', text: '学校算力资源分散，缺乏统一纳管与成本控制', recommendation: ['算力管理', '学校管理平台'] },
      { id: 'data_silo', text: '数据孤岛严重，业务系统互不通，缺乏全局视图', recommendation: ['学校管理平台', '心理健康顾问与生活助手'] },
      { id: 'res_conf', text: '校园公共资源管理混乱，使用率低', recommendation: ['校园设施预约与管理', '学校管理平台'] }
    ]
  },
  {
    id: 'eduOffice',
    name: '教务处/二级学院',
    desc: '关注教学质量与师生能力',
    symptoms: [
      { id: 'grading', text: '教师批改负担重，难以提供个性化建议', recommendation: ['智能辅助评分', 'AI学情分析', '个化学习路径规划'] },
      { id: 'ai_env', text: '师生对 AI 感到好奇但缺乏实务场景', recommendation: ['智谱中联-提示词实训', '智能体创作平台'] },
      { id: 'perf_ev', text: '缺乏数据支撑评估教师教学过程与绩效', recommendation: ['教师教学档案袋-3', '学校管理平台'] }
    ]
  },
  {
    id: 'studentAffairs',
    name: '学工部/辅导员体系',
    desc: '关注日常管理与安全预警',
    symptoms: [
      { id: 'scholarship', text: '奖助学金评审繁琐，存在“骗助”风险', recommendation: ['奖助学金申请与评审', '学校管理平台'] },
      { id: 'warning', text: '学业或心理隐患发现晚，缺乏早期预警', recommendation: ['学业发展预警平台', '心理健康顾问与生活助手'] },
      { id: 'growth', text: '希望提供统一的智能化成长辅助平台', recommendation: ['学生空间', '人才成长引擎（发展管理）'] }
    ]
  },
  {
    id: 'employment',
    name: '招生就业处',
    desc: '关注高质量就业率 KPI',
    symptoms: [
      { id: 'career_planning', text: '学生规划意识弱，大四才开始盲目找工作', recommendation: ['职业生涯规划', '人才成长引擎（发展管理）'] },
      { id: 'practical_skills', text: '实战能力弱（简历无亮点、面试易紧张）', recommendation: ['简历诊断', 'AI模拟面试'] },
      { id: 'matching', text: '岗位资源与能力画像不匹配，撮合效率低', recommendation: ['岗位撮合与精准就业平台', 'AI学情分析'] }
    ]
  },
  {
    id: 'research',
    name: '科研处/实验室',
    desc: '关注科研产出与协作效率',
    symptoms: [
      { id: 'proj_manage', text: '科研项目多且杂，流程难以数字化追踪', recommendation: ['科研项目管理'] },
      { id: 'collab_eff', text: '文献管理混乱，缺乏智能化协作工具', recommendation: ['论文写作助手（超智科研社）', '算力管理'] }
    ]
  }
]

// --- State Management ---
const advisorMode = ref('quick') // 'quick' or 'diagnostic'
const diagnosticStep = ref(1) // 1: Role, 2: Symptoms
const selectedRoleId = ref(null)
const selectedSymptomIds = ref([])

const selectedNeeds = ref([])
const userPrompt = ref('')
const isAnalyzing = ref(false)
const showResults = ref(false)

// Computed helpers
const currentRole = computed(() => diagnosticRoles.find(r => r.id === selectedRoleId.value))

// --- Logic ---
const switchMode = (mode) => {
  advisorMode.value = mode
  reset()
}

const toggleNeed = (id) => {
  const index = selectedNeeds.value.indexOf(id)
  if (index === -1) selectedNeeds.value.push(id)
  else selectedNeeds.value.splice(index, 1)
}

const selectRole = (id) => {
  selectedRoleId.value = id
  diagnosticStep.value = 2
}

const toggleSymptom = (id) => {
  const index = selectedSymptomIds.value.indexOf(id)
  if (index === -1) selectedSymptomIds.value.push(id)
  else selectedSymptomIds.value.splice(index, 1)
}

const startAnalysis = () => {
  if (advisorMode.value === 'quick') {
    if (userPrompt.value.trim()) {
      Object.entries(keywordMap).forEach(([id, keywords]) => {
        if (keywords.some(k => userPrompt.value.includes(k))) {
          if (!selectedNeeds.value.includes(id)) selectedNeeds.value.push(id)
        }
      })
    }
    if (selectedNeeds.value.length === 0) return
  } else {
    if (selectedSymptomIds.value.length === 0) return
  }

  isAnalyzing.value = true
  setTimeout(() => {
    isAnalyzing.value = false
    showResults.value = true
  }, 1500)
}

const recommendedProducts = computed(() => {
  const allProducts = Object.values(props.productsData).flat()
  const results = []

  if (advisorMode.value === 'quick') {
    selectedNeeds.value.forEach(needId => {
      const need = needs.find(n => n.id === needId)
      if (need && props.productsData[need.category]) {
        results.push(...props.productsData[need.category].slice(0, 3))
      }
    })
  } else {
    // Diagnostic logic
    const role = diagnosticRoles.find(r => r.id === selectedRoleId.value)
    if (role) {
      selectedSymptomIds.value.forEach(sId => {
        const symptom = role.symptoms.find(s => s.id === sId)
        if (symptom) {
          symptom.recommendation.forEach(pTitle => {
            const product = allProducts.find(p => p.title.includes(pTitle))
            if (product && !results.some(r => r.id === product.id)) {
              results.push(product)
            }
          })
        }
      })
    }
    // PRD Suggestion 2: Force bind basic system
    const basicSystem = allProducts.find(p => p.title.includes('算力管理') || p.title.includes('学校管理平台'))
    if (basicSystem && !results.some(r => r.id === basicSystem.id)) {
      results.push({ ...basicSystem, isBase: true })
    }
  }

  // Deduplicate
  return Array.from(new Set(results))
})

const reset = () => {
  selectedNeeds.value = []
  userPrompt.value = ''
  selectedRoleId.value = null
  selectedSymptomIds.value = []
  diagnosticStep.value = 1
  showResults.value = false
}

const handleOpenProduct = (product) => {
  emit('navigate', product)
}
</script>

<template>
  <div class="purchase-advisor-overlay" @click.self="$emit('close')">
    <div class="advisor-modal">
      <header class="modal-header">
        <div class="title-group">
          <div class="icon-orb" :class="{ 'diag-orb': advisorMode === 'diagnostic' }">
            <Stethoscope v-if="advisorMode === 'diagnostic'" :size="24" />
            <Sparkles v-else class="sparkle-icon" :size="24" />
          </div>
          <div>
            <h3>智能采购建议 <span class="mode-tag">{{ advisorMode === 'diagnostic' ? '诊断模式' : '极速模式' }}</span></h3>
            <p>{{ advisorMode === 'diagnostic' ? '针对性场景诊断，为您量身定制系统矩阵' : '基于业务需求，为您精准推荐产品矩阵' }}</p>
          </div>
        </div>
        <div class="header-actions">
          <button class="mode-switch-btn" @click="switchMode(advisorMode === 'quick' ? 'diagnostic' : 'quick')">
            {{ advisorMode === 'quick' ? '进入诊断模式' : '切换快速匹配' }}
          </button>
          <button class="close-btn" @click="$emit('close')">
            <X :size="20" />
          </button>
        </div>
      </header>

      <div class="modal-body">
        <!-- MODE: QUICK -->
        <div v-if="advisorMode === 'quick' && !isAnalyzing && !showResults" class="step-content">
          <div class="prompt-section">
            <div class="prompt-header">
              <MessageSquareText :size="18" class="prompt-icon" />
              <span>直接描述您的具体场景（可选）</span>
            </div>
            <div class="prompt-input-wrapper">
              <textarea 
                v-model="userPrompt" 
                placeholder="例如：我需要一个能够帮助老师进行日常阅卷，并且能分析学生学习进度的系统..."
                rows="3"
                class="prompt-textarea"
              ></textarea>
              <div class="prompt-hint">
                <Search :size="14" />
                <span>智能引擎将自动识别关键字进行匹配</span>
              </div>
            </div>
          </div>

          <div class="divider">
            <span>或者点击选择以下需求标签</span>
          </div>

          <div class="needs-grid">
            <div 
              v-for="need in needs" 
              :key="need.id"
              class="need-card"
              :class="{ active: selectedNeeds.includes(need.id) }"
              @click="toggleNeed(need.id)"
            >
              <div class="check-badge">
                <Check v-if="selectedNeeds.includes(need.id)" :size="12" />
              </div>
              <div class="need-icon">
                <component :is="need.icon" :size="28" />
              </div>
              <span class="need-label">{{ need.label }}</span>
            </div>
          </div>
        </div>

        <!-- MODE: DIAGNOSTIC -->
        <div v-if="advisorMode === 'diagnostic' && !isAnalyzing && !showResults" class="step-content diagnostic-step">
          <!-- Step 1: Role Selection -->
          <div v-if="diagnosticStep === 1">
            <div class="diag-header-text">
              <h4>请选择您的业务部门/角色身份</h4>
              <p>我们将根据您的角色背景提供针对性的诊断项</p>
            </div>
            <div class="roles-grid">
              <div 
                v-for="role in diagnosticRoles" 
                :key="role.id"
                class="role-card"
                @click="selectRole(role.id)"
              >
                <div class="role-icon">
                  <UserCircle :size="32" />
                </div>
                <h5>{{ role.name }}</h5>
                <p>{{ role.desc }}</p>
              </div>
            </div>
          </div>

          <!-- Step 2: Symptoms -->
          <div v-if="diagnosticStep === 2">
            <div class="diag-header-text has-back">
              <button class="back-link" @click="diagnosticStep = 1">
                <ChevronLeft :size="16" />
                返回身份选择
              </button>
              <h4>针对【{{ currentRole?.name }}】，您是否正面临以下痛点？</h4>
              <p>多选痛点场景，我们将为您构建完整的解决方案映射</p>
            </div>
            <div class="symptoms-list">
              <div 
                v-for="symptom in currentRole?.symptoms" 
                :key="symptom.id"
                class="symptom-item"
                :class="{ active: selectedSymptomIds.includes(symptom.id) }"
                @click="toggleSymptom(symptom.id)"
              >
                <div class="symptom-check">
                  <Check v-if="selectedSymptomIds.includes(symptom.id)" :size="14" />
                </div>
                <span class="symptom-text">{{ symptom.text }}</span>
              </div>
            </div>
          </div>
        </div>

        <!-- SHARED: Analysis State -->
        <div v-if="isAnalyzing" class="analysis-state">
          <div class="loading-spinner"></div>
          <p class="loading-text">{{ advisorMode === 'diagnostic' ? '正在进行场景建模与矩阵匹配...' : '正在匹配产品特性与需求场景...' }}</p>
        </div>

        <!-- SHARED: Results State -->
        <div v-if="showResults" class="results-state">
          <div class="results-header">
            <h4>根据诊断结果，推荐为您配置以下产品方案</h4>
            <button class="reselect-btn" @click="reset">重新开始</button>
          </div>

          <div class="recommendations-list">
            <div 
              v-for="product in recommendedProducts" 
              :key="product.id"
              class="recommendation-item"
              :class="{ 'base-item': product.isBase }"
              @click="handleOpenProduct(product)"
            >
              <div class="product-icon" :style="{ backgroundColor: `${product.color}15`, color: product.color }">
                <component :is="product.icon" :size="20" v-if="product.icon" />
                <Workflow :size="20" v-else />
              </div>
              <div class="product-info">
                <h5>{{ product.title }} <span v-if="product.isBase" class="base-tag">推荐配套基座</span></h5>
                <p>{{ product.description }}</p>
              </div>
              <div class="match-tag" :style="{ color: product.color }">
                匹配度 {{ product.isBase ? '60%' : '98%' }}
              </div>
            </div>
          </div>
          
          <div v-if="advisorMode === 'diagnostic'" class="diag-note">
            <DatabaseZap :size="14" />
            <span>注：以上 AI 业务的高效运转，依赖于统一的数据底座与算力支撑。</span>
          </div>

          <div class="final-cta">
            <button class="collab-btn" @click="$emit('close')">
              联系我们洽谈合作
            </button>
          </div>
        </div>

        <!-- SHARED: Footer Action for Step 1 -->
        <div v-if="!isAnalyzing && !showResults" class="footer-actions">
          <button 
            v-if="advisorMode === 'quick' || diagnosticStep === 2"
            class="analyze-btn" 
            :disabled="(advisorMode === 'quick' && !selectedNeeds.length && !userPrompt.trim()) || (advisorMode === 'diagnostic' && !selectedSymptomIds.length)"
            @click="startAnalysis"
          >
            <span>{{ advisorMode === 'diagnostic' ? '生成诊断处方' : '生成智能采购建议' }}</span>
            <ArrowRight :size="18" />
          </button>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.purchase-advisor-overlay {
  position: fixed;
  top: 0; left: 0;
  width: 100vw; height: 100vh;
  background: rgba(15, 23, 42, 0.4);
  backdrop-filter: blur(8px);
  z-index: 3000;
  display: flex;
  align-items: center; justify-content: center;
}

.advisor-modal {
  width: 720px;
  max-width: 95vw;
  background: #fff;
  border-radius: 24px;
  box-shadow: 0 40px 100px -20px rgba(0,0,0,0.25);
  overflow: hidden;
  animation: modalPop 0.4s cubic-bezier(0.16, 1, 0.3, 1);
}

@keyframes modalPop {
  from { transform: scale(0.95) translateY(20px); opacity: 0; }
  to { transform: scale(1) translateY(0); opacity: 1; }
}

.modal-header {
  padding: 24px 32px;
  border-bottom: 1px solid #f1f5f9;
  display: flex;
  justify-content: space-between;
  align-items: center;
  background: #fdfdfe;
}

.title-group {
  display: flex;
  align-items: center; gap: 16px;
}

.icon-orb {
  width: 48px; height: 48px;
  background: linear-gradient(135deg, #0f172a, #334155);
  border-radius: 14px;
  display: flex; align-items: center; justify-content: center;
  color: #fff;
}
.icon-orb.diag-orb {
  background: linear-gradient(135deg, #3b82f6, #8b5cf6);
}

.title-group h3 {
  font-size: 1.25rem; font-weight: 700;
  color: #0f172a; margin: 0;
  display: flex; align-items: center; gap: 8px;
}

.mode-tag {
  font-size: 0.75rem; background: #f1f5f9;
  padding: 2px 8px; border-radius: 6px;
  color: #64748b; font-weight: 500;
}

.title-group p {
  font-size: 0.85rem; color: #64748b; margin: 2px 0 0 0;
}

.header-actions {
  display: flex; align-items: center; gap: 12px;
}

.mode-switch-btn {
  background: #fff; border: 1px solid #e2e8f0;
  padding: 8px 16px; border-radius: 98px;
  font-size: 0.85rem; font-weight: 600;
  color: #0f172a; cursor: pointer;
  transition: all 0.2s;
}
.mode-switch-btn:hover {
  background: #f8fafc; border-color: #3b82f6; color: #3b82f6;
}

.close-btn {
  background: transparent; border: none;
  width: 36px; height: 36px;
  border-radius: 50%; cursor: pointer;
  color: #94a3b8; display: flex; align-items: center; justify-content: center;
}

.modal-body {
  padding: 32px;
  min-height: 520px;
}

/* Diagnostic Mode UI */
.diag-header-text {
  margin-bottom: 24px;
}
.diag-header-text h4 {
  font-size: 1.1rem; font-weight: 700; color: #0f172a; margin: 0 0 4px 0;
}
.diag-header-text p {
  font-size: 0.9rem; color: #64748b; margin: 0;
}
.diag-header-text.has-back {
  display: flex; flex-direction: column; gap: 8px;
}

.back-link {
  display: flex; align-items: center; gap: 4px;
  background: none; border: none;
  color: #3b82f6; font-size: 0.85rem; font-weight: 600;
  cursor: pointer; padding: 0; width: fit-content;
}

.roles-grid {
  display: grid; grid-template-columns: repeat(3, 1fr); gap: 16px;
}

.role-card {
  border: 1px solid #f1f5f9; border-radius: 16px;
  padding: 24px; text-align: center;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  cursor: pointer;
}
.role-card:hover {
  border-color: #3b82f6; background: #eff6ff;
  transform: translateY(-4px);
}
.role-icon {
  margin-bottom: 16px; color: #cbd5e1;
}
.role-card:hover .role-icon { color: #3b82f6; }
.role-card h5 { margin: 0 0 8px 0; font-size: 0.95rem; font-weight: 700; color: #0f172a; }
.role-card p { margin: 0; font-size: 0.8rem; color: #94a3b8; }

.symptoms-list {
  display: flex; flex-direction: column; gap: 12px;
}

.symptom-item {
  display: flex; align-items: center; gap: 16px;
  padding: 16px 20px; border-radius: 12px;
  border: 1px solid #f1f5f9; background: #fff;
  cursor: pointer; transition: all 0.2s;
}
.symptom-item:hover { background: #f8fafc; border-color: #e2e8f0; }
.symptom-item.active { border-color: #3b82f6; background: #eff6ff; }

.symptom-check {
  width: 20px; height: 20px;
  border: 2px solid #e2e8f0; border-radius: 50%;
  display: flex; align-items: center; justify-content: center;
  color: transparent; flex-shrink: 0;
}
.active .symptom-check {
  background: #3b82f6; border-color: #3b82f6; color: #fff;
}
.symptom-text { font-size: 0.95rem; font-weight: 500; color: #1e293b; }

/* Existing Styles Restored/Adapted */
.prompt-section { margin-bottom: 24px; }
.prompt-header { display: flex; align-items: center; gap: 8px; margin-bottom: 12px; font-size: 0.95rem; font-weight: 600; color: #1e293b; }
.prompt-icon { color: #3b82f6; }
.prompt-input-wrapper { background: #f8fafc; border: 1px solid #e2e8f0; border-radius: 12px; padding: 12px; transition: all 0.3s; }
.prompt-input-wrapper:focus-within { background: #fff; border-color: #3b82f6; box-shadow: 0 0 0 4px rgba(59, 130, 246, 0.05); }
.prompt-textarea { width: 100%; background: transparent; border: none; resize: none; font-family: inherit; font-size: 0.95rem; line-height: 1.5; color: #0f172a; outline: none; }
.prompt-hint { display: flex; align-items: center; gap: 6px; margin-top: 8px; font-size: 0.75rem; color: #94a3b8; }

.divider { display: flex; align-items: center; gap: 16px; margin-bottom: 24px; color: #94a3b8; font-size: 0.8rem; }
.divider::before, .divider::after { content: ''; flex: 1; height: 1px; background: #f1f5f9; }

.needs-grid { display: grid; grid-template-columns: repeat(2, 1fr); gap: 12px; }
.need-card { border: 1px solid #f1f5f9; border-radius: 12px; padding: 16px; display: flex; flex-direction: column; gap: 8px; cursor: pointer; transition: all 0.2s; position: relative; }
.need-card:hover { border-color: #cbd5e1; background: #f8fafc; }
.need-card.active { border-color: #3b82f6; background: #eff6ff; }
.check-badge { position: absolute; top: 12px; right: 12px; width: 18px; height: 18px; border-radius: 50%; border: 1px solid #e2e8f0; display: flex; align-items: center; justify-content: center; color: transparent; }
.active .check-badge { background: #3b82f6; border-color: #3b82f6; color: #fff; }
.need-icon { color: #64748b; }
.active .need-icon { color: #3b82f6; }
.need-label { font-weight: 600; color: #1e293b; font-size: 0.9rem; }

.footer-actions { display: flex; justify-content: center; margin-top: 32px; }
.analyze-btn { background: #0f172a; color: #fff; border: none; padding: 14px 28px; border-radius: 12px; font-weight: 600; display: flex; align-items: center; gap: 8px; cursor: pointer; transition: all 0.3s; }
.analyze-btn:disabled { opacity: 0.5; cursor: not-allowed; }
.analyze-btn:not(:disabled):hover { background: #1e293b; transform: translateY(-2px); box-shadow: 0 10px 20px -10px rgba(0,0,0,0.3); }

.analysis-state { display: flex; flex-direction: column; align-items: center; justify-content: center; height: 350px; gap: 20px; }
.loading-spinner { width: 48px; height: 48px; border: 4px solid #f1f5f9; border-top-color: #3b82f6; border-radius: 50%; animation: spin 1s linear infinite; }
@keyframes spin { to { transform: rotate(360deg); } }
.loading-text { color: #64748b; font-size: 1rem; }

.results-state { animation: fadeIn 0.5s ease; }
@keyframes fadeIn { from { opacity: 0; } to { opacity: 1; } }
.results-header { display: flex; justify-content: space-between; align-items: center; margin-bottom: 20px; }
.results-header h4 { font-size: 1.1rem; font-weight: 700; color: #0f172a; margin: 0; }
.reselect-btn { background: transparent; border: none; color: #3b82f6; font-weight: 600; cursor: pointer; font-size: 0.9rem; }

.recommendations-list { display: flex; flex-direction: column; gap: 10px; margin-bottom: 24px; max-height: 400px; overflow-y: auto; padding-right: 4px; }
.recommendation-item { display: flex; align-items: center; gap: 16px; padding: 14px 20px; background: #f8fafc; border-radius: 16px; border: 1px solid #f1f5f9; cursor: pointer; transition: all 0.2s; }
.recommendation-item:hover { background: #fff; border-color: #3b82f6; box-shadow: 0 8px 20px -8px rgba(0,0,0,0.1); }
.recommendation-item.base-item { border-style: dashed; border-color: #94a3b8; }

.product-icon { width: 40px; height: 40px; border-radius: 10px; display: flex; align-items: center; justify-content: center; flex-shrink: 0; }
.product-info { flex: 1; }
.product-info h5 { margin: 0 0 2px 0; font-weight: 700; color: #0f172a; display: flex; align-items: center; gap: 8px; }
.base-tag { font-size: 0.7rem; background: #f1f5f9; padding: 2px 6px; border-radius: 4px; color: #64748b; font-weight: 500; }
.product-info p { margin: 0; font-size: 0.85rem; color: #64748b; display: -webkit-box; -webkit-line-clamp: 1; line-clamp: 1; -webkit-box-orient: vertical; overflow: hidden; }
.match-tag { font-size: 0.75rem; font-weight: 700; background: #fff; padding: 4px 8px; border-radius: 6px; border: 1px solid currentColor; }

.diag-note { display: flex; align-items: center; gap: 8px; padding: 12px; background: #f1f5f9; border-radius: 8px; margin-bottom: 24px; font-size: 0.8rem; color: #64748b; }

.final-cta { display: flex; justify-content: center; }
.collab-btn { width: 100%; background: linear-gradient(135deg, #3b82f6, #8b5cf6); color: #fff; border: none; padding: 14px; border-radius: 12px; font-weight: 700; cursor: pointer; transition: all 0.3s; }
.collab-btn:hover { opacity: 0.9; transform: translateY(-2px); box-shadow: 0 10px 30px -10px rgba(59, 130, 246, 0.5); }
</style>
