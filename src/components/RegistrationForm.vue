<template>
  <div class="registration-card">
    <h2 class="card-title">Create New Account</h2>

    <form @submit.prevent="handleSubmit">
      <div class="form-group">
        <label for="username">Username</label>
        <input
          id="username"
          v-model="formData.username"
          type="text"
          placeholder="Enter username"
          required
        />
      </div>

      <div class="form-group">
        <label for="email">Email</label>
        <input
          id="email"
          v-model="formData.email"
          type="email"
          placeholder="Enter email"
          required
        />
      </div>

      <div class="form-group">
        <label for="password">Password</label>
        <input
          id="password"
          v-model="formData.password"
          type="password"
          placeholder="Enter password"
          required
        />
      </div>

      <div v-if="error" class="error-message">
        {{ error }}
      </div>

      <div v-if="success" class="success-message">
        {{ success }}
      </div>

      <div v-if="warning" class="warning-message">
        {{ warning }}
      </div>

      <button type="submit" class="submit-btn">
        Create Account
      </button>
    </form>

    <div class="info-box">
      <p><strong>Spam Prevention Active</strong></p>
      <p>Maximum {{ accountLimit }} accounts per browser fingerprint</p>
      <p v-if="accountsFromThisDevice > 0" class="device-count">
        Accounts from this device: {{ accountsFromThisDevice }}/{{ accountLimit }}
      </p>
    </div>
  </div>
</template>

<script setup>
import { ref, computed, inject } from 'vue'

const props = defineProps({
  fingerprint: {
    type: String,
    default: null
  },
  existingAccounts: {
    type: Array,
    default: () => []
  }
})

const emit = defineEmits(['account-created'])

const accountLimit = inject('accountLimit', 2)

const formData = ref({
  username: '',
  email: '',
  password: ''
})

const error = ref('')
const success = ref('')
const warning = ref('')

const accountsFromThisDevice = computed(() => {
  if (!props.fingerprint) return 0
  return props.existingAccounts.filter(account =>
    account.fingerprint === props.fingerprint
  ).length
})

const clearMessages = () => {
  error.value = ''
  success.value = ''
  warning.value = ''
}

const resetForm = () => {
  formData.value = {
    username: '',
    email: '',
    password: ''
  }
}

const handleSubmit = () => {
  clearMessages()

  if (!props.fingerprint) {
    error.value = 'Unable to verify browser fingerprint. Please refresh the page.'
    return
  }

  const existingWithSameFingerprint = props.existingAccounts.filter(
    account => account.fingerprint === props.fingerprint
  )

  if (existingWithSameFingerprint.length >= accountLimit) {
    error.value = `⚠️ Spam Prevention: Maximum ${accountLimit} accounts already created from this browser. You cannot create more accounts.`
    return
  }

  const existingUsername = props.existingAccounts.find(
    account => account.username.toLowerCase() === formData.value.username.toLowerCase()
  )

  if (existingUsername) {
    error.value = 'Username already taken. Please choose another.'
    return
  }

  const existingEmail = props.existingAccounts.find(
    account => account.email.toLowerCase() === formData.value.email.toLowerCase()
  )

  if (existingEmail) {
    error.value = 'Email already registered. Please use another email.'
    return
  }

  emit('account-created', {
    ...formData.value,
    fingerprint: props.fingerprint
  })

  success.value = '✓ Account created successfully!'

  if (existingWithSameFingerprint.length === accountLimit - 1) {
    warning.value = `Warning: You've reached the maximum number of accounts (${accountLimit}) for this browser.`
  }

  resetForm()

  setTimeout(() => {
    clearMessages()
  }, 5000)
}
</script>

<style scoped>
.registration-card {
  background: white;
  border-radius: 15px;
  padding: 30px;
  box-shadow: 0 10px 30px rgba(0, 0, 0, 0.1);
}

.card-title {
  color: #333;
  margin-bottom: 25px;
  font-size: 1.5rem;
}

.form-group {
  margin-bottom: 20px;
}

.form-group label {
  display: block;
  margin-bottom: 5px;
  color: #555;
  font-weight: 500;
}

.form-group input {
  width: 100%;
  padding: 12px 15px;
  border: 2px solid #e0e0e0;
  border-radius: 8px;
  font-size: 1rem;
  transition: all 0.3s;
}

.form-group input:focus {
  outline: none;
  border-color: #667eea;
  box-shadow: 0 0 0 3px rgba(102, 126, 234, 0.1);
}

.error-message {
  background: #ffebee;
  color: #c62828;
  padding: 12px;
  border-radius: 8px;
  margin-bottom: 20px;
  border-left: 4px solid #c62828;
}

.success-message {
  background: #e8f5e9;
  color: #2e7d32;
  padding: 12px;
  border-radius: 8px;
  margin-bottom: 20px;
  border-left: 4px solid #2e7d32;
}

.warning-message {
  background: #fff3e0;
  color: #e65100;
  padding: 12px;
  border-radius: 8px;
  margin-bottom: 20px;
  border-left: 4px solid #e65100;
}

.submit-btn {
  width: 100%;
  padding: 14px;
  background: linear-gradient(135deg, #667eea, #764ba2);
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 1.1rem;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s;
}

.submit-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 10px 20px rgba(102, 126, 234, 0.3);
}

.submit-btn:active {
  transform: translateY(0);
}

.info-box {
  margin-top: 25px;
  padding: 15px;
  background: #f5f7fa;
  border-radius: 8px;
  border-left: 4px solid #667eea;
}

.info-box p {
  margin: 5px 0;
  color: #555;
  font-size: 0.95rem;
}

.info-box p strong {
  color: #667eea;
}

.device-count {
  margin-top: 10px;
  font-weight: 600;
  color: #764ba2 !important;
}
</style>