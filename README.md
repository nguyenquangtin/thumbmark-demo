# ThumbmarkJS Demo - Prevent Spam Account Creation

A Vue.js 3 demonstration application that shows how to use ThumbmarkJS browser fingerprinting to prevent spam and multiple account creation from the same browser.

## Features

- **Browser Fingerprinting**: Uses ThumbmarkJS to generate unique browser fingerprints
- **Spam Prevention**: Limits the number of accounts that can be created per browser (default: 2)
- **Visual Feedback**: Shows fingerprint details and account creation statistics
- **JSON Tree View**: Interactive display of complete fingerprint data with all components
- **Persistent Storage**: Stores accounts in localStorage for demo purposes
- **Responsive Design**: Works on desktop and mobile devices

## How Spam Prevention Tracking Works

### 1. Browser Fingerprint Generation
When a user visits the page, ThumbmarkJS generates a unique fingerprint based on:
- **Canvas Fingerprinting**: How the browser renders 2D graphics
- **WebGL Fingerprinting**: 3D graphics rendering capabilities
- **Audio Context**: Audio processing characteristics unique to the device
- **Screen Properties**: Resolution, color depth, and pixel ratio
- **Hardware Info**: CPU cores, memory, and device characteristics
- **Installed Fonts**: System and browser fonts available

### 2. Account Limiting Logic
The spam prevention system works by:
```javascript
// Each account stores the browser fingerprint
const account = {
  username: "user123",
  email: "user@example.com",
  fingerprint: "59169f2d2c9af33c..." // Unique browser ID
}

// Check existing accounts from this fingerprint
const accountsFromSameBrowser = existingAccounts.filter(
  account => account.fingerprint === currentFingerprint
)

// Block if limit reached (default: 2)
if (accountsFromSameBrowser.length >= 2) {
  // Prevent account creation
  return "Maximum accounts reached from this browser"
}
```

### 3. Real-time Tracking Display
The registration form shows:
- **Active Protection Status**: "Spam Prevention Active"
- **Account Limit**: "Maximum 2 accounts per browser fingerprint"
- **Current Usage**: "Accounts from this device: 1/2"
- **Visual Warnings**: Color-coded alerts when approaching limits

### 4. Why It's Effective Against Spam

| Bypass Attempt | Prevention |
|----------------|------------|
| Clear cookies | ✅ Fingerprint remains the same |
| Use incognito/private mode | ✅ Browser characteristics unchanged |
| Change IP/Use VPN | ✅ Hardware fingerprint persists |
| Clear localStorage | ✅ Server-side storage in production |
| Use different email | ✅ Fingerprint still tracked |

### 5. Storage and Persistence
- **Demo Mode**: Uses localStorage for simplicity
- **Production**: Should use server-side database with:
  - Fingerprint hash storage
  - Timestamp tracking
  - IP address logging
  - Account association

### 6. Adjusting Protection Levels
You can customize the spam prevention in `App.vue`:
```javascript
// Change account limit per browser
provide('accountLimit', 2) // Default: 2 accounts

// In production, you might also add:
// - Time-based limits (1 account per hour)
// - Suspicious activity thresholds
// - Whitelist/blacklist management
```

## Getting Started

### Prerequisites

- Node.js 16+ and npm

### Installation

```bash
# Install dependencies
npm install

# Run development server
npm run dev

# Build for production
npm run build

# Preview production build
npm run preview
```

### Development

The application runs on http://localhost:3000 by default.

## Demo Usage

1. **View Your Fingerprint**: The left panel shows your browser's unique fingerprint
2. **Create Accounts**: Use the registration form to create accounts
3. **Observe Spam Prevention**: After creating 2 accounts, you'll be blocked from creating more
4. **View Statistics**: The bottom section shows all created accounts and statistics
5. **Clear Data**: Use the "Clear All" button to reset the demo

## Technology Stack

- **Vue.js 3**: Modern JavaScript framework with Composition API
- **Vite**: Fast build tool and development server
- **ThumbmarkJS**: Browser fingerprinting library
- **CSS3**: Custom styling with gradients and animations

## Project Structure

```
src/
├── App.vue                      # Main application component
├── main.js                      # Application entry point
├── style.css                    # Global styles
└── components/
    ├── FingerprintDisplay.vue   # Shows fingerprint information
    ├── RegistrationForm.vue     # Account creation form with spam prevention
    ├── AccountsList.vue         # Displays created accounts and statistics
    └── FingerprintDetails.vue   # JSON tree view of complete fingerprint data
```

## Configuration

You can adjust the maximum accounts per fingerprint by modifying the `accountLimit` value in `App.vue`:

```javascript
provide('accountLimit', 2) // Change 2 to your desired limit
```

## Security Notes

This is a demonstration application. In production:
- Store fingerprints and account data securely on the server
- Implement additional anti-spam measures (CAPTCHA, rate limiting, email verification)
- Consider privacy regulations when collecting browser fingerprints
- Use HTTPS to prevent fingerprint tampering
- Combine fingerprinting with other security measures

## Production Implementation Best Practices

### Server-Side Integration
```javascript
// Example server-side validation
async function validateAccount(req, res) {
  const { username, email, fingerprint } = req.body

  // Check fingerprint against database
  const existingAccounts = await db.accounts.count({
    where: { fingerprint: fingerprint }
  })

  if (existingAccounts >= MAX_ACCOUNTS_PER_FINGERPRINT) {
    return res.status(429).json({
      error: "Too many accounts from this device"
    })
  }

  // Additional checks: rate limiting, email verification, etc.
}
```

### Privacy Considerations
- **GDPR Compliance**: Requires user consent for fingerprinting
- **Transparency**: Inform users about data collection
- **Data Minimization**: Only collect necessary fingerprint components
- **Right to Deletion**: Allow users to request data removal

## License

MIT