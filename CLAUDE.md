# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

ThumbmarkJS Demo - A Vue.js 3 application demonstrating browser fingerprinting for spam prevention using ThumbmarkJS library.

## Commands

```bash
# Install dependencies
npm install

# Run development server (http://localhost:3000)
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview
```

## Architecture

### Technology Stack
- **Vue.js 3** with Composition API
- **Vite** for build tooling
- **ThumbmarkJS** for browser fingerprinting
- **@thumbmarkjs/vue** for Vue integration

### Component Structure
- `App.vue`: Main application container, manages fingerprint generation and account state
- `components/FingerprintDisplay.vue`: Shows browser fingerprint information
- `components/RegistrationForm.vue`: Handles account creation with spam prevention logic
- `components/AccountsList.vue`: Displays registered accounts and statistics

### Key Features Implementation
- **Fingerprint Generation**: Uses `getThumbmark()` from ThumbmarkJS on app mount
- **Spam Prevention**: Validates account creation against fingerprint-based limits (default: 2 accounts per browser)
- **Data Persistence**: Uses localStorage for demo purposes (key: 'demo_accounts')
- **Account Limit**: Configurable via Vue's provide/inject (currently set to 2)