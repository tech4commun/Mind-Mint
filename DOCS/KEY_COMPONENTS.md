# Key Components Deep Dive

This document explains the most important files in the Mind Mint project and how they work together.

---

## 🎯 Core Files (Start Here)

### 1. `AndroidManifest.xml`
**Location**: `app/src/main/AndroidManifest.xml`  
**Purpose**: The app's blueprint - declares all components and permissions

**What's Inside**:
```xml
<!-- Permissions the app needs -->
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.FOREGROUND_SERVICE" />

<!-- Activities (screens) -->
<activity android:name=".Activities.HomeActivity" />

<!-- Services (background processes) -->
<service android:name=".Services.AppUsageAccessibilityService" />

<!-- Receivers (event listeners) -->
<receiver android:name=".Receivers.MidnightResetReceiver" />
```

**Key Sections**:
- **Permissions**: What system features the app can access
- **Queries**: Allows app to see installed apps (for blocking)
- **Activities**: All screens declared with launch modes
- **Services**: Background processes (Accessibility Service, Focus Service)
- **Receivers**: Event listeners (midnight reset, boot complete)

---

## 🖥️ Main Activities

### 2. `HomeActivity.java`
**Location**: `app/src/main/java/com/gxdevs/mindmint/Activities/HomeActivity.java`  
**Purpose**: The main dashboard after onboarding

**Responsibilities**:
- Display blocked apps list
- Show daily statistics (time saved, apps blocked)
- Navigate to other features (Focus, Tasks, Habits, Stats)
- Configure app blocking settings
- Show bottom sheets for time picker and app selection

**Key Methods**:
```java
void setupBlockedApps()          // Load blocked apps from preferences
void navigateToFocusMode()       // Start focus session
void showTimePicker()            // Bottom sheet for setting time limits
void updateStats()               // Refresh daily statistics
```

**UI Components**:
- RecyclerView of blocked apps
- Statistics cards (time saved, sessions completed)
- Navigation buttons to other activities
- Bottom sheets for configuration

---

### 3. `FocusMode.java`
**Location**: `app/src/main/java/com/gxdevs/mindmint/Activities/FocusMode.java`  
**Purpose**: Focus timer screen where users start focus sessions

**How It Works**:
1. User sets focus duration (10min, 25min, custom)
2. Clicks "Start Focus"
3. `FocusService` starts in background
4. Timer counts down with notification
5. On completion:
   - Earns Mint Crystals (in-app currency)
   - Shows success animation (Lottie)
   - Updates streak

**Key Features**:
- Customizable timer duration
- Background service with notification
- Animated timer display
- Reward system integration
- Bottom sheet for companion selection (future feature)

**Key Methods**:
```java
void startFocusSession()         // Starts FocusService
void onFocusComplete()           // Handles completion rewards
void updateTimer()               // Updates countdown display
```

---

### 4. `TaskActivity.java`
**Location**: `app/src/main/java/com/gxdevs/mindmint/Activities/TaskActivity.java`  
**Purpose**: To-do list management

**Features**:
- Create, edit, delete tasks
- Mark tasks as complete
- Set reminders (notifications)
- Organize by categories
- Swipe to delete

**Data Flow**:
```
User creates task
    ↓
AddTaskBottomSheet shows
    ↓
User fills form (title, due date, reminder)
    ↓
TaskManager.addTask(task)
    ↓
Saved to SharedPreferences as JSON
    ↓
TaskAdapter updates RecyclerView
    ↓
If reminder set → TaskNotificationManager.scheduleReminder()
```

---

### 5. `HabitActivity.java`
**Location**: `app/src/main/java/com/gxdevs/mindmint/Activities/HabitActivity.java`  
**Purpose**: Build and track daily habits

**Features**:
- Create habits with daily/weekly goals
- Check in daily
- Track streaks
- View completion percentage
- Navigate to detailed stats

