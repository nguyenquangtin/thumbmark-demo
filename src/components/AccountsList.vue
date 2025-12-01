<template>
  <div v-if="accounts.length > 0" class="accounts-section">
    <div class="section-header">
      <h2>Registered Accounts ({{ accounts.length }})</h2>
      <button @click="$emit('clear-accounts')" class="clear-btn">
        Clear All
      </button>
    </div>

    <div class="accounts-grid">
      <div
        v-for="(account, index) in sortedAccounts"
        :key="index"
        class="account-card"
        :class="{ 'same-fingerprint': isSameFingerprint(account) }"
      >
        <div class="account-header">
          <span class="account-index">#{{ index + 1 }}</span>
          <span v-if="isSameFingerprint(account)" class="same-device-badge">
            Same Device
          </span>
        </div>

        <div class="account-info">
          <div class="info-row">
            <span class="label">Username:</span>
            <span class="value">{{ account.username }}</span>
          </div>
          <div class="info-row">
            <span class="label">Email:</span>
            <span class="value">{{ account.email }}</span>
          </div>
          <div class="info-row">
            <span class="label">Fingerprint:</span>
            <code class="fingerprint-code">{{ truncateFingerprint(account.fingerprint) }}</code>
          </div>
          <div class="info-row">
            <span class="label">Created:</span>
            <span class="value">{{ formatDate(account.createdAt) }}</span>
          </div>
        </div>

        <div v-if="groupedByFingerprint[account.fingerprint] > 1" class="duplicate-warning">
          ⚠️ {{ groupedByFingerprint[account.fingerprint] }} accounts from this device
        </div>
      </div>
    </div>

    <div class="statistics">
      <h3>Statistics</h3>
      <div class="stats-grid">
        <div class="stat-card">
          <span class="stat-value">{{ totalAccounts }}</span>
          <span class="stat-label">Total Accounts</span>
        </div>
        <div class="stat-card">
          <span class="stat-value">{{ uniqueFingerprints }}</span>
          <span class="stat-label">Unique Devices</span>
        </div>
        <div class="stat-card">
          <span class="stat-value">{{ averageAccountsPerDevice }}</span>
          <span class="stat-label">Avg per Device</span>
        </div>
        <div class="stat-card" :class="{ 'warning': suspiciousDevices > 0 }">
          <span class="stat-value">{{ suspiciousDevices }}</span>
          <span class="stat-label">Suspicious Devices</span>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed, inject } from 'vue'
import { getThumbmark } from '@thumbmarkjs/thumbmarkjs'

const props = defineProps({
  accounts: {
    type: Array,
    default: () => []
  }
})

defineEmits(['clear-accounts'])

const accountLimit = inject('accountLimit', 2)
let currentFingerprint = null

getThumbmark().then(result => {
  // Extract the thumbmark string from the result object
  currentFingerprint = result.thumbmark || result
})

const sortedAccounts = computed(() => {
  return [...props.accounts].sort((a, b) =>
    new Date(b.createdAt) - new Date(a.createdAt)
  )
})

const groupedByFingerprint = computed(() => {
  const groups = {}
  props.accounts.forEach(account => {
    groups[account.fingerprint] = (groups[account.fingerprint] || 0) + 1
  })
  return groups
})

const totalAccounts = computed(() => props.accounts.length)

const uniqueFingerprints = computed(() => {
  return new Set(props.accounts.map(a => a.fingerprint)).size
})

const averageAccountsPerDevice = computed(() => {
  if (uniqueFingerprints.value === 0) return '0'
  return (totalAccounts.value / uniqueFingerprints.value).toFixed(1)
})

const suspiciousDevices = computed(() => {
  return Object.values(groupedByFingerprint.value)
    .filter(count => count > accountLimit).length
})

const isSameFingerprint = (account) => {
  return account.fingerprint === currentFingerprint
}

const truncateFingerprint = (fp) => {
  if (!fp) return 'N/A'
  return fp.substring(0, 12) + '...'
}

const formatDate = (dateString) => {
  if (!dateString) return 'Unknown'
  const date = new Date(dateString)
  return date.toLocaleString('en-US', {
    month: 'short',
    day: 'numeric',
    hour: '2-digit',
    minute: '2-digit'
  })
}
</script>

<style scoped>
.accounts-section {
  margin-top: 40px;
  padding: 30px;
  background: white;
  border-radius: 15px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
}

.section-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 25px;
}

.section-header h2 {
  color: #333;
  font-size: 1.5rem;
}

.clear-btn {
  padding: 8px 20px;
  background: #ff5252;
  color: white;
  border: none;
  border-radius: 6px;
  cursor: pointer;
  font-weight: 600;
  transition: all 0.3s;
}

.clear-btn:hover {
  background: #ff1744;
  transform: translateY(-2px);
  box-shadow: 0 5px 15px rgba(255, 82, 82, 0.3);
}

.accounts-grid {
  display: grid;
  grid-template-columns: repeat(auto-fill, minmax(300px, 1fr));
  gap: 20px;
  margin-bottom: 30px;
}

.account-card {
  background: #f8f9fa;
  border-radius: 10px;
  padding: 20px;
  border: 2px solid transparent;
  transition: all 0.3s;
}

.account-card.same-fingerprint {
  border-color: #667eea;
  background: linear-gradient(135deg, #f5f7fa, #e8eaf6);
}

.account-card:hover {
  transform: translateY(-3px);
  box-shadow: 0 10px 20px rgba(0, 0, 0, 0.1);
}

.account-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
}

.account-index {
  background: #667eea;
  color: white;
  padding: 4px 10px;
  border-radius: 20px;
  font-size: 0.85rem;
  font-weight: 600;
}

.same-device-badge {
  background: #4caf50;
  color: white;
  padding: 4px 12px;
  border-radius: 20px;
  font-size: 0.75rem;
  font-weight: 600;
}

.account-info {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.info-row {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.info-row .label {
  color: #666;
  font-size: 0.9rem;
}

.info-row .value {
  color: #333;
  font-weight: 500;
  font-size: 0.9rem;
}

.fingerprint-code {
  font-family: 'Courier New', monospace;
  background: #e0e0e0;
  padding: 2px 8px;
  border-radius: 4px;
  font-size: 0.85rem;
}

.duplicate-warning {
  margin-top: 10px;
  padding: 8px;
  background: #fff3e0;
  color: #e65100;
  border-radius: 6px;
  font-size: 0.85rem;
  text-align: center;
}

.statistics {
  margin-top: 30px;
}

.statistics h3 {
  color: #333;
  margin-bottom: 20px;
  font-size: 1.3rem;
}

.stats-grid {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
  gap: 15px;
}

.stat-card {
  background: linear-gradient(135deg, #667eea, #764ba2);
  color: white;
  padding: 20px;
  border-radius: 10px;
  text-align: center;
  display: flex;
  flex-direction: column;
  gap: 5px;
}

.stat-card.warning {
  background: linear-gradient(135deg, #ff5252, #ff1744);
}

.stat-value {
  font-size: 2rem;
  font-weight: bold;
}

.stat-label {
  font-size: 0.9rem;
  opacity: 0.9;
}

@media (max-width: 768px) {
  .accounts-grid {
    grid-template-columns: 1fr;
  }

  .stats-grid {
    grid-template-columns: repeat(2, 1fr);
  }
}
</style>