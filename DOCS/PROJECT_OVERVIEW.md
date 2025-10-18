# Mind Mint - Project Overview

## 🎯 What is Mind Mint?

Mind Mint is an **Android productivity app** designed to help users combat "doomscrolling" (endless scrolling through social media) and improve focus. It's built natively for Android using Java and XML.

---

## 🏗️ Project Structure

```
Mind-Mint/
├── app/                          # Main application module
│   ├── src/main/
│   │   ├── java/com/gxdevs/mindmint/
│   │   │   ├── Activities/      # Screen/Activity classes
│   │   │   ├── Services/        # Background services
│   │   │   ├── Utils/           # Helper classes
│   │   │   ├── Views/           # Custom UI components
│   │   │   ├── Models/          # Data models
│   │   │   ├── Adapters/        # RecyclerView adapters
│   │   │   ├── Fragments/       # UI fragments
│   │   │   ├── Receivers/       # Broadcast receivers
│   │   │   └── Components/      # Reusable UI components
│   │   ├── res/                 # Resources (layouts, images, etc.)
│   │   └── AndroidManifest.xml  # App configuration
│   └── build.gradle             # App-level build configuration
├── gradle/                       # Gradle wrapper files
├── build.gradle                 # Project-level build config
└── settings.gradle              # Project settings
```

---

## 🔑 Core Features

### 1. **App Blocker**
- Blocks distracting apps (Instagram Reels, YouTube Shorts, Snapchat)
- Uses Accessibility Service to monitor app usage
- Displays overlay when blocked apps are opened

### 2. **Focus Mode**
- Dedicated focus timer
- Earn in-app currency (Mint Crystals & Peace Coins) while focused
- Background service to track focus sessions

### 3. **Task Manager**
- Create and manage to-do lists
- Set reminders for tasks
- Track task completion

### 4. **Habit Tracker**
- Build and track daily habits
- Weekly and monthly statistics
- Streak tracking system

### 5. **Time Management**
- Set time limits for apps
- Daily usage tracking
- Midnight reset for daily stats

---

## 🛠️ Technology Stack

| Component | Technology |
|-----------|-----------|
| **Language** | Java |
| **UI** | XML Layouts |
| **Build System** | Gradle |
| **Min SDK** | 28 (Android 9.0) |
| **Target SDK** | 35 (Android 15) |
| **Architecture** | Android SDK Standard (Activities, Services, Receivers) |

---

## 📦 Key Dependencies

### Core Android Libraries
- **AndroidX** - Modern Android support libraries
- **Material Design** - Google's Material UI components
- **ConstraintLayout** - Flexible layout system

### Third-Party Libraries
- **Lottie** - Animation library (JSON-based animations)
- **MPAndroidChart** - Chart/graph visualization
- **Firebase Crashlytics** - Crash reporting
- **Gson** - JSON parsing
- **Blurry** - Image blur effects
- **SplashScreen** - Modern splash screen API

---

## 🔐 Permissions Required

The app requires these Android permissions:

| Permission | Purpose |
|------------|---------|
| `INTERNET` | Firebase analytics, crash reporting |
| `ACCESS_NETWORK_STATE` | Check connectivity |
| `SCHEDULE_EXACT_ALARM` | Task reminders, midnight reset |
| `FOREGROUND_SERVICE` | Focus mode background tracking |
| `REQUEST_IGNORE_BATTERY_OPTIMIZATIONS` | Ensure services run reliably |
| `POST_NOTIFICATIONS` | Show task/focus reminders |
| **Accessibility Service** | Monitor app usage for blocking feature |

---

## 🎨 UI/UX Design

- **Theme**: Custom dark theme with gradient backgrounds
- **Animations**: Lottie animations for smooth transitions
- **Navigation**: Activity-based navigation
- **Components**: Material Design with custom styling
- **Colors**: Mint green primary color scheme

---

## 📱 App Flow

```
OnBoarding (First Launch)
    ↓
HomeActivity (Main Dashboard)
    ├── FocusMode (Timer & Focus Session)
    ├── TaskActivity (To-Do List)
    ├── HabitActivity (Habit Tracker)
    ├── StatsActivity (Usage Statistics)
    ├── SettingsActivity (App Settings)
    └── CustomAppSelectionActivity (Choose apps to block)

Background Services:
- AppUsageAccessibilityService (Always running when enabled)
- FocusService (Active during focus sessions)

Receivers:
- MidnightResetReceiver (Daily stats reset)
- TaskReminderReceiver (Task notifications)
```

---

## 🔄 How It Works

### App Blocking Mechanism
1. User selects apps to block in `CustomAppSelectionActivity`
2. `AppUsageAccessibilityService` monitors which app is currently open
3. When a blocked app is detected, `BlockingOverlayDisplayActivity` is shown
4. User must close the app or wait for the timer

### Focus Mode
1. User starts a focus session in `FocusMode`
2. `FocusService` runs in the background with notification
3. Timer counts down while blocking selected apps
4. On completion, user earns Mint Crystals (currency)

### Habit Tracking
1. User creates habits with daily goals
2. `HabitManager` stores data in SharedPreferences (local storage)
3. Daily check-ins update streak counts
4. `StatsActivity` displays charts using MPAndroidChart

---

## 💾 Data Storage

- **SharedPreferences** - All app data is stored locally
- **No Cloud Sync** - Data stays on device
- **JSON Files** - Animation assets stored in `assets/` folder

---

## 🚀 Getting Started

See `BUILD_AND_RUN.md` for instructions on building and running the app.

---

## 🔒 Privacy & Security

✅ **Local-First**: All data stored on device  
✅ **No Account Required**: No login/signup  
✅ **Minimal Network Usage**: Only Firebase analytics  
✅ **Open Source**: Code is publicly auditable  

---

## 📄 License

Modified MIT License - See `LICENSE` file for details.