**Habit Types**:
- Daily habits (check-in each day)
- Weekly habits (X times per week)
- Custom frequency

**Key Components**:
- RecyclerView with HabitAdapter
- Floating Action Button to add habits
- Streak counter display
- Click → Navigate to HabitDetailActivity

---

## 🔧 Background Services

### 6. `AppUsageAccessibilityService.java`
**Location**: `app/src/main/java/com/gxdevs/mindmint/Services/AppUsageAccessibilityService.java`  
**Purpose**: **THE CORE OF THE APP** - Monitors app usage and enforces blocking

**How Accessibility Services Work**:
```
User enables accessibility service in Settings
    ↓
Service runs continuously in background
    ↓
onAccessibilityEvent() triggered when user interacts with apps
    ↓
Service checks if current app is blocked
    ↓
If blocked → Launch BlockingOverlayDisplayActivity
```

**Key Responsibilities**:
1. **App Monitoring**: Detect which app is currently open
2. **Blocking Enforcement**: Show overlay when blocked app detected
3. **Usage Tracking**: Record time spent in apps
4. **Daily Stats**: Update statistics
5. **Time Limits**: Enforce daily time limits per app

**Key Methods**:
```java
void onAccessibilityEvent(AccessibilityEvent event)  // Main event handler
boolean isAppBlocked(String packageName)             // Check if app is blocked
void showBlockingOverlay()                           // Display blocking screen
void updateUsageStats(String packageName, long time) // Track usage time
void checkTimeLimits()                               // Enforce time limits
```

**Configuration**: `res/xml/accessibility_service_config.xml`
```xml
<accessibility-service
    android:accessibilityEventTypes="typeWindowStateChanged"
    android:accessibilityFeedbackType="feedbackGeneric"
    android:canRetrieveWindowContent="true" />
```

**Security Implications**:
- Can see which apps are opened
- **Cannot** see app content, passwords, or messages
- Standard practice for parental controls, screen time apps
- User must manually enable in Settings

---

### 7. `FocusService.java`
**Location**: `app/src/main/java/com/gxdevs/mindmint/Services/FocusService.java`  
**Purpose**: Manage focus timer in background

**Service Type**: Foreground Service (shows notification)

**Lifecycle**:
```
FocusMode.startFocusSession()
    ↓
startService(new Intent(this, FocusService.class))
    ↓
FocusService.onCreate()
    ↓
Shows foreground notification
    ↓
Timer runs (Handler.postDelayed or CountDownTimer)
    ↓
Broadcasts progress updates to FocusMode
    ↓
On completion → stopSelf()
```

**Why Foreground Service?**:
- Android kills background services to save battery
- Foreground services with notification are protected
- Ensures timer completes even if user switches apps

**Key Methods**:
```java
void onStartCommand()            // Start timer
void startTimer()                // Initialize countdown
void onTimerTick()               // Update progress
void onTimerComplete()           // Handle completion
void sendBroadcast()             // Update UI
```

---

## 📊 Data Management

### 8. `HabitManager.java`
**Location**: `app/src/main/java/com/gxdevs/mindmint/Utils/HabitManager.java`  
**Purpose**: CRUD operations for habits

**Storage**: SharedPreferences (JSON serialization with Gson)

**Key Methods**:
```java
void addHabit(Habit habit)                    // Create new habit
List<Habit> getHabits()                       // Read all habits
void updateHabit(Habit habit)                 // Update existing
void deleteHabit(String habitId)              // Delete habit
void markComplete(String habitId, Date date)  // Check in
int getStreak(String habitId)                 // Calculate streak
```

**Data Structure**:
```json
{
  "habits": [
    {
      "id": "uuid-1234",
      "name": "Morning Exercise",
      "frequency": "daily",
      "streak": 7,
      "completedDates": ["2025-10-11", "2025-10-12", "..."],
      "createdAt": "2025-10-01"
    }
  ]
}
```

---

