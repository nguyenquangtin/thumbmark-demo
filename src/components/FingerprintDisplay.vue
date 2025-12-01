<template>
  <div class="fingerprint-card">
    <h2 class="card-title">Your Browser Fingerprint</h2>

    <div v-if="isLoading" class="loading">
      <div class="loader"></div>
      <p>Generating fingerprint...</p>
    </div>

    <div v-else class="fingerprint-content">
      <div class="fingerprint-id">
        <span class="label">Fingerprint ID:</span>
        <code>{{ displayFingerprint }}</code>
      </div>

      <div class="info-section">
        <h3>What is this?</h3>
        <p>
          This unique identifier is generated based on your browser's characteristics
          including screen resolution, installed plugins, timezone, and other factors.
          It helps us detect when the same browser is creating multiple accounts.
        </p>
      </div>

      <div class="features">
        <h3>Detection Features:</h3>
        <ul>
          <li>Canvas fingerprinting</li>
          <li>WebGL fingerprinting</li>
          <li>Audio context fingerprinting</li>
          <li>Screen & hardware info</li>
          <li>Browser & system fonts</li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue'

const props = defineProps({
  fingerprint: {
    type: String,
    default: null
  },
  isLoading: {
    type: Boolean,
    default: true
  }
})

const displayFingerprint = computed(() => {
  if (!props.fingerprint) return 'Not available'
  return props.fingerprint.substring(0, 16) + '...'
})
</script>

<style scoped>
.fingerprint-card {
  background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
  border-radius: 15px;
  padding: 30px;
  height: 100%;
}

.card-title {
  color: #333;
  margin-bottom: 20px;
  font-size: 1.5rem;
}

.loading {
  text-align: center;
  padding: 40px 0;
}

.loader {
  width: 50px;
  height: 50px;
  border: 3px solid #f3f3f3;
  border-top: 3px solid #667eea;
  border-radius: 50%;
  margin: 0 auto 20px;
  animation: spin 1s linear infinite;
}

@keyframes spin {
  0% { transform: rotate(0deg); }
  100% { transform: rotate(360deg); }
}

.fingerprint-content {
  display: flex;
  flex-direction: column;
  gap: 20px;
}

.fingerprint-id {
  background: white;
  padding: 15px;
  border-radius: 10px;
  display: flex;
  align-items: center;
  gap: 10px;
  word-break: break-all;
}

.fingerprint-id .label {
  font-weight: bold;
  color: #667eea;
}

.fingerprint-id code {
  font-family: 'Courier New', monospace;
  color: #333;
  font-size: 1.1rem;
}

.info-section {
  background: rgba(255, 255, 255, 0.8);
  padding: 15px;
  border-radius: 10px;
}

.info-section h3,
.features h3 {
  color: #667eea;
  margin-bottom: 10px;
  font-size: 1.1rem;
}

.info-section p {
  color: #555;
  line-height: 1.6;
}

.features {
  background: rgba(255, 255, 255, 0.8);
  padding: 15px;
  border-radius: 10px;
}

.features ul {
  list-style: none;
  padding: 0;
}

.features li {
  color: #555;
  padding: 5px 0;
  padding-left: 20px;
  position: relative;
}

.features li:before {
  content: "✓";
  position: absolute;
  left: 0;
  color: #667eea;
  font-weight: bold;
}
</style>