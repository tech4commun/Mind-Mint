# Building and Running Mind Mint

## ✅ Safety Check - Is This Safe to Run?

**YES, this project is completely safe!** Here's why:

✓ **Standard Gradle Wrapper**: The `gradlew` and `gradlew.bat` files are official Gradle wrapper scripts  
✓ **Legitimate Dependencies**: All libraries come from trusted sources (Google, GitHub)  
✓ **No Suspicious Code**: No malicious scripts, crypto miners, or data theft  
✓ **Open Source**: Publicly auditable code on GitHub  
✓ **Standard Android App**: Uses normal Android development patterns  

You can safely run `gradlew` commands in your terminal!

---

## 📋 Prerequisites

Before building the app, ensure you have:

1. **Java Development Kit (JDK)**
   - JDK 17 or higher recommended
   - Check: `java -version`
   - Download: https://adoptium.net/

2. **Android Studio** (Recommended)
   - Download: https://developer.android.com/studio
   - Includes Android SDK automatically

3. **Android SDK** (if not using Android Studio)
   - SDK Platform 35 (Android 15)
   - Build Tools 35.0.0
   - SDK Platform-Tools

4. **Git** (for version control)
   - Download: https://git-scm.com/

---

## 🔧 Setup Instructions

### Option 1: Using Android Studio (Easiest)

1. **Open the Project**
   ```
   File → Open → Select Mind-Mint folder
   ```

2. **Sync Gradle**
   - Android Studio will automatically sync Gradle
   - Wait for dependencies to download

3. **Connect Android Device or Emulator**
   - **Physical Device**: Enable Developer Options & USB Debugging
   - **Emulator**: Create an AVD in Device Manager (Android 9.0+)

4. **Run the App**
   - Click the green "Run" button (▶️)
   - Or press `Shift + F10`

### Option 2: Using Command Line

1. **Navigate to Project**
   ```powershell
   cd d:\Mind-Mint
   ```

2. **Make Gradle Wrapper Executable** (First time only)
   - On Windows: Already executable
   - On Linux/Mac: `chmod +x gradlew`

3. **Build the Project**
   ```powershell
   # Windows
   .\gradlew build
   
   # Linux/Mac
   ./gradlew build
   ```

4. **Install on Connected Device**
   ```powershell
   # Windows
   .\gradlew installDebug
   
   # Linux/Mac
   ./gradlew installDebug
   ```

---

## 🎯 Common Gradle Commands

| Command | Description |
|---------|-------------|
| `.\gradlew tasks` | List all available tasks |
| `.\gradlew build` | Compile and build the APK |
| `.\gradlew clean` | Delete build folder |
| `.\gradlew assembleDebug` | Build debug APK only |
| `.\gradlew assembleRelease` | Build release APK (requires signing) |
| `.\gradlew installDebug` | Build & install debug APK |
| `.\gradlew uninstallAll` | Uninstall app from device |
| `.\gradlew lint` | Run code quality checks |
| `.\gradlew dependencies` | Show all dependencies |

---

## 📱 Running on Device vs Emulator

### Physical Android Device

**Advantages:**
- Real-world performance
- Test actual hardware features
- Faster than emulator

**Setup:**
1. Enable Developer Options:
   - Settings → About Phone → Tap "Build Number" 7 times
2. Enable USB Debugging:
   - Settings → Developer Options → USB Debugging
3. Connect device via USB
4. Allow USB debugging when prompted

### Android Emulator

**Advantages:**
- No physical device needed
- Test multiple Android versions
- Easier app data inspection

**Setup in Android Studio:**
1. Tools → Device Manager
2. Create Device → Select device (e.g., Pixel 7)
3. Download system image (API 28 or higher)
4. Launch emulator

---

## 🔨 Build Variants

The app has two build variants:

### Debug Build
- Faster build times
- Includes debugging information
- Uses debug keystore (auto-generated)
- **Location**: `app/build/outputs/apk/debug/app-debug.apk`

### Release Build
- Optimized & minified code
- Requires signing configuration
- **NOT configured by default** (needs keystore)

To create a release build, you need to:
1. Create a keystore file
2. Add signing config to `gradle.properties`
3. Run: `.\gradlew assembleRelease`

---

## 🐛 Troubleshooting

### "SDK location not found"
**Solution:** Create `local.properties` in project root:
```properties
sdk.dir=C\:\\Users\\YourName\\AppData\\Local\\Android\\Sdk
```

### "Could not resolve dependencies"
**Solution:**
```powershell
.\gradlew clean
.\gradlew build --refresh-dependencies
```

### "Unsupported class file major version"
**Solution:** Update JDK to version 17 or higher

### "AAPT2 error"
**Solution:** Update Android SDK Build Tools:
```powershell
# In Android Studio: SDK Manager → SDK Tools → Android SDK Build-Tools
```

### "Execution failed for task ':app:processDebugResources'"
**Solution:** Check that all resource files (XML) are valid and no duplicates exist

---

## 📂 Build Output Locations

After building, find the APK files here:

```
Mind-Mint/
└── app/
    └── build/
        └── outputs/
            └── apk/
                ├── debug/
                │   └── app-debug.apk          # Debug build
                └── release/
                    └── app-release.apk        # Release build (if configured)
```

---

## 🔐 Signing Configuration (Optional)

For release builds, create `gradle.properties` in the project root:

```properties
KEYSTORE_FILE=/path/to/your/keystore.jks
KEYSTORE_PASSWORD=your_keystore_password
KEY_ALIAS=your_key_alias
KEY_PASSWORD=your_key_password
```

**Note:** Never commit `gradle.properties` to version control (it's in `.gitignore`)!

---

## 🧪 Testing

### Run Unit Tests
```powershell
.\gradlew test
```

### Run Instrumented Tests (on device/emulator)
```powershell
.\gradlew connectedAndroidTest
```

---

## 📊 Project Statistics

Check project size and dependencies:
```powershell
# Show dependency tree
.\gradlew app:dependencies

# Analyze APK size
.\gradlew assembleDebug
# Then use Android Studio: Build → Analyze APK
```

---

## 🚀 Next Steps

After successfully building:
1. Read `ARCHITECTURE.md` to understand code structure
2. Read `KEY_COMPONENTS.md` to understand main classes
3. Explore `app/src/main/java/` to see the source code
4. Check `AndroidManifest.xml` to see app configuration

---

## ❓ Need Help?

- **Android Studio Issues**: https://developer.android.com/studio/troubleshoot
- **Gradle Issues**: https://docs.gradle.org/current/userguide/troubleshooting.html
- **Project Issues**: Check GitHub Issues on the original repository
