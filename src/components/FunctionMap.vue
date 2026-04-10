<script setup>
import {
  X,
  ArrowUpRight,
  DatabaseZap,
  BrainCircuit,
  GraduationCap,
  Briefcase,
  Layers,
  Search,
  BookOpen,
  LayoutGrid
} from 'lucide-vue-next'
import { ref, computed } from 'vue'

const props = defineProps({
  productsData: {
    type: Object,
    required: true
  }
})

const emit = defineEmits(['close', 'navigate'])
const searchQuery = ref('')

const filteredProductsData = computed(() => {
  if (!searchQuery.value) return props.productsData
  
  const filtered = {}
  const query = searchQuery.value.toLowerCase()
  
  Object.entries(props.productsData).forEach(([category, products]) => {
    const matchingProducts = products.filter(p => 
      p.title.toLowerCase().includes(query) || 
      (p.description && p.description.toLowerCase().includes(query))
    )
    if (matchingProducts.length > 0) {
      filtered[category] = matchingProducts
    }
  })
  return filtered
})

const handleProductClick = (link) => {
  emit('navigate', link)
}

const getCategoryColor = (category) => {
  const colors = {
    '基座系统': '#475569',
    '智能助研': '#0ea5e9',
    '智能助教': '#8b5cf6',
    '智能伴学': '#ec4899',
    '智能助管': '#10b981',
    '智能就业': '#f59e0b'
  }
  return colors[category] || '#6366f1'
}

const getCategoryIcon = (category) => {
  const icons = {
    '基座系统': Layers,
    '智能助研': BrainCircuit,
    '智能助教': LayoutGrid,
    '智能伴学': GraduationCap,
    '智能助管': DatabaseZap,
    '智能就业': Briefcase
  }
  return icons[category] || BookOpen
}
</script>

<template>
  <div class="function-map-page">
    <header class="map-header">
      <div class="header-content">
        <div class="header-left">
          <h2 class="title">智域 AIOS 全平台矩阵</h2>
          <p class="subtitle">汇聚科研、教学、伴学、管理与就业的全维度智能系统</p>
        </div>
        
        <div class="header-actions">
          <div class="search-box">
            <Search :size="18" class="search-icon" />
            <input v-model="searchQuery" type="text" placeholder="搜索系统名称或功能..." />
          </div>
          <button class="close-btn" @click="$emit('close')">
            <X :size="24" />
          </button>
        </div>
      </div>
    </header>

    <main class="matrix-main">
      <div class="matrix-rows">
        <div 
          v-for="(products, category) in filteredProductsData" 
          :key="category" 
          class="category-row"
          :style="{ '--row-color': getCategoryColor(category) }"
        >
          <div class="category-info">
            <div class="category-icon-wrapper">
              <component :is="getCategoryIcon(category)" :size="20" />
            </div>
            <h3 class="category-name">{{ category }}</h3>
            <span class="category-count">{{ products.length }}</span>
          </div>

          <div class="products-horizontal-grid">
            <div 
              v-for="product in products" 
              :key="product.id" 
              class="matrix-product-card"
              @click="handleProductClick(product.link)"
              :style="{ '--product-color': product.color }"
            >
              <div class="card-top">
                <div class="mini-icon" :style="{ backgroundColor: `${product.color}15`, color: product.color }">
                  <img v-if="product.iconUrl" :src="product.iconUrl" :alt="product.title" class="product-img" />
                  <component v-else :is="product.icon" :size="18" />
                </div>
                <ArrowUpRight class="jump-icon" :size="14" />
              </div>
              <div class="card-bottom">
                <h4 class="mini-title">{{ product.title }}</h4>
                <p class="mini-stats">{{ product.stats }}</p>
              </div>
            </div>
          </div>
        </div>
      </div>
    </main>

    <!-- Background Decor -->
    <div class="bg-elements">
      <div class="glow-orb orb-1"></div>
      <div class="glow-orb orb-2"></div>
    </div>
  </div>
</template>

<style scoped>
.function-map-page {
  position: fixed;
  top: 0;
  left: 0;
  width: 100vw;
  height: 100vh;
  background: #ffffff;
  z-index: 2000;
  overflow-y: auto;
  display: flex;
  flex-direction: column;
  animation: pageFadeIn 0.5s cubic-bezier(0.16, 1, 0.3, 1);
}

@keyframes pageFadeIn {
  from { opacity: 0; filter: blur(10px); }
  to { opacity: 1; filter: blur(0); }
}

.map-header {
  padding: 32px 6vw;
  background: rgba(255, 255, 255, 0.8);
  backdrop-filter: blur(20px);
  -webkit-backdrop-filter: blur(20px);
  border-bottom: 1px solid #f1f5f9;
  position: sticky;
  top: 0;
  z-index: 100;
}

.header-content {
  display: flex;
  justify-content: space-between;
  align-items: center;
  max-width: 1400px;
  margin: 0 auto;
  width: 100%;
}

.title {
  font-size: 1.8rem;
  font-weight: 700;
  color: #0f172a;
  margin: 0;
  letter-spacing: -0.02em;
}

.subtitle {
  font-size: 0.95rem;
  color: #64748b;
  margin: 4px 0 0 0;
}

.header-actions {
  display: flex;
  align-items: center;
  gap: 20px;
}

.search-box {
  display: flex;
  align-items: center;
  background: #f1f5f9;
  border-radius: 12px;
  padding: 0 16px;
  width: 300px;
  height: 44px;
  transition: all 0.3s ease;
  border: 1px solid transparent;
}

.search-box:focus-within {
  background: #fff;
  border-color: #3b82f6;
  box-shadow: 0 0 0 4px rgba(59, 130, 246, 0.1);
}

.search-icon {
  color: #94a3b8;
  margin-right: 10px;
}

.search-box input {
  background: transparent;
  border: none;
  font-size: 0.9rem;
  color: #1e293b;
  width: 100%;
  outline: none;
}

.close-btn {
  background: #f1f5f9;
  border: none;
  width: 44px;
  height: 44px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  color: #64748b;
  transition: all 0.2s ease;
}

.close-btn:hover {
  background: #e2e8f0;
  color: #0f172a;
  transform: rotate(90deg);
}

.matrix-main {
  flex: 1;
  padding: 40px 6vw;
  max-width: 1400px;
  margin: 0 auto;
  width: 100%;
}

.matrix-rows {
  display: flex;
  flex-direction: column;
  gap: 40px;
}

.category-row {
  display: flex;
  gap: 32px;
  animation: rowSlideIn 0.6s cubic-bezier(0.16, 1, 0.3, 1) both;
}

@keyframes rowSlideIn {
  from { opacity: 0; transform: translateX(-20px); }
  to { opacity: 1; transform: translateX(0); }
}

.category-info {
  width: 180px;
  flex-shrink: 0;
  display: flex;
  flex-direction: column;
  gap: 12px;
  padding-top: 8px;
}

.category-icon-wrapper {
  width: 40px;
  height: 40px;
  border-radius: 10px;
  background: var(--row-color);
  color: white;
  display: flex;
  align-items: center;
  justify-content: center;
  box-shadow: 0 8px 16px -4px var(--row-color);
}

.category-name {
  font-size: 1.25rem;
  font-weight: 700;
  color: #0f172a;
  margin: 0;
}

.category-count {
  font-size: 0.8rem;
  font-weight: 600;
  color: #94a3b8;
  background: #f1f5f9;
  padding: 2px 10px;
  border-radius: 20px;
  width: fit-content;
}

.products-horizontal-grid {
  flex: 1;
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  gap: 16px;
}

.matrix-product-card {
  background: #fff;
  border: 1px solid #f1f5f9;
  border-radius: 16px;
  padding: 20px;
  cursor: pointer;
  transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
  display: flex;
  flex-direction: column;
  gap: 16px;
  position: relative;
  overflow: hidden;
}

.matrix-product-card:hover {
  border-color: var(--product-color);
  transform: translateY(-4px);
  box-shadow: 0 12px 24px -8px rgba(0,0,0,0.1);
}

.matrix-product-card::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 4px;
  height: 100%;
  background: var(--product-color);
  opacity: 0;
  transition: opacity 0.3s ease;
}

.matrix-product-card:hover::before {
  opacity: 1;
}

.card-top {
  display: flex;
  justify-content: space-between;
  align-items: flex-start;
}

.mini-icon {
  width: 44px;
  height: 44px;
  border-radius: 12px;
  display: flex;
  align-items: center;
  justify-content: center;
}

.product-img {
  width: 100%;
  height: 100%;
  object-fit: cover;
  border-radius: 12px;
}

.jump-icon {
  color: #cbd5e1;
  transform: translate(4px, -4px);
  transition: all 0.3s ease;
}

.matrix-product-card:hover .jump-icon {
  color: var(--product-color);
  transform: translate(0, 0);
}

.card-bottom {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.mini-title {
  font-size: 1rem;
  font-weight: 600;
  color: #1e293b;
  margin: 0;
  line-height: 1.3;
}

.mini-stats {
  font-size: 0.75rem;
  color: #94a3b8;
  font-weight: 500;
}

/* Decor */
.bg-elements {
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  pointer-events: none;
  z-index: 10;
  overflow: hidden;
}

.glow-orb {
  position: absolute;
  width: 600px;
  height: 600px;
  border-radius: 50%;
  filter: blur(120px);
  opacity: 0.08;
}

.orb-1 {
  background: #3b82f6;
  top: -200px;
  right: -200px;
}

.orb-2 {
  background: #ec4899;
  bottom: -200px;
  left: -200px;
}

@media (max-width: 1024px) {
  .category-row {
    flex-direction: column;
    gap: 16px;
  }
  .category-info {
    width: 100%;
    flex-direction: row;
    align-items: center;
    justify-content: space-between;
  }
}

@media (max-width: 640px) {
  .header-content {
    flex-direction: column;
    align-items: flex-start;
    gap: 16px;
  }
  .search-box {
    width: 100%;
  }
}
</style>
