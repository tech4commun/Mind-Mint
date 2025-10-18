# Mind Mint - Architecture & Code Structure

## 🏛️ Architecture Overview

Mind Mint follows the **traditional Android architecture pattern** with Activities, Services, and Receivers. It does NOT use modern architectures like MVVM/MVP/MVI, making it simpler but more monolithic.

```
┌─────────────────────────────────────────┐
│         Presentation Layer              │
│  (Activities, Fragments, Custom Views)  │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│          Business Logic Layer           │
│    (Utils, Managers, Data Classes)      │
└──────────────┬──────────────────────────┘
               │
┌──────────────▼──────────────────────────┐
│          Data Layer                     │
│  (SharedPreferences, JSON Assets)       │
└─────────────────────────────────────────┘

      Background Processing:
┌─────────────────────────────────────────┐
│  Services & Receivers                   │
│  - AppUsageAccessibilityService         │
│  - FocusService                         │
│  - MidnightResetReceiver                │
└─────────────────────────────────────────┘
```

---

## 📁 Directory Structure Explained

### `app/src/main/java/com/gxdevs/mindmint/`

#### **Activities/** - UI Screens
Each Activity represents a full screen in the app.

| File | Purpose | Key Features |
|------|---------|--------------|
| `OnBoarding.java` | First launch tutorial | Permission requests, intro slides |
| `HomeActivity.java` | Main dashboard | App blocking, stats display, navigation hub |
| `FocusMode.java` | Focus timer screen | Countdown timer, currency earning |
| `TaskActivity.java` | To-do list | Create/edit/delete tasks |
| `HabitActivity.java` | Habit tracker | Daily habits, streaks |
| `HabitDetailActivity.java` | Individual habit stats | Charts, completion history |
| `StatsActivity.java` | Usage statistics | Weekly/monthly graphs |
| `SettingsActivity.java` | App settings | Preferences, notifications |
| `CustomAppSelectionActivity.java` | Choose apps to block | App list with checkboxes |
| `BlockingOverlayDisplayActivity.java` | Blocking screen | Shown when blocked app opened |

#### **Services/** - Background Processes
Services run in the background without UI.

| File | Purpose | Lifecycle |
|------|---------|-----------|
| `AppUsageAccessibilityService.java` | **Core service** - monitors which app is open | Always running (when enabled) |
| `FocusService.java` | Manages focus timer in background | Active during focus sessions |

**AppUsageAccessibilityService** is the heart of the app:
- Extends `AccessibilityService`
- Detects when user opens blocked apps
- Launches `BlockingOverlayDisplayActivity`
- Tracks app usage time
- Updates daily statistics

#### **Utils/** - Helper Classes
Utility classes for common operations.

| File | Purpose | Storage |
|------|---------|---------|
| `MintCrystals.java` | Manage in-app currency (crystals) | SharedPreferences |
| `PeaceCoins.java` | Manage peace coins currency | SharedPreferences |
| `TaskManager.java` | CRUD operations for tasks | SharedPreferences (JSON) |
| `HabitManager.java` | CRUD operations for habits | SharedPreferences (JSON) |
| `StreakManager.java` | Track daily streaks | SharedPreferences |
| `StreakPrefs.java` | Streak data persistence | SharedPreferences |
| `TaskNotificationManager.java` | Schedule task reminders | AlarmManager |
| `Utils.java` | Misc helper functions | N/A |

#### **Models/** - Data Classes
Plain Old Java Objects (POJOs) representing data.

| File | Represents | Fields |
|------|-----------|--------|
| `AppInfo.java` | An installed app | Package name, app name, icon, usage time |
| `Habit.java` | A habit | Name, description, frequency, streak, completion dates |
| `Task.java` | A to-do task | Title, description, due date, completed status |

#### **Adapters/** - RecyclerView Adapters
Connect data to UI lists.

