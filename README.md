# Mind Mint: Reclaim Your Focus

![GitHub stars](https://img.shields.io/github/stars/tech4commun/mind-mint?style=social) ![GitHub forks](https://img.shields.io/github/forks/tech4commun/mind-mint?style=social) ![GitHub last commit](https://img.shields.io/github/last-commit/tech4commun/mind-mint) ![License](https://img.shields.io/badge/license-Custom-brightgreen)

**Mind Mint** is an Android application designed to help you stop doomscrolling and boost your productivity. Take control of your digital life and focus on what truly matters.

## ✨ Features

*   **App Blocker:** Block distracting apps like Instagram Reels, YouTube Shorts, and Snapchat Highlights
*   **Focus Mode:** Dedicated focus timer to help you concentrate on your tasks
*   **In-App Currency:** Earn rewards as you stay focused and productive
*   **Task Manager:** Keep track of your to-do list with reminders
*   **Habit Tracker:** Build healthy habits and track your progress with detailed stats
*   **Customizable Reminders:** Set reminders to take a break from doomscrolling
*   **Time Limits:** Set daily time limits for distracting apps

## 🚀 Quick Start

### Prerequisites
- Android Studio or JDK 17+
- Android SDK (API 28+)
- An Android device or emulator

### Building the App

```bash
# Clone the repository
git clone https://github.com/tech4commun/mind-mint.git
cd mind-mint

# Create local.properties with your Android SDK path
echo "sdk.dir=YOUR_SDK_PATH" > local.properties

# Build the app
./gradlew assembleDebug

# Install on connected device
./gradlew installDebug
```

### Configuration Files

The project needs two configuration files that are not in version control:

1. **`local.properties`** - Contains Android SDK path:
   ```properties
   sdk.dir=C\:\\Users\\YourName\\AppData\\Local\\Android\\Sdk
   ```

2. **`gradle.properties`** (optional, example provided):
   ```properties
   android.useAndroidX=true
   org.gradle.jvmargs=-Xmx2048m
   ```

3. **`app/google-services.json`** (optional) - For Firebase features. A dummy file is included for development.

## 📱 Tech Stack

*   **Language:** Java
*   **UI:** XML with Material Design
*   **Build System:** Gradle
*   **Min SDK:** API 28 (Android 9.0)
*   **Target SDK:** API 35 (Android 15)

### Key Libraries
- AndroidX (Core, AppCompat, ConstraintLayout)
- Material Design Components
- Lottie (Animations)
- MPAndroidChart (Statistics graphs)
- Firebase Crashlytics
- Gson (JSON parsing)

## 📚 Documentation

Comprehensive documentation is available in the [`DOCS/`](DOCS/) folder:

- [Quick Start Guide](DOCS/QUICK_START.md) - Get started fast
- [Build Instructions](DOCS/BUILD_AND_RUN.md) - Detailed build guide
- [Project Overview](DOCS/PROJECT_OVERVIEW.md) - Features and architecture
- [Architecture Guide](DOCS/ARCHITECTURE.md) - Code structure and patterns
- [Key Components](DOCS/KEY_COMPONENTS.md) - Important files explained

## 🗺️ Roadmap

Exciting updates in the pipeline:

*   **Companions:** Friendly companions to accompany your productivity journey
*   **Friendly Battles:** Compete with friends to stay motivated
*   **Advanced Statistics:** More detailed insights and analytics
*   **Themes:** Customizable app themes
*   **And much more!**

## 🤝 Contributing

We welcome all contributions! Whether you're a seasoned developer or just starting:

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit your changes (`git commit -m 'Add some AmazingFeature'`)
4. Push to the branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

### Current Focus
- Improving animations and transitions
- Enhancing UI/UX
- Bug fixes and performance improvements
- Adding new features from the roadmap

## 🐛 Known Issues

- Firebase Crashlytics requires valid `google-services.json` for production builds
- First build may take 5-10 minutes while dependencies download

## 📄 License

This project is licensed under a modified MIT License - see the [LICENSE](LICENSE) file for details.

## 🙏 Acknowledgments

- Original project by [@gtxprime](https://github.com/gtxprime/mind-mint)
- All contributors who help improve Mind Mint

## 📞 Support

- Open an [Issue](https://github.com/tech4commun/mind-mint/issues) for bug reports
- Check [Discussions](https://github.com/tech4commun/mind-mint/discussions) for questions
- Read the [Documentation](DOCS/) for detailed information

---

**Made with ❤️ to help you stay focused and productive** 
