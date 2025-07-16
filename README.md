# Desktop App

An Electron-based desktop application.

## Development

### Prerequisites
- Node.js 18 or higher
- npm

### Setup
```bash
npm install
```

### Running the app
```bash
npm start
```

### Building locally
```bash
# Build for current platform
npm run build

# Build for specific platforms
npm run build:mac
npm run build:windows
npm run build:linux

# Build for all platforms
npm run build:all
```

## Releases

This project uses GitHub Actions to automatically build and release the desktop app for multiple platforms.

### How to create a release

1. **Tag your release**: Create and push a version tag
   ```bash
   git tag v1.0.0
   git push origin v1.0.0
   ```

2. **Automatic build**: GitHub Actions will automatically:
   - Build the app for Windows, macOS, and Linux
   - Package the executables
   - Create a GitHub release with downloadable files

3. **Manual trigger**: You can also manually trigger the build workflow from the GitHub Actions tab

### Download and Installation

After a release is created, users can download the appropriate file for their platform:

- **Windows**: Download and run the `.exe` installer
- **macOS**: Download the `.dmg` file, open it, and drag the app to Applications
- **Linux**: Download the `.AppImage` file, make it executable, and run it

### Platform-specific notes

#### macOS
- The app is built as a universal binary (supports both Intel and Apple Silicon Macs)
- Users may need to allow the app in System Preferences > Security & Privacy if downloaded from GitHub

#### Linux
- The AppImage format works on most Linux distributions
- Make the file executable: `chmod +x *.AppImage`
- Then run it directly: `./desktop-app-1.0.0.AppImage`

#### Windows
- The installer includes automatic updates support
- Users can choose installation directory during setup
