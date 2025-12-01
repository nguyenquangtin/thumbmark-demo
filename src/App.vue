<template>
  <div class="container">
    <h1 class="title">ThumbmarkJS Demo</h1>
    <p class="subtitle">Prevent Spam Account Creation with Browser Fingerprinting</p>

    <div class="content-grid">
      <FingerprintDisplay
        :fingerprint="fingerprint"
        :isLoading="isLoadingFingerprint"
      />

      <RegistrationForm
        :fingerprint="fingerprint"
        :existingAccounts="existingAccounts"
        @account-created="handleAccountCreated"
      />
    </div>

    <AccountsList
      :accounts="existingAccounts"
      @clear-accounts="clearAccounts"
    />
  </div>
</template>

<script setup>
import { ref, onMounted, provide } from 'vue'
import { getThumbmark } from '@thumbmarkjs/thumbmarkjs'
import FingerprintDisplay from './components/FingerprintDisplay.vue'
import RegistrationForm from './components/RegistrationForm.vue'
import AccountsList from './components/AccountsList.vue'

const fingerprint = ref(null)
const isLoadingFingerprint = ref(true)
const existingAccounts = ref([])

const loadStoredAccounts = () => {
  const stored = localStorage.getItem('demo_accounts')
  if (stored) {
    existingAccounts.value = JSON.parse(stored)
  }
}

const saveAccounts = () => {
  localStorage.setItem('demo_accounts', JSON.stringify(existingAccounts.value))
}

const handleAccountCreated = (account) => {
  existingAccounts.value.push({
    ...account,
    createdAt: new Date().toISOString()
  })
  saveAccounts()
}

const clearAccounts = () => {
  existingAccounts.value = []
  localStorage.removeItem('demo_accounts')
}

onMounted(async () => {
  loadStoredAccounts()

  try {
    console.log('Starting fingerprint generation...')

    // Add timeout to prevent infinite loading
    const thumbmarkPromise = getThumbmark()
    const timeoutPromise = new Promise((_, reject) =>
      setTimeout(() => reject(new Error('Fingerprint generation timeout')), 5000)
    )

    const result = await Promise.race([thumbmarkPromise, timeoutPromise])
    console.log('Fingerprint generated:', result)
    // Extract the thumbmark string from the result object
    fingerprint.value = result.thumbmark || result
  } catch (error) {
    console.error('Failed to get fingerprint:', error)
    // Generate a fallback fingerprint
    fingerprint.value = 'fallback-' + Math.random().toString(36).substring(7)
  } finally {
    isLoadingFingerprint.value = false
  }
})

provide('accountLimit', 2)
</script>

<style scoped>
.container {
  background: white;
  border-radius: 20px;
  padding: 40px;
  box-shadow: 0 20px 60px rgba(0, 0, 0, 0.3);
}

.title {
  text-align: center;
  color: #333;
  font-size: 2.5rem;
  margin-bottom: 10px;
  background: linear-gradient(135deg, #667eea, #764ba2);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
  background-clip: text;
}

.subtitle {
  text-align: center;
  color: #666;
  margin-bottom: 40px;
  font-size: 1.1rem;
}

.content-grid {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 30px;
  margin-bottom: 40px;
}

@media (max-width: 768px) {
  .content-grid {
    grid-template-columns: 1fr;
  }

  .container {
    padding: 20px;
  }

  .title {
    font-size: 2rem;
  }
}
</style>