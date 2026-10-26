# 🚀 NOCTALIÆ DEPLOYMENT GUIDE

This guide covers all aspects of deploying Noctaliæ, from local development to production on the Google Play Store.

---

## 📋 TABLE OF CONTENTS

1. [Prerequisites](#prerequisites)
2. [Local Environment](#local-environment)
3. [Backend (Infomaniak)](#backend-infomaniak)
4. [Android Build](#android-build)
5. [Google Play Store](#google-play-store)
6. [Monitoring](#monitoring)
7. [Troubleshooting](#troubleshooting)

---

## 🔧 PREREQUISITES

### Required tools

```bash
# Node.js 20+
node --version

# NPM
npm --version

# Expo CLI
npm install -g expo-cli

# EAS CLI (for builds)
npm install -g eas-cli

# Git
git --version
```

### Required accounts

- ✅ **Expo** : https://expo.dev (free)
- ✅ **Google Play Console** : https://play.google.com/console ($25 one-time)
- ⚠️ **Sentry** : https://sentry.io (optional, 5K errors/month free)
- ⚠️ **Amplitude** : https://amplitude.com (optional, 10M events/month free)
- ✅ **Infomaniak** : Backend hosting (already active)

---

## 💻 LOCAL ENVIRONMENT

### 1. Clone & Installation

```bash
# Clone the repo
git clone https://github.com/tm-ai0/noctaliae-mobile.git
cd noctaliae-mobile

# Install dependencies
npm install

# Create the .env file
cp .env.example .env
```

### 2. .env Configuration

```env
# Backend API
API_BASE_URL=https://api.thomasmaury.fr

# Monitoring (optional in development)
SENTRY_DSN=
AMPLITUDE_API_KEY=

# Premium (coming soon)
STRIPE_PUBLISHABLE_KEY=
```

### 3. Run the app in development

```bash
# Start the Metro bundler
npm start

# Run on Android
npm run android

# Run on Android with the cache cleared
npm run start:clear
```

### 4. Test on a real device

#### Option A : Expo Go (fast, limited)

```bash
npm start
# Scan the QR code with the Expo Go app
```

⚠️ **Limitation** : Does not work with `expo-av` (audio recording)

#### Option B : Development Build (recommended)

```bash
# Create a development build
eas build --platform android --profile development

# Install the APK on your phone
# Then run:
npm start --dev-client
```

---

## 🌐 BACKEND (INFOMANIAK)

### Current status

- ✅ Backend v2.2 deployed at `https://api.thomasmaury.fr`
- ✅ Working endpoints (transcription, analysis, chat)
- ✅ Rate limiting enabled (100 req/h per IP)
- ✅ All API keys configured (Claude, Groq, OpenAI)

### Backend files

```
BACKEND_V2.2_INFOMANIAK/
├── index.js           # Main file
├── .env              # Environment variables (on Infomaniak)
└── package.json      # Dependencies
```

### Update the backend

#### Via FileZilla (current method)

1. **Connect to Infomaniak FTP**
   - Host: `ftp.infomaniak.com`
   - Username: your Infomaniak username
   - Password: your Infomaniak password

2. **Navigate to**
   ```
   ~/sites/api.thomasmaury.fr/
   ```

3. **Back up the old file**
   ```
   Rename: index.js → index.js.backup-YYYYMMDD
   ```

4. **Upload the new one**
   ```
   Upload: BACKEND_V2.2_INFOMANIAK/index.js
   ```

5. **Restart the app**
   - Go to the Infomaniak control panel
   - "Node.js" section
   - Click "Restart application"

6. **Verify**
   ```bash
   curl https://api.thomasmaury.fr/health
   # Should return: {"status":"ok","version":"2.2"}
   ```

#### Backend environment variables

The variables are configured directly on Infomaniak (no .env file uploaded) :

```env
# API Keys (already configured on Infomaniak)
ANTHROPIC_API_KEY=sk-ant-xxxxx
GROQ_API_KEY=gsk_xxxxx
OPENAI_API_KEY=sk-xxxxx

# App config
PORT=3000
NODE_ENV=production
```

---

## 📱 ANDROID BUILD

### Build types

1. **Development** : For testing with hot reload
2. **Preview** : APK to share (beta testers)
3. **Production** : AAB for the Google Play Store

### Preview Build (APK)

```bash
# First build (configure EAS)
eas build:configure

# Log in to Expo
eas login

# Build APK
eas build --platform android --profile preview

# The build takes 10-15 min
# You receive a link to download the APK
```

### Production Build (AAB)

```bash
# Build AAB for the Play Store
eas build --platform android --profile production

# The build generates an .aab
# Download it to upload it to the Play Store
```

### Configure the keystore (first time)

EAS manages the keystore automatically. If you want to reuse it :

```bash
# Download the keystore
eas credentials

# Save it somewhere safe
# You will need it for updates
```

---

## 🏪 GOOGLE PLAY STORE

### First release (not done yet)

#### 1. Create the app on Play Console

1. Go to https://play.google.com/console
2. Click "Create app"
3. Fill in the details :
   - **Name** : Noctaliæ
   - **Default language** : French
   - **Type** : App
   - **Free/Paid** : Free

#### 2. Complete the listing

**Store Listing**
- Title (30 chars max) : `Noctaliæ - Dream Analysis`
- Short description (80 chars) : `Analyze your dreams with AI and neuroscience`
- Full description (4000 chars max) :

```
Noctaliæ helps you understand your dreams through artificial intelligence and neuroscience.

🧠 SCIENTIFIC ANALYSIS
Based on the work of Isabelle Arnulf (Paris Brain Institute), our analyses use the latest advances in dream neuroscience.

🎙️ VOICE RECORDING
Record your dreams when you wake up, while they are still fresh in your memory.

🤖 ADVANCED AI
Two AI models to analyze your dreams :
- Claude Sonnet 4.5 (in-depth analysis)
- Llama 3.3 70B (quick analysis)

💬 IN-DEPTH CONVERSATION
Ask questions about your dreams and explore their meaning in depth.

🎨 ELEGANT INTERFACE
Modern, night-inspired design with several customizable themes.

🔒 PRIVACY
Your dreams stay on your device. No cloud synchronization by default.
```

**Required screenshots**
- At least 2 phone screenshots (1080x1920 or 1080x2340)
- 1 high-resolution app icon (512x512)
- 1 optional banner (1024x500)

#### 3. Categorization

- **Category** : Health & Fitness
- **Tags** : Dreams, AI, Neuroscience, Personal journal
- **Target audience** : 13+
- **Content** :
  - ❌ Ads : No
  - ❌ In-app purchases : No (for now)
  - ✅ Data collection : Yes (optional analytics)

#### 4. Compliance

**Privacy policy**
- You must create a privacy policy page
- Host it at https://thomasmaury.fr/noctaliae/privacy
- Add the URL in Play Console

**Example of minimal content** :

```markdown
# Privacy Policy - Noctaliæ

Last updated : [Date]

## Data collected

- **Audio recordings** : Stored locally on your device
- **Transcriptions** : Sent to our server for analysis, then deleted
- **AI analyses** : Stored locally on your device

## Data shared

We only send your data to the following services to operate the app :
- OpenAI (Whisper transcription)
- Anthropic (Claude analysis)
- Groq (Llama analysis)

## Your rights

You can delete all your data at any time from within the app.

## Contact

contact@thomasmaury.fr
```

#### 5. Upload the AAB

```bash
# Production build
eas build --platform android --profile production

# Once the build is complete, download the .aab

# Upload via Play Console
# Production > Releases > Create new release
# Upload the AAB
```

#### 6. Internal testing

Before the public release, conduct internal testing :

1. Create a list of testers (you + a few friends)
2. Upload the AAB to "Internal Testing"
3. Test for a few days
4. Fix bugs
5. Move to "Open Testing" (public beta)
6. Finally, move to "Production"

---

## 📊 MONITORING

### Sentry (Crash Reporting)

#### Setup

```bash
# Install Sentry
npm install @sentry/react-native

# Configure
npx @sentry/wizard -i reactNative -p android
```

#### Get the DSN

1. Go to https://sentry.io
2. Create a "Noctaliæ" project
3. Copy the DSN : `https://xxxxx@xxxxx.ingest.sentry.io/xxxxx`
4. Add it to `.env` :
   ```env
   SENTRY_DSN=https://xxxxx@xxxxx.ingest.sentry.io/xxxxx
   ```

#### Test

```typescript
// Force an error to test
import { captureError } from '@/config/sentry.config';

throw new Error('Test Sentry');
```

### Amplitude (Analytics)

#### Setup

```bash
# Install Amplitude
npm install @amplitude/analytics-react-native
```

#### Get the API Key

1. Go to https://amplitude.com
2. Create a "Noctaliæ" project
3. Copy the API Key
4. Add it to `.env` :
   ```env
   AMPLITUDE_API_KEY=xxxxx
   ```

#### Track events

```typescript
import { Analytics } from '@/config/analytics.config';

Analytics.recordingStarted();
Analytics.analysisCompleted('claude-sonnet-4', 5.2, 'deep');
```

---

## 🐛 TROUBLESHOOTING

### Problem 1 : "Unable to resolve module"

```bash
# Clear cache
npm start -- --clear

# Or
npx expo start -c

# Or
rm -rf node_modules
npm install
```

### Problem 2 : EAS build fails

```bash
# Check the logs
eas build:list

# Check the config
eas build:configure

# Clean and rebuild
eas build --platform android --profile preview --clear-cache
```

### Problem 3 : Backend is not responding

```bash
# Check the health check
curl https://api.thomasmaury.fr/health

# If error 502/503 → Restart the app on Infomaniak

# If error 429 → Rate limit exceeded, wait 1h
```

### Problem 4 : Audio is not recording

```bash
# Check the permissions in app.json
{
  "android": {
    "permissions": [
      "RECORD_AUDIO",
      "WRITE_EXTERNAL_STORAGE"
    ]
  }
}

# Rebuild the app
eas build --platform android --profile development
```

### Problem 5 : TypeScript errors

```bash
# Check types
npx tsc --noEmit

# If there are too many errors, migrate gradually :
# Just rename the files .js → .ts
# Add // @ts-nocheck at the top
# Fix gradually
```

---

## ✅ DEPLOYMENT CHECKLIST

### Before each release

- [ ] Tests pass : `npm test`
- [ ] TypeScript OK : `npx tsc --noEmit`
- [ ] Local build works : `npm run android`
- [ ] Backend health check OK : `curl https://api.thomasmaury.fr/health`
- [ ] Version incremented in `app.json`
- [ ] Changelog updated
- [ ] Screenshots up to date (if UI changes)

### Preview Build (Beta)

- [ ] `eas build --platform android --profile preview`
- [ ] Download the APK
- [ ] Test on multiple devices
- [ ] Share with beta testers
- [ ] Collect feedback

### Production Build (Play Store)

- [ ] All beta bugs fixed
- [ ] `eas build --platform android --profile production`
- [ ] Upload AAB to Play Console
- [ ] Fill in release notes
- [ ] Submit for Google review (1-3 days)
- [ ] Once approved, roll out gradually (10% → 50% → 100%)

---

## 📞 SUPPORT

If you have questions :

- **Email** : contact@thomasmaury.fr
- **GitHub Issues** : https://github.com/tm-ai0/noctaliae-mobile/issues
- **Documentation** : See `ARCHITECTURE.md` and `TESTING.md`

---

**Last updated : 21/11/2025**
