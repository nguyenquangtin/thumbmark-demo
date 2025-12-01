# ThumbmarkJS Demo - Prevent Spam Account Creation

A Vue.js 3 demonstration application that shows how to use ThumbmarkJS browser fingerprinting to prevent spam and multiple account creation from the same browser.

## Features

- **Browser Fingerprinting**: Uses ThumbmarkJS to generate unique browser fingerprints
- **Spam Prevention**: Limits the number of accounts that can be created per browser (default: 2)
- **Visual Feedback**: Shows fingerprint details and account creation statistics
- **Persistent Storage**: Stores accounts in localStorage for demo purposes
- **Responsive Design**: Works on desktop and mobile devices

## How It Works

1. When the page loads, ThumbmarkJS generates a unique fingerprint for the browser
2. The fingerprint is based on various browser characteristics:
   - Canvas fingerprinting
   - WebGL fingerprinting
   - Audio context fingerprinting
   - Screen and hardware information
   - Browser and system fonts

3. When a user tries to create an account:
   - The system checks if the fingerprint has already created the maximum allowed accounts
   - If the limit is reached, account creation is blocked
   - This prevents users from creating multiple spam accounts even if they clear cookies

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
├── App.vue                 # Main application component
├── main.js                # Application entry point
├── style.css              # Global styles
└── components/
    ├── FingerprintDisplay.vue  # Shows fingerprint information
    ├── RegistrationForm.vue    # Account creation form with validation
    └── AccountsList.vue        # Displays created accounts and statistics
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

## License

MIT