<template>
  <div class="fingerprint-details-section">
    <h2 class="section-title">Fingerprint Raw Data</h2>
    <p class="section-subtitle">Complete fingerprint object with all components and metadata</p>

    <div v-if="!fingerprintData" class="no-data">
      <p>No fingerprint data available yet...</p>
    </div>

    <div v-else class="json-container">
      <div class="controls">
        <button @click="toggleCollapse" class="control-btn">
          {{ isCollapsed ? 'Expand All' : 'Collapse All' }}
        </button>
        <button @click="copyToClipboard" class="control-btn">
          {{ copyStatus || 'Copy JSON' }}
        </button>
      </div>

      <vue-json-pretty
        :data="fingerprintData"
        :deep="expandDepth"
        :show-line="true"
        :show-double-quotes="true"
        :highlight-selected-node="true"
        :collapsed-on-click-node="false"
        class="json-tree"
      />
    </div>

    <div v-if="fingerprintData" class="fingerprint-stats">
      <div class="stat-item">
        <span class="stat-label">Version:</span>
        <span class="stat-value">{{ fingerprintData.version || 'N/A' }}</span>
      </div>
      <div class="stat-item">
        <span class="stat-label">Thumbmark ID:</span>
        <code class="stat-value">{{ fingerprintData.thumbmark }}</code>
      </div>
      <div class="stat-item">
        <span class="stat-label">Components:</span>
        <span class="stat-value">{{ componentCount }} detected</span>
      </div>
      <div class="stat-item">
        <span class="stat-label">Generation Time:</span>
        <span class="stat-value">{{ fingerprintData.info?.time || 'N/A' }}ms</span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue'
import VueJsonPretty from 'vue-json-pretty'
import 'vue-json-pretty/lib/styles.css'

const props = defineProps({
  fingerprintData: {
    type: Object,
    default: null
  }
})

const expandDepth = ref(2)
const isCollapsed = ref(false)
const copyStatus = ref('')

const componentCount = computed(() => {
  if (!props.fingerprintData?.components) return 0
  return Object.keys(props.fingerprintData.components).length
})

const toggleCollapse = () => {
  isCollapsed.value = !isCollapsed.value
  expandDepth.value = isCollapsed.value ? 0 : 2
}

const copyToClipboard = async () => {
  try {
    await navigator.clipboard.writeText(JSON.stringify(props.fingerprintData, null, 2))
    copyStatus.value = 'Copied!'
    setTimeout(() => {
      copyStatus.value = ''
    }, 2000)
  } catch (error) {
    console.error('Failed to copy:', error)
    copyStatus.value = 'Failed'
    setTimeout(() => {
      copyStatus.value = ''
    }, 2000)
  }
}
</script>

<style scoped>
.fingerprint-details-section {
  margin-top: 40px;
  padding: 30px;
  background: white;
  border-radius: 15px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
}

.section-title {
  color: #333;
  font-size: 1.5rem;
  margin-bottom: 8px;
}

.section-subtitle {
  color: #666;
  margin-bottom: 25px;
  font-size: 1rem;
}

.no-data {
  text-align: center;
  padding: 40px;
  background: #f5f5f5;
  border-radius: 10px;
  color: #999;
}

.json-container {
  background: #1e1e1e;
  border-radius: 10px;
  padding: 20px;
  overflow: auto;
  max-height: 600px;
}

.controls {
  display: flex;
  gap: 10px;
  margin-bottom: 20px;
}

.control-btn {
  padding: 8px 16px;
  background: linear-gradient(135deg, #667eea, #764ba2);
  color: white;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-weight: 600;
  transition: all 0.3s;
}

.control-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(102, 126, 234, 0.3);
}

.control-btn:active {
  transform: translateY(0);
}

.json-tree {
  font-family: 'Monaco', 'Menlo', 'Courier New', monospace;
  font-size: 14px;
  line-height: 1.5;
}

:deep(.vjs-tree) {
  color: #e0e0e0 !important;
  background: transparent !important;
}

:deep(.vjs-tree .vjs-key) {
  color: #9cdcfe !important;
}

:deep(.vjs-tree .vjs-value-number) {
  color: #b5cea8 !important;
}

:deep(.vjs-tree .vjs-value-string) {
  color: #ce9178 !important;
}

:deep(.vjs-tree .vjs-value-boolean) {
  color: #569cd6 !important;
}

:deep(.vjs-tree .vjs-value-null) {
  color: #808080 !important;
}

:deep(.vjs-tree .vjs-tree-node:hover) {
  background-color: rgba(255, 255, 255, 0.05) !important;
}

:deep(.vjs-tree .vjs-tree-node.is-highlight) {
  background-color: rgba(102, 126, 234, 0.2) !important;
}

.fingerprint-stats {
  margin-top: 25px;
  padding: 20px;
  background: linear-gradient(135deg, #f5f7fa, #c3cfe2);
  border-radius: 10px;
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
  gap: 15px;
}

.stat-item {
  display: flex;
  align-items: center;
  gap: 10px;
  padding: 10px;
  background: white;
  border-radius: 8px;
}

.stat-label {
  font-weight: 600;
  color: #667eea;
  white-space: nowrap;
}

.stat-value {
  color: #333;
  word-break: break-all;
}

.stat-value code {
  font-family: 'Courier New', monospace;
  background: #f0f0f0;
  padding: 2px 6px;
  border-radius: 4px;
}

@media (max-width: 768px) {
  .json-container {
    max-height: 400px;
  }

  .fingerprint-stats {
    grid-template-columns: 1fr;
  }

  .controls {
    flex-direction: column;
  }

  .control-btn {
    width: 100%;
  }
}
</style>