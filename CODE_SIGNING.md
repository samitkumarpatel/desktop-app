# Code Signing Setup Guide

This guide explains how to set up code signing for your Electron desktop app to avoid security warnings from operating systems.

## Overview

Code signing is essential for desktop applications to:
- Avoid "Unknown Publisher" warnings on Windows
- Prevent Gatekeeper warnings on macOS 
- Ensure users trust your application
- Enable automatic updates

## Platform-Specific Setup

### 🍎 macOS Code Signing

#### Requirements:
1. **Apple Developer Account** ($99/year)
2. **Developer ID Application Certificate**
3. **App-specific password** for notarization

#### Steps:

1. **Get your certificates from Keychain Access:**
   ```bash
   # Export your Developer ID Application certificate as .p12
   # Include private key, set a password
   ```

2. **Convert certificate to base64:**
   ```bash
   base64 -i certificate.p12 | pbcopy
   ```

3. **Add GitHub Secrets:**
   - `CSC_LINK`: Base64 encoded .p12 certificate
   - `CSC_KEY_PASSWORD`: Certificate password
   - `APPLE_ID`: Your Apple ID email
   - `APPLE_ID_PASS`: App-specific password
   - `APPLE_TEAM_ID`: Your Team ID (found in Apple Developer portal)

4. **Update package.json with your Team ID:**
   ```json
   "mac": {
     "notarize": {
       "teamId": "YOUR_ACTUAL_TEAM_ID"
     }
   }
   ```

### 🪟 Windows Code Signing

#### Requirements:
1. **Code Signing Certificate** (from CA like DigiCert, Sectigo, etc.)
2. **Certificate in .p12 format**

#### Steps:

1. **Convert certificate to base64:**
   ```bash
   base64 -i certificate.p12 | pbcopy
   ```

2. **Add GitHub Secrets:**
   - `WIN_CSC_LINK`: Base64 encoded .p12 certificate
   - `WIN_CSC_KEY_PASSWORD`: Certificate password

3. **Update package.json with certificate path:**
   ```json
   "win": {
     "certificateFile": "certificate.p12",
     "certificatePassword": "env:WIN_CSC_KEY_PASSWORD"
   }
   ```

### 🐧 Linux

Linux doesn't require code signing, but AppImage format provides good compatibility.

## Setting up GitHub Secrets

1. Go to your repository on GitHub
2. Navigate to **Settings** → **Secrets and variables** → **Actions**
3. Click **New repository secret**
4. Add each secret with the exact names mentioned above

## Testing Code Signing

### Local Testing (macOS):
```bash
# Set environment variables
export CSC_LINK="path/to/certificate.p12"
export CSC_KEY_PASSWORD="your_password"
export APPLE_ID="your@apple.id"
export APPLE_ID_PASS="app-specific-password"
export APPLE_TEAM_ID="your_team_id"

# Build and sign
npm run build:mac
```

### Local Testing (Windows):
```bash
# Set environment variables
export WIN_CSC_LINK="path/to/certificate.p12"
export WIN_CSC_KEY_PASSWORD="your_password"

# Build and sign
npm run build:windows
```

## Verification

### macOS:
```bash
# Check code signature
codesign -dv --verbose=4 "dist/Desktop App.app"

# Check notarization
spctl -a -t exec -vv "dist/Desktop App.app"
```

### Windows:
```bash
# Check signature (Windows)
signtool verify /pa "dist/Desktop App Setup.exe"
```

## Common Issues

### macOS:
- **"Developer ID Application" not found**: Ensure certificate is properly installed
- **Notarization fails**: Check Apple ID credentials and app-specific password
- **Hardened Runtime errors**: Verify entitlements.mac.plist is correct

### Windows:
- **Certificate not trusted**: Ensure timestamp server is accessible
- **SmartScreen warnings**: Use EV certificate or build reputation over time

## Cost Considerations

| Platform | Certificate Type | Annual Cost | Notes |
|----------|------------------|-------------|-------|
| macOS | Apple Developer ID | $99 | Required for distribution |
| Windows | Standard Code Signing | $200-400 | Basic certificate |
| Windows | EV Code Signing | $400-600 | Immediate SmartScreen reputation |

## Alternative: Self-Signed (Development Only)

For development/testing, you can create self-signed certificates, but users will still see warnings:

### macOS Self-Signed:
```bash
# Create self-signed certificate
security create-keychain -p test build.keychain
security default-keychain -s build.keychain
```

### Windows Self-Signed:
```bash
# Create self-signed certificate
New-SelfSignedCertificate -DnsName "YourApp" -Type CodeSigning -CertStoreLocation Cert:\CurrentUser\My
```

⚠️ **Warning**: Self-signed certificates will still show security warnings to users.

## Next Steps

1. Obtain proper certificates for your target platforms
2. Set up GitHub secrets with your certificate information
3. Update package.json with your specific details (Team ID, etc.)
4. Test the build process
5. Create a release to verify code signing works

Remember: Code signing is an investment in user trust and professional app distribution!