| File | Displays | Used In |
|------|----------|---------|
| `AppAdapter.java` | List of apps to block | `CustomAppSelectionActivity` |
| `HabitAdapter.java` | List of habits | `HabitActivity` |
| `TaskAdapter.java` | List of tasks | `TaskActivity` |
| `StatsAdapter.java` | Usage statistics | `StatsActivity` |

#### **Views/** - Custom UI Components
Reusable custom views.

| File | Purpose | Technology |
|------|---------|-----------|
| `AnalogClockView.java` | Custom clock widget | Canvas drawing |
| `RevealMaskImageView.java` | Animated reveal effect | Bitmap masking |

#### **Components/** - UI Components
Specialized UI elements.

| File | Purpose | Library |
|------|---------|---------|
| `RoundedBarChart.java` | Custom rounded bar charts | Extends MPAndroidChart |

#### **Fragments/** - Bottom Sheets
Dialog-like UI fragments.

| File | Purpose | Displayed From |
|------|---------|---------------|
| `AddTaskBottomSheet.java` | Create new task | `TaskActivity` |
| `RepeatOptionsBottomSheet.java` | Set task repeat schedule | `AddTaskBottomSheet` |

#### **Receivers/** - Broadcast Receivers
React to system events.

| File | Trigger | Action |
|------|---------|--------|
| `MidnightResetReceiver.java` | Daily at midnight (AlarmManager) | Reset daily stats, update streaks |
| `TaskReminderReceiver.java` | Task reminder time | Show notification |
| `ServiceResumeReceiver.java` | Device boot complete | Restart services |

---

## 🔄 Data Flow Examples

### Example 1: Blocking an App

```
User opens Instagram
    ↓
AppUsageAccessibilityService.onAccessibilityEvent()
    ↓
Checks if "Instagram" is in blocked apps list (SharedPreferences)
    ↓
If YES → Launch BlockingOverlayDisplayActivity
    ↓
Overlay displayed with "Return to Focus" message
    ↓
User presses back → Returns to home
```

### Example 2: Starting Focus Mode

```
User clicks "Start Focus" button in FocusMode Activity
    ↓
FocusMode.java calls startService(FocusService)
    ↓
FocusService starts with foreground notification
    ↓
Timer counts down (updates UI via broadcasts)
    ↓
On completion:
  - FocusService stops
  - MintCrystals.addCrystals(amount) called
  - SharedPreferences updated
  - UI shows "Focus Complete" animation
```

### Example 3: Creating a Habit

```
User fills form in HabitActivity
    ↓
Clicks "Save" → HabitActivity.createHabit()
    ↓
Creates new Habit object
    ↓
HabitManager.addHabit(habit)
    ↓
Converts Habit to JSON (Gson)
    ↓
Saves to SharedPreferences
    ↓
HabitAdapter notified → RecyclerView updates
```

---

## 💾 Data Persistence

### SharedPreferences Files
All data stored in XML format in:
```
/data/data/com.gxdevs.mindmint/shared_prefs/
```

| File | Contains |
|------|----------|
| `MintCrystalsPrefs.xml` | Currency balance |
| `PeaceCoinsPrefs.xml` | Peace coins balance |
| `HabitPrefs.xml` | Habits list (JSON array) |
| `TaskPrefs.xml` | Tasks list (JSON array) |
| `StreakPrefs.xml` | Streak counts, last check-in dates |
| `AppBlockerPrefs.xml` | Blocked apps list, time limits |
| `[Other prefs].xml` | Settings, first launch flag, etc. |

### JSON Asset Files
Static data in `app/src/main/assets/`:
- `amber.json`, `amethyst.json`, etc. - Likely companion/character data (not yet implemented)

---

## 🧩 Key Design Patterns

### 1. **Singleton Pattern**
Used in manager classes:
```java
public class HabitManager {
    private static HabitManager instance;
    
    public static HabitManager getInstance(Context context) {
        if (instance == null) {
            instance = new HabitManager(context);
        }
        return instance;
    }
}
```

### 2. **Adapter Pattern**
RecyclerView adapters:
```java
public class HabitAdapter extends RecyclerView.Adapter<HabitAdapter.ViewHolder> {
    // Adapts Habit data to RecyclerView
}
```

### 3. **Observer Pattern**
Implicit through Android callbacks:
- `onAccessibilityEvent()` - Observes system events
- `onReceive()` - Observes broadcasts
- `onClick()` - Observes UI interactions

---

## 🔧 Configuration Files

### `AndroidManifest.xml`
The app's configuration file defines:
- **Permissions**: What system features the app can access
- **Activities**: All screens in the app
- **Services**: Background processes
- **Receivers**: Event listeners
- **Queries**: What apps can be detected (for app blocking)

### `build.gradle` (app-level)
Defines:
- **Dependencies**: External libraries
- **SDK versions**: Min/target Android versions
- **Build types**: Debug vs Release
- **Signing config**: For release builds

### `libs.versions.toml`
Centralized version management for dependencies.

---

## 🎨 Resource Structure

### `res/layout/` - UI Layouts
XML files defining screen layouts:
- `activity_*.xml` - Activity layouts
- `fragment_*.xml` - Fragment layouts
- `item_*.xml` - RecyclerView item layouts
- `bottom_sheet_*.xml` - Bottom sheet layouts

### `res/drawable/` - Graphics
- XML drawables (shapes, gradients)
- Vector graphics (.xml)
- PNG images (icons, backgrounds)

### `res/values/` - Constants
- `strings.xml` - Text strings
- `colors.xml` - Color palette
- `styles.xml` - UI themes
- `dimens.xml` - Dimensions (margins, padding)

### `res/anim/` - Animations
XML animation definitions

---

## 🔐 Accessibility Service (Critical Component)

### Why It's Needed
Android's security model prevents apps from knowing what other apps are running. The **Accessibility Service** is a legitimate workaround designed for assistive technologies (screen readers), but also used for app monitoring.

### How It Works
```java
public class AppUsageAccessibilityService extends AccessibilityService {
    @Override
    public void onAccessibilityEvent(AccessibilityEvent event) {
        // Triggered when user switches apps
        String packageName = event.getPackageName().toString();
        
        // Check if package is in blocked list
        if (isBlocked(packageName)) {
            showBlockingOverlay();
        }
    }
}
```

### Security & Privacy
- User must manually enable in Settings → Accessibility
- Can see app names, but NOT app content
- Cannot read passwords or sensitive data
- Standard practice for parental control/focus apps

---

## 📊 Threading Model

- **Main Thread**: UI operations, user interactions
- **Background Thread**: Not extensively used (could be improved)
- **AsyncTask**: Not used (deprecated anyway)
- **Services**: Run on main thread but can perform long operations

**Note**: The app could benefit from using modern concurrency:
- WorkManager for background tasks
- Coroutines or RxJava for async operations
- ViewModel for data persistence across configuration changes

---

## 🚀 Potential Improvements

The architecture could be modernized:

1. **MVVM Architecture**: Separate business logic from UI
2. **Repository Pattern**: Abstract data sources
3. **Dependency Injection**: Use Hilt/Dagger
4. **Jetpack Components**:
   - Room Database (instead of SharedPreferences)
   - LiveData/StateFlow (reactive data)
   - Navigation Component (instead of manual navigation)
   - WorkManager (instead of AlarmManager)
5. **Kotlin Migration**: Modern language features
6. **Compose UI**: Declarative UI framework

---

## 📚 Further Reading

- [Android Developer Guide](https://developer.android.com/guide)
- [Accessibility Services](https://developer.android.com/guide/topics/ui/accessibility/service)
- [Services Overview](https://developer.android.com/guide/components/services)
- [SharedPreferences Guide](https://developer.android.com/training/data-storage/shared-preferences)