### 9. `TaskManager.java`
**Location**: `app/src/main/java/com/gxdevs/mindmint/Utils/TaskManager.java`  
**Purpose**: CRUD operations for tasks

Similar to HabitManager but for tasks:
- Create, read, update, delete tasks
- Schedule notifications via TaskNotificationManager
- Mark tasks as complete
- Filter by category/priority

---

### 10. `MintCrystals.java` & `PeaceCoins.java`
**Location**: `app/src/main/java/com/gxdevs/mindmint/Utils/`  
**Purpose**: Manage in-app currency (gamification)

**MintCrystals**: Earned from focus sessions
**PeaceCoins**: Earned from completing tasks/habits

**Key Methods**:
```java
void addCrystals(int amount)     // Increase balance
void spendCrystals(int amount)   // Decrease balance
int getBalance()                 // Get current amount
```

**Use Cases** (some planned for future):
- Unlock companions/avatars
- Unlock themes
- Progress rewards
- Leaderboard comparisons

---

## 📱 UI Components

### 11. RecyclerView Adapters

#### `HabitAdapter.java`
Displays list of habits in `HabitActivity`

**Structure**:
```java
public class HabitAdapter extends RecyclerView.Adapter<HabitAdapter.ViewHolder> {
    List<Habit> habits;
    
    class ViewHolder extends RecyclerView.ViewHolder {
        TextView habitName;
        TextView streakCount;
        CheckBox completedToday;
    }
    
    @Override
    void onBindViewHolder(ViewHolder holder, int position) {
        Habit habit = habits.get(position);
        holder.habitName.setText(habit.getName());
        holder.streakCount.setText(habit.getStreak() + " days");
        // ... bind other fields
    }
}
```

#### `TaskAdapter.java`
Displays list of tasks in `TaskActivity`

Similar structure with:
- Task name, due date, priority
- Checkbox for completion
- Swipe to delete functionality

---

### 12. Custom Views

#### `AnalogClockView.java`
**Purpose**: Custom animated clock widget

**How It Works**:
- Extends `View`
- Overrides `onDraw(Canvas canvas)`
- Draws clock face, hour/minute hands
- Updates with Handler for animation

**Usage**: Displayed in FocusMode during timer

---

## 🔔 Receivers

### 13. `MidnightResetReceiver.java`
**Location**: `app/src/main/java/com/gxdevs/mindmint/Receivers/MidnightResetReceiver.java`  
**Purpose**: Daily reset at midnight

**Scheduled By**: AlarmManager (exact alarm at midnight)

**Actions**:
1. Reset daily usage statistics
2. Clear today's app times
3. Update streaks (increment or break)
4. Reset daily limits
5. Schedule next midnight alarm

**Code Flow**:
```java
void onReceive(Context context, Intent intent) {
    // Reset daily stats
    SharedPreferences prefs = context.getSharedPreferences("stats", MODE_PRIVATE);
    prefs.edit().clear().apply();
    
    // Update habits (check for broken streaks)
    HabitManager.updateStreaks();
    
    // Schedule next midnight
    scheduleNextMidnightAlarm();
}
```

---

### 14. `TaskReminderReceiver.java`
**Purpose**: Show task reminder notifications

**Triggered**: When task reminder time arrives (AlarmManager)

**Actions**:
1. Receive alarm intent with task data
2. Build notification
3. Show notification to user
4. Clicking notification → Opens TaskActivity

---

## 🎨 Resources

### 15. Layout Files (`res/layout/`)

Key layouts:
- `activity_home.xml` - Main dashboard UI
- `activity_focus_mode.xml` - Focus timer screen
- `bottom_sheet_time_picker.xml` - Time selection dialog
- `item_habit.xml` - Individual habit card in RecyclerView
- `activity_blocking_overlay.xml` - Blocking screen shown when app blocked

**Structure Example** (`item_habit.xml`):
```xml
<androidx.cardview.widget.CardView>
    <LinearLayout>
        <TextView android:id="@+id/habitName" />
        <TextView android:id="@+id/streakCount" />
        <CheckBox android:id="@+id/checkToday" />
    </LinearLayout>
</androidx.cardview.widget.CardView>
```

---

### 16. Drawables (`res/drawable/`)

Types:
- **Shape Drawables**: `circle.xml`, `rounded_bg.xml` (XML-defined shapes)
- **Gradients**: `focus_gradient.xml`, `bg1.xml` (color gradients)
- **Vector Icons**: `ic_arrow.xml`, `bell.xml` (scalable icons)
- **State Lists**: `custom_button.xml` (button states: normal, pressed)

**Example** (`circle.xml`):
```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="oval">
    <solid android:color="@color/mint_green" />
    <size android:width="50dp" android:height="50dp" />
</shape>
```

---

### 17. Animations (`res/anim/`)

- `button_anim.xml` - Button press animation
- Various transition animations

**Lottie Animations**: JSON files in `assets/`
- Used for complex animations (focus complete, loading)
- Created with Adobe After Effects

---

## 🔐 Configuration Files

### 18. `accessibility_service_config.xml`
**Location**: `app/src/main/res/xml/accessibility_service_config.xml`  
**Purpose**: Configure accessibility service behavior

```xml
<accessibility-service
    android:accessibilityEventTypes="typeWindowStateChanged"
    android:accessibilityFeedbackType="feedbackGeneric"
    android:canRetrieveWindowContent="false"
    android:notificationTimeout="100"
    android:packageNames="@null"  <!-- Monitor all apps -->
/>
```

---

### 19. Build Files

#### `build.gradle` (Project-level)
Defines plugins for the entire project

#### `app/build.gradle` (App-level)
- **Dependencies**: All libraries used
- **SDK versions**: minSdk 28, targetSdk 35
- **Application ID**: `com.gxdevs.mindmint`
- **Version**: Code 5, Name "Pumpkin 5"

#### `libs.versions.toml`
Centralized version catalog:
```toml
[versions]
lottie = "3.4.1"
mpandroidchart = "v3.1.0"

[libraries]
lottie = { module = "com.airbnb.android:lottie", version.ref = "lottie" }
```

---

## 🧪 How to Explore the Code

### Recommended Reading Order:

1. **Start Here**:
   - `AndroidManifest.xml` - See all components
   - `HomeActivity.java` - Main screen logic

2. **Core Functionality**:
   - `AppUsageAccessibilityService.java` - App blocking
   - `FocusMode.java` + `FocusService.java` - Focus timer

3. **Data Layer**:
   - `HabitManager.java` - How data is saved/loaded
   - `Habit.java` - Data model

4. **UI**:
   - `activity_home.xml` - See UI structure
   - `HabitAdapter.java` - See how lists are populated

5. **Background Tasks**:
   - `MidnightResetReceiver.java` - Daily reset logic

---

## 💡 Tips for Understanding the Code

1. **Search for "TODO"**: May contain planned features
2. **Look for SharedPreferences**: All data storage
3. **Check Intent extras**: How data passes between Activities
4. **Follow the broadcasts**: How services communicate with UI
5. **Trace button clicks**: Start from `onClick()` methods

---

## 🚀 Next Steps

- **Modify**: Try changing colors in `colors.xml`
- **Add Feature**: Create a new habit type
- **Fix Bug**: Check GitHub Issues for known problems
- **Improve UI**: Update layout files
- **Optimize**: Add background threads for heavy operations

---

## 📚 Resources

- [Android Activity Lifecycle](https://developer.android.com/guide/components/activities/activity-lifecycle)
- [Services Overview](https://developer.android.com/guide/components/services)
- [RecyclerView Guide](https://developer.android.com/guide/topics/ui/layout/recyclerview)
- [SharedPreferences Guide](https://developer.android.com/training/data-storage/shared-preferences)
