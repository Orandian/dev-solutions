# Complete Expo React Native Solutions Guide

A comprehensive guide to developing, building, and deploying React Native applications using Expo, covering everything from local development to app store submission.

## Table of Contents
1. [Getting Started](#getting-started)
2. [Local Development](#local-development)
3. [Project Health & Diagnostics](#project-health--diagnostics)
4. [EAS CLI Setup](#eas-cli-setup)
5. [Building for Preview](#building-for-preview)
6. [Production Builds](#production-builds)
7. [App Store Submission](#app-store-submission)
8. [Updates & Hot Fixes](#updates--hot-fixes)
9. [Advanced Commands](#advanced-commands)
10. [Troubleshooting](#troubleshooting)

---

## Getting Started

### Prerequisites
```bash
# Install Node.js (LTS version recommended)
node --version
npm --version

# Install Expo CLI globally
npm install -g @expo/cli

# Verify installation
expo --version
```

### Project Initialization
```bash
# Create new Expo project
npx create-expo-app MyApp
cd MyApp

# Initialize existing React Native project with Expo
npx create-expo-app --template blank MyApp
npx create-expo-app --template typescript MyApp

# Install dependencies
npm install
```

---

## Local Development

### Running Your App Locally
```bash
# Start development server
npm start
# or
npx expo start

# Start with specific options
npx expo start --clear        # Clear cache
npx expo start --offline      # Offline mode
npx expo start --localhost    # Force localhost
npx expo start --lan         # Use LAN instead of localhost
npx expo start --dev-client  # Use development client
```

### Network Troubleshooting
```bash
# Use tunnel when local network doesn't work
npx expo start --tunnel

# Force tunnel mode
npx expo start --tunnel --force

# Start with specific host
npx expo start --host tunnel
npx expo start --host lan
npx expo start --host localhost
```

### Testing on Devices
**Android:**
- Open Expo Go app
- Scan QR code from terminal
- Or manually enter the URL shown in terminal

**iOS:**
- Use iPhone camera to scan QR code
- Tap notification to open in Expo Go
- Or use Expo Go app's scanner

---

## Project Health & Diagnostics

### Health Check Commands
```bash
# Check project configuration and dependencies
npx expo-doctor

# Install and run doctor
npx expo install --fix

# Check for common issues
npx expo config

# Validate app.json/app.config.js
npx expo config --type public

# Check dependencies compatibility
npx expo install --check
```

### Dependency Management
```bash
# Install Expo-compatible versions
npx expo install package-name

# Update dependencies to compatible versions
npx expo install --fix

# Check outdated packages
npm outdated

# Update specific package
npx expo install react-native@latest
```

---

## EAS CLI Setup

### Installation and Authentication
```bash
# Install EAS CLI globally
npm install -g eas-cli

# Verify installation
eas --version

# Login to your Expo account
eas login

# Check current user
eas whoami

# Logout
eas logout
```

### Project Configuration
```bash
# Initialize EAS in your project
eas init

# Configure build profiles
eas build:configure

# View current configuration
eas config

# Link project to Expo account
eas project:init
```

---

## Building for Preview

### Android Preview Builds
```bash
# Build Android preview (APK)
eas build --profile preview --platform android

# Build with custom profile
eas build --profile development --platform android

# Build and auto-submit to internal testing
eas build --profile preview --platform android --auto-submit
```

### iOS Preview Builds
```bash
# Build iOS preview (IPA for simulator)
eas build --profile preview --platform ios

# Build for device testing
eas build --profile preview-device --platform ios

# Build iOS simulator build
eas build --profile preview --platform ios --simulator
```

### Installing Preview Builds

**iOS:**
1. Download the `.tar.gz` file from EAS dashboard
2. Extract the folder
3. Drag the `.app` file to iOS Simulator
4. App will install and appear on home screen

**Android:**
1. Copy the download link from EAS dashboard
2. Open link on Android device
3. Download and install the APK file
4. Enable "Install from unknown sources" if prompted

---

## Production Builds

### Building for App Stores
```bash
# Build for both platforms
eas build

# Build for specific platform
eas build --platform android
eas build --platform ios

# Build with specific profile
eas build --profile production

# Build and auto-submit
eas build --platform android --auto-submit
```

### Build Profiles Configuration
```bash
# View build profiles
cat eas.json

# Build with custom message
eas build --message "Release version 1.0.0"

# Build specific version
eas build --profile production --platform all
```

---

## App Store Submission

### Google Play Store Submission
```bash
# Submit to Google Play Store
eas submit --platform android

# Submit with specific build
eas submit --platform android --id [build-id]

# Submit latest build
eas submit --platform android --latest

# Submit with custom track
eas submit --platform android --track internal
```

### Apple App Store Submission
```bash
# Submit to App Store
eas submit --platform ios

# Submit with specific build
eas submit --platform ios --id [build-id]

# Submit latest build
eas submit --platform ios --latest

# Submit to TestFlight only
eas submit --platform ios --apple-team-id [team-id]
```

---

## Updates & Hot Fixes

### EAS Update Commands
```bash
# Create and publish update
eas update

# Update with message
eas update --message "Fix critical login bug"

# Update specific branch/channel
eas update --branch production
eas update --channel production

# Update with automatic message
eas update --auto

# Republish latest update
eas update --republish
```

### Managing Updates
```bash
# View update history
eas update:list

# View update details
eas update:view [update-id]

# Delete update
eas update:delete [update-id]

# Roll back to previous update
eas update:rollback
```

---

## Advanced Commands

### Development and Debugging
```bash
# Generate native code (for custom native code)
eas prebuild

# Prebuild for specific platform
eas prebuild --platform android
eas prebuild --platform ios

# Clean prebuild
eas prebuild --clean

# Run with custom development client
npx expo run:android
npx expo run:ios
```

### Build Management
```bash
# List all builds
eas build:list

# View build details
eas build:view [build-id]

# Cancel running build
eas build:cancel [build-id]

# Download build artifacts
eas build:download [build-id]
```

### Project Management
```bash
# View project information
eas project:info

# Transfer project ownership
eas project:transfer

# View build queue
eas build:queue

# Check account usage
eas account:usage
```

---

## Troubleshooting

### Common Issues and Solutions

#### Local Development Issues
```bash
# Clear Expo cache
npx expo start --clear

# Reset Metro cache
npx expo start --reset-cache

# Fix port conflicts
npx expo start --port 8081

# Debug network issues
npx expo start --tunnel --offline
```

#### Build Issues
```bash
# Check build logs
eas build:list
eas build:view [build-id]

# Rebuild with clean state
eas build --clear-cache

# Check credentials
eas credentials

# Reset credentials
eas credentials --reset
```

#### Update Issues
```bash
# Check update status
eas channel:list
eas branch:list

# Fix update not showing
eas update --force

# Clear update cache on device
# (Restart Expo Go or development client)
```

### Debugging Commands
```bash
# Enable verbose logging
DEBUG=* eas build

# Check system info
npx expo diagnostics

# Validate configuration
npx expo config --type public

# Check environment
npx expo env:info
```

---

## Complete Development Workflow

### Daily Development
```bash
# 1. Start development
npx expo start

# 2. Install new packages
npx expo install package-name

# 3. Check project health
npx expo-doctor

# 4. Test on devices via QR code
```

### Pre-Release Checklist
```bash
# 1. Health check
npx expo-doctor

# 2. Update dependencies
npx expo install --fix

# 3. Build preview
eas build --profile preview --platform all

# 4. Test preview builds
# 5. Create production build
eas build --profile production

# 6. Submit to stores
eas submit --platform all
```

### Hot Fix Workflow
```bash
# 1. Fix the issue in code
# 2. Test locally
npx expo start

# 3. Publish update
eas update --message "Fix critical issue"

# 4. Monitor deployment
eas update:list
```

---

## Configuration Files

### Key Configuration Files
```
├── app.json              # Expo configuration
├── eas.json              # EAS Build configuration
├── package.json          # Dependencies and scripts
├── babel.config.js       # Babel configuration
└── .easignore           # Files to ignore during build
```

### Essential Scripts (package.json)
```json
{
  "scripts": {
    "start": "expo start",
    "android": "expo start --android",
    "ios": "expo start --ios",
    "web": "expo start --web",
    "build": "eas build",
    "build:preview": "eas build --profile preview",
    "submit": "eas submit",
    "update": "eas update"
  }
}
```

---

## Quick Reference

### Most Used Commands
| Command | Description |
|---------|-------------|
| `npx expo start` | Start development server |
| `npx expo start --tunnel` | Start with tunnel (network issues) |
| `npx expo-doctor` | Check project health |
| `eas login` | Login to Expo account |
| `eas build --profile preview` | Build preview version |
| `eas build` | Build for production |
| `eas submit` | Submit to app stores |
| `eas update` | Deploy hot fix update |

### Build Profiles
- **development**: For development with debugging
- **preview**: For internal testing (APK/IPA)
- **production**: For app store submission

### Platforms
- **android**: Google Play Store
- **ios**: Apple App Store
- **all**: Both platforms

Remember to always run `npx expo-doctor` before building to ensure your project is configured correctly!