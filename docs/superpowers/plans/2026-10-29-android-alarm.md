# 安卓极简闹钟 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 构建一个本地离线、系统工具型的安卓极简闹钟首版，支持闹钟列表、新建编辑、到点响铃、贪睡、关闭和重启恢复。

**Architecture:** 采用单模块 Android App 结构，使用 `Jetpack Compose + MVVM + Room + AlarmManager`。将时间计算、调度注册、响铃控制从 UI 中拆出，形成可单测的领域和系统交互层；UI 只负责展示和事件分发。

**Tech Stack:** Kotlin, Jetpack Compose, AndroidX Lifecycle, Room, Coroutines, AlarmManager, BroadcastReceiver, JUnit, kotlinx-coroutines-test

---

## 文件结构

### 根目录

- Create: `settings.gradle.kts`
- Create: `build.gradle.kts`
- Create: `gradle.properties`
- Create: `app/build.gradle.kts`
- Create: `app/proguard-rules.pro`

### Android 配置

- Create: `app/src/main/AndroidManifest.xml`
- Create: `app/src/main/res/xml/backup_rules.xml`
- Create: `app/src/main/res/xml/data_extraction_rules.xml`
- Create: `app/src/main/res/values/strings.xml`
- Create: `app/src/main/res/values/themes.xml`
- Create: `app/src/main/res/values/colors.xml`

### 应用入口与导航

- Create: `app/src/main/java/com/example/alarmclock/AlarmClockApplication.kt`
- Create: `app/src/main/java/com/example/alarmclock/MainActivity.kt`
- Create: `app/src/main/java/com/example/alarmclock/ui/AlarmClockApp.kt`
- Create: `app/src/main/java/com/example/alarmclock/ui/theme/Color.kt`
- Create: `app/src/main/java/com/example/alarmclock/ui/theme/Theme.kt`
- Create: `app/src/main/java/com/example/alarmclock/ui/theme/Type.kt`

### 数据与领域

- Create: `app/src/main/java/com/example/alarmclock/data/local/entity/AlarmEntity.kt`
- Create: `app/src/main/java/com/example/alarmclock/data/local/entity/AppSettingsEntity.kt`
- Create: `app/src/main/java/com/example/alarmclock/data/local/entity/AlarmTriggerRecordEntity.kt`
- Create: `app/src/main/java/com/example/alarmclock/data/local/dao/AlarmDao.kt`
- Create: `app/src/main/java/com/example/alarmclock/data/local/dao/AppSettingsDao.kt`
- Create: `app/src/main/java/com/example/alarmclock/data/local/dao/AlarmTriggerRecordDao.kt`
- Create: `app/src/main/java/com/example/alarmclock/data/local/AlarmDatabase.kt`
- Create: `app/src/main/java/com/example/alarmclock/data/repository/AlarmRepository.kt`
- Create: `app/src/main/java/com/example/alarmclock/domain/model/Alarm.kt`
- Create: `app/src/main/java/com/example/alarmclock/domain/model/AppSettings.kt`
- Create: `app/src/main/java/com/example/alarmclock/domain/model/AlarmTriggerRecord.kt`
- Create: `app/src/main/java/com/example/alarmclock/domain/usecase/CalculateNextTriggerUseCase.kt`
- Create: `app/src/main/java/com/example/alarmclock/domain/usecase/FormatRepeatTextUseCase.kt`

### 调度与系统交互

- Create: `app/src/main/java/com/example/alarmclock/scheduler/AlarmScheduler.kt`
- Create: `app/src/main/java/com/example/alarmclock/scheduler/AndroidAlarmScheduler.kt`
- Create: `app/src/main/java/com/example/alarmclock/scheduler/AlarmIntentFactory.kt`
- Create: `app/src/main/java/com/example/alarmclock/receiver/AlarmReceiver.kt`
- Create: `app/src/main/java/com/example/alarmclock/receiver/BootReceiver.kt`
- Create: `app/src/main/java/com/example/alarmclock/alarm/RingingController.kt`
- Create: `app/src/main/java/com/example/alarmclock/alarm/AlarmRingingActivity.kt`

### 表现层

- Create: `app/src/main/java/com/example/alarmclock/ui/list/AlarmListViewModel.kt`
- Create: `app/src/main/java/com/example/alarmclock/ui/list/AlarmListScreen.kt`
- Create: `app/src/main/java/com/example/alarmclock/ui/edit/AlarmEditViewModel.kt`
- Create: `app/src/main/java/com/example/alarmclock/ui/edit/AlarmEditScreen.kt`
- Create: `app/src/main/java/com/example/alarmclock/ui/ringing/AlarmRingingViewModel.kt`
- Create: `app/src/main/java/com/example/alarmclock/ui/ringing/AlarmRingingScreen.kt`
- Create: `app/src/main/java/com/example/alarmclock/ui/component/AlarmItemCard.kt`
- Create: `app/src/main/java/com/example/alarmclock/ui/component/RepeatChipRow.kt`

### 测试

- Create: `app/src/test/java/com/example/alarmclock/domain/usecase/CalculateNextTriggerUseCaseTest.kt`
- Create: `app/src/test/java/com/example/alarmclock/domain/usecase/FormatRepeatTextUseCaseTest.kt`
- Create: `app/src/test/java/com/example/alarmclock/ui/list/AlarmListViewModelTest.kt`
- Create: `app/src/test/java/com/example/alarmclock/ui/edit/AlarmEditViewModelTest.kt`
- Create: `app/src/test/java/com/example/alarmclock/scheduler/AndroidAlarmSchedulerTest.kt`

## 任务拆分

### Task 1: 搭建 Android 工程骨架

**Files:**
- Create: `settings.gradle.kts`
- Create: `build.gradle.kts`
- Create: `gradle.properties`
- Create: `app/build.gradle.kts`
- Create: `app/src/main/AndroidManifest.xml`
- Create: `app/src/main/java/com/example/alarmclock/AlarmClockApplication.kt`
- Create: `app/src/main/java/com/example/alarmclock/MainActivity.kt`
- Create: `app/src/main/java/com/example/alarmclock/ui/AlarmClockApp.kt`
- Create: `app/src/main/res/values/themes.xml`

- [ ] **Step 1: 写工程配置文件**

```kotlin
// settings.gradle.kts
pluginManagement {
    repositories {
        google()
        mavenCentral()
        gradlePluginPortal()
    }
}

dependencyResolutionManagement {
    repositoriesMode.set(RepositoriesMode.FAIL_ON_PROJECT_REPOS)
    repositories {
        google()
        mavenCentral()
    }
}

rootProject.name = "Android-naozhong"
include(":app")
```

```kotlin
// build.gradle.kts
plugins {
    id("com.android.application") version "8.7.3" apply false
    id("org.jetbrains.kotlin.android") version "2.0.21" apply false
    id("com.google.devtools.ksp") version "2.0.21-1.0.25" apply false
}
```

```properties
# gradle.properties
org.gradle.jvmargs=-Xmx2048m -Dfile.encoding=UTF-8
android.useAndroidX=true
android.nonTransitiveRClass=true
kotlin.code.style=official
```

- [ ] **Step 2: 写 `app/build.gradle.kts`，把 Compose、Room、测试依赖一次配齐**

```kotlin
plugins {
    id("com.android.application")
    id("org.jetbrains.kotlin.android")
    id("com.google.devtools.ksp")
}

android {
    namespace = "com.example.alarmclock"
    compileSdk = 35

    defaultConfig {
        applicationId = "com.example.alarmclock"
        minSdk = 26
        targetSdk = 35
        versionCode = 1
        versionName = "1.0.0"

        testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
        vectorDrawables.useSupportLibrary = true
    }

    buildFeatures {
        compose = true
    }

    composeOptions {
        kotlinCompilerExtensionVersion = "1.5.15"
    }

    kotlinOptions {
        jvmTarget = "17"
    }
}

dependencies {
    val composeBom = platform("androidx.compose:compose-bom:2024.10.01")

    implementation("androidx.core:core-ktx:1.15.0")
    implementation("androidx.activity:activity-compose:1.10.0")
    implementation("androidx.lifecycle:lifecycle-runtime-ktx:2.8.7")
    implementation("androidx.lifecycle:lifecycle-viewmodel-compose:2.8.7")
    implementation("androidx.navigation:navigation-compose:2.8.5")
    implementation(composeBom)
    implementation("androidx.compose.ui:ui")
    implementation("androidx.compose.material3:material3")
    implementation("androidx.compose.ui:ui-tooling-preview")

    implementation("androidx.room:room-runtime:2.6.1")
    implementation("androidx.room:room-ktx:2.6.1")
    ksp("androidx.room:room-compiler:2.6.1")

    testImplementation("junit:junit:4.13.2")
    testImplementation("org.jetbrains.kotlinx:kotlinx-coroutines-test:1.9.0")
    testImplementation("io.mockk:mockk:1.13.13")
}
```

- [ ] **Step 3: 写清单和最小入口**

```xml
<!-- app/src/main/AndroidManifest.xml -->
<manifest xmlns:android="http://schemas.android.com/apk/res/android">
    <uses-permission android:name="android.permission.VIBRATE" />
    <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
    <application
        android:name=".AlarmClockApplication"
        android:allowBackup="true"
        android:supportsRtl="true"
        android:theme="@style/Theme.AlarmClock">
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
</manifest>
```

```kotlin
// AlarmClockApplication.kt
class AlarmClockApplication : Application() {
    fun restoreEnabledAlarms() = Unit
}
```

```kotlin
// MainActivity.kt
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent { AlarmClockApp() }
    }
}
```

```xml
<!-- themes.xml -->
<resources xmlns:tools="http://schemas.android.com/tools">
    <style name="Theme.AlarmClock" parent="Theme.Material3.DayNight.NoActionBar">
        <item name="android:statusBarColor">@android:color/transparent</item>
        <item name="android:navigationBarColor">@android:color/black</item>
    </style>
</resources>
```

- [ ] **Step 4: 写最小 UI 壳，先让列表页能占位显示**

```kotlin
@Composable
fun AlarmClockApp() {
    MaterialTheme {
        Surface(modifier = Modifier.fillMaxSize()) {
            Box(modifier = Modifier.fillMaxSize(), contentAlignment = Alignment.Center) {
                Text(text = "Alarm Clock")
            }
        }
    }
}
```

- [ ] **Step 5: 运行最小编译检查**

Run: `./gradlew :app:assembleDebug`
Expected: `BUILD SUCCESSFUL`

- [ ] **Step 6: 提交骨架**

```bash
git add settings.gradle.kts build.gradle.kts gradle.properties app/build.gradle.kts app/src/main/AndroidManifest.xml app/src/main/java/com/example/alarmclock/AlarmClockApplication.kt app/src/main/java/com/example/alarmclock/MainActivity.kt app/src/main/java/com/example/alarmclock/ui/AlarmClockApp.kt app/src/main/res/values/themes.xml
git commit -m "feat: 初始化安卓闹钟工程骨架"
```

### Task 2: 完成领域模型和时间计算

**Files:**
- Create: `app/src/main/java/com/example/alarmclock/domain/model/Alarm.kt`
- Create: `app/src/main/java/com/example/alarmclock/domain/usecase/CalculateNextTriggerUseCase.kt`
- Create: `app/src/main/java/com/example/alarmclock/domain/usecase/FormatRepeatTextUseCase.kt`
- Test: `app/src/test/java/com/example/alarmclock/domain/usecase/CalculateNextTriggerUseCaseTest.kt`
- Test: `app/src/test/java/com/example/alarmclock/domain/usecase/FormatRepeatTextUseCaseTest.kt`

- [ ] **Step 1: 先写失败的时间计算测试**

```kotlin
class CalculateNextTriggerUseCaseTest {

    private val useCase = CalculateNextTriggerUseCase()

    @Test
    fun `single alarm schedules today when target time is still ahead`() {
        val now = ZonedDateTime.parse("2026-10-29T07:30:00+08:00[Asia/Shanghai]")
        val alarm = Alarm(id = 1, hour = 8, minute = 0, repeatMask = emptySet(), enabled = true)

        val result = useCase(alarm, now)

        assertEquals(
            ZonedDateTime.parse("2026-10-29T08:00:00+08:00[Asia/Shanghai]").toInstant().toEpochMilli(),
            result
        )
    }

    @Test
    fun `weekday repeating alarm skips to next selected day`() {
        val now = ZonedDateTime.parse("2026-10-30T22:00:00+08:00[Asia/Shanghai]")
        val alarm = Alarm(id = 1, hour = 7, minute = 30, repeatMask = setOf(DayOfWeek.MONDAY), enabled = true)

        val result = useCase(alarm, now)

        assertEquals(
            ZonedDateTime.parse("2026-11-02T07:30:00+08:00[Asia/Shanghai]").toInstant().toEpochMilli(),
            result
        )
    }
}
```

- [ ] **Step 2: 运行单测确认失败**

Run: `./gradlew :app:testDebugUnitTest --tests "com.example.alarmclock.domain.usecase.CalculateNextTriggerUseCaseTest"`
Expected: FAIL with `Unresolved reference: CalculateNextTriggerUseCase` or `Alarm`

- [ ] **Step 3: 写最小领域模型和用例实现**

```kotlin
data class Alarm(
    val id: Long,
    val hour: Int,
    val minute: Int,
    val repeatMask: Set<DayOfWeek>,
    val enabled: Boolean,
    val label: String = "",
    val ringtoneUri: String? = null,
    val vibrate: Boolean = true,
    val snoozeMinutes: Int = 10,
    val nextTriggerAt: Long? = null,
)

class CalculateNextTriggerUseCase {
    operator fun invoke(alarm: Alarm, now: ZonedDateTime): Long {
        val base = now.withHour(alarm.hour).withMinute(alarm.minute).withSecond(0).withNano(0)
        if (alarm.repeatMask.isEmpty()) {
            val target = if (base.isAfter(now)) base else base.plusDays(1)
            return target.toInstant().toEpochMilli()
        }

        for (offset in 0..7) {
            val candidate = base.plusDays(offset.toLong())
            if (candidate.dayOfWeek in alarm.repeatMask && candidate.isAfter(now)) {
                return candidate.toInstant().toEpochMilli()
            }
        }
        error("No valid trigger time found")
    }
}
```

```kotlin
class FormatRepeatTextUseCase {
    operator fun invoke(repeatMask: Set<DayOfWeek>): String = when {
        repeatMask.isEmpty() -> "仅一次"
        repeatMask.size == 7 -> "每天"
        repeatMask == setOf(
            DayOfWeek.MONDAY,
            DayOfWeek.TUESDAY,
            DayOfWeek.WEDNESDAY,
            DayOfWeek.THURSDAY,
            DayOfWeek.FRIDAY,
        ) -> "工作日"
        else -> repeatMask.sortedBy { it.value }.joinToString("、") { it.name.take(3) }
    }
}
```

- [ ] **Step 4: 补上重复文案测试并让两个用例一起通过**

```kotlin
class FormatRepeatTextUseCaseTest {
    private val useCase = FormatRepeatTextUseCase()

    @Test
    fun `empty repeat mask returns once text`() {
        assertEquals("仅一次", useCase(emptySet()))
    }

    @Test
    fun `monday to friday returns weekday text`() {
        val text = useCase(
            setOf(
                DayOfWeek.MONDAY,
                DayOfWeek.TUESDAY,
                DayOfWeek.WEDNESDAY,
                DayOfWeek.THURSDAY,
                DayOfWeek.FRIDAY,
            )
        )
        assertEquals("工作日", text)
    }
}
```

- [ ] **Step 5: 运行领域单测**

Run: `./gradlew :app:testDebugUnitTest --tests "com.example.alarmclock.domain.usecase.*"`
Expected: `BUILD SUCCESSFUL`

- [ ] **Step 6: 提交领域层**

```bash
git add app/src/main/java/com/example/alarmclock/domain app/src/test/java/com/example/alarmclock/domain
git commit -m "feat: 添加闹钟领域模型与时间计算"
```

### Task 3: 完成 Room 持久化和仓储接口

**Files:**
- Create: `app/src/main/java/com/example/alarmclock/data/local/entity/AlarmEntity.kt`
- Create: `app/src/main/java/com/example/alarmclock/data/local/dao/AlarmDao.kt`
- Create: `app/src/main/java/com/example/alarmclock/data/local/AlarmDatabase.kt`
- Create: `app/src/main/java/com/example/alarmclock/data/repository/AlarmRepository.kt`
- Modify: `app/src/main/java/com/example/alarmclock/domain/model/Alarm.kt`
- Test: `app/src/test/java/com/example/alarmclock/ui/list/AlarmListViewModelTest.kt`

- [ ] **Step 1: 先写 ViewModel 期望使用的仓储测试桩**

```kotlin
private class FakeAlarmRepository : AlarmRepository {
    private val state = MutableStateFlow(
        listOf(
            Alarm(id = 1, hour = 7, minute = 30, repeatMask = emptySet(), enabled = true),
            Alarm(id = 2, hour = 8, minute = 0, repeatMask = emptySet(), enabled = false),
        )
    )

    override fun observeAlarms(): Flow<List<Alarm>> = state

    override suspend fun updateEnabled(alarmId: Long, enabled: Boolean) {
        state.value = state.value.map {
            if (it.id == alarmId) it.copy(enabled = enabled) else it
        }
    }
}
```

- [ ] **Step 2: 声明仓储接口和实体映射**

```kotlin
interface AlarmRepository {
    fun observeAlarms(): Flow<List<Alarm>>
    suspend fun getAlarm(alarmId: Long): Alarm?
    suspend fun upsert(alarm: Alarm): Long
    suspend fun delete(alarmId: Long)
    suspend fun updateEnabled(alarmId: Long, enabled: Boolean)
}
```

```kotlin
@Entity(tableName = "alarms")
data class AlarmEntity(
    @PrimaryKey(autoGenerate = true) val id: Long = 0,
    val hour: Int,
    val minute: Int,
    val label: String,
    val repeatDays: String,
    val enabled: Boolean,
    val ringtoneUri: String?,
    val vibrate: Boolean,
    val snoozeMinutes: Int,
    val nextTriggerAt: Long?,
)
```

- [ ] **Step 3: 写 DAO 和数据库定义**

```kotlin
@Dao
interface AlarmDao {
    @Query("SELECT * FROM alarms ORDER BY hour, minute")
    fun observeAll(): Flow<List<AlarmEntity>>

    @Query("SELECT * FROM alarms WHERE id = :alarmId")
    suspend fun getById(alarmId: Long): AlarmEntity?

    @Insert(onConflict = OnConflictStrategy.REPLACE)
    suspend fun upsert(entity: AlarmEntity): Long

    @Query("DELETE FROM alarms WHERE id = :alarmId")
    suspend fun deleteById(alarmId: Long)

    @Query("UPDATE alarms SET enabled = :enabled WHERE id = :alarmId")
    suspend fun updateEnabled(alarmId: Long, enabled: Boolean)
}

@Database(
    entities = [AlarmEntity::class],
    version = 1,
    exportSchema = false,
)
abstract class AlarmDatabase : RoomDatabase() {
    abstract fun alarmDao(): AlarmDao
}
```

- [ ] **Step 4: 写仓储实现，把重复规则转成可存储字符串**

```kotlin
class RoomAlarmRepository(
    private val alarmDao: AlarmDao,
) : AlarmRepository {

    override fun observeAlarms(): Flow<List<Alarm>> =
        alarmDao.observeAll().map { list -> list.map { it.toDomain() } }

    override suspend fun getAlarm(alarmId: Long): Alarm? = alarmDao.getById(alarmId)?.toDomain()

    override suspend fun upsert(alarm: Alarm): Long = alarmDao.upsert(alarm.toEntity())

    override suspend fun delete(alarmId: Long) = alarmDao.deleteById(alarmId)

    override suspend fun updateEnabled(alarmId: Long, enabled: Boolean) =
        alarmDao.updateEnabled(alarmId, enabled)
}

private fun AlarmEntity.toDomain(): Alarm = Alarm(
    id = id,
    hour = hour,
    minute = minute,
    label = label,
    repeatMask = repeatDays.split(",").filter { it.isNotBlank() }.map { DayOfWeek.valueOf(it) }.toSet(),
    enabled = enabled,
    ringtoneUri = ringtoneUri,
    vibrate = vibrate,
    snoozeMinutes = snoozeMinutes,
    nextTriggerAt = nextTriggerAt,
)
```

- [ ] **Step 5: 运行受影响单测，确保接口与领域层一致**

Run: `./gradlew :app:testDebugUnitTest --tests "com.example.alarmclock.domain.usecase.*"`
Expected: `BUILD SUCCESSFUL`

- [ ] **Step 6: 提交持久化层**

```bash
git add app/src/main/java/com/example/alarmclock/data app/src/main/java/com/example/alarmclock/domain/model/Alarm.kt
git commit -m "feat: 添加闹钟本地存储与仓储接口"
```

### Task 4: 完成调度器、广播接收和重启恢复

**Files:**
- Create: `app/src/main/java/com/example/alarmclock/scheduler/AlarmScheduler.kt`
- Create: `app/src/main/java/com/example/alarmclock/scheduler/AndroidAlarmScheduler.kt`
- Create: `app/src/main/java/com/example/alarmclock/scheduler/AlarmIntentFactory.kt`
- Create: `app/src/main/java/com/example/alarmclock/receiver/AlarmReceiver.kt`
- Create: `app/src/main/java/com/example/alarmclock/receiver/BootReceiver.kt`
- Modify: `app/src/main/AndroidManifest.xml`
- Test: `app/src/test/java/com/example/alarmclock/scheduler/AndroidAlarmSchedulerTest.kt`

- [ ] **Step 1: 先写调度器接口测试目标**

```kotlin
class AndroidAlarmSchedulerTest {

    @Test
    fun `schedule calls exact alarm api with alarm data`() {
        val alarmManager = mockk<AlarmManager>(relaxed = true)
        val pendingIntent = mockk<PendingIntent>(relaxed = true)
        val factory = mockk<AlarmIntentFactory>()
        every { factory.createAlarmPendingIntent(any()) } returns pendingIntent

        val scheduler = AndroidAlarmScheduler(alarmManager, factory)
        scheduler.schedule(1L, 123456789L)

        verify {
            alarmManager.setExactAndAllowWhileIdle(
                AlarmManager.RTC_WAKEUP,
                123456789L,
                pendingIntent
            )
        }
    }
}
```

- [ ] **Step 2: 写调度器接口和最小实现**

```kotlin
interface AlarmScheduler {
    fun schedule(alarmId: Long, triggerAtMillis: Long)
    fun cancel(alarmId: Long)
}

class AlarmIntentFactory(
    private val context: Context,
) {
    fun createAlarmPendingIntent(alarmId: Long): PendingIntent =
        PendingIntent.getBroadcast(
            context,
            alarmId.toInt(),
            Intent(context, AlarmReceiver::class.java).putExtra("alarm_id", alarmId),
            PendingIntent.FLAG_UPDATE_CURRENT or PendingIntent.FLAG_IMMUTABLE,
        )
}

class AndroidAlarmScheduler(
    private val alarmManager: AlarmManager,
    private val intentFactory: AlarmIntentFactory,
) : AlarmScheduler {
    override fun schedule(alarmId: Long, triggerAtMillis: Long) {
        alarmManager.setExactAndAllowWhileIdle(
            AlarmManager.RTC_WAKEUP,
            triggerAtMillis,
            intentFactory.createAlarmPendingIntent(alarmId),
        )
    }

    override fun cancel(alarmId: Long) {
        alarmManager.cancel(intentFactory.createAlarmPendingIntent(alarmId))
    }
}
```

- [ ] **Step 3: 写广播接收器骨架，把触发和恢复入口接好**

```kotlin
class AlarmReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        val alarmId = intent.getLongExtra("alarm_id", -1L)
        if (alarmId <= 0) return
        RingingController(context).start(alarmId)
    }
}

class BootReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
        if (intent.action != Intent.ACTION_BOOT_COMPLETED) return
        val app = context.applicationContext as AlarmClockApplication
        app.restoreEnabledAlarms()
    }
}
```

- [ ] **Step 4: 在清单里声明 Receiver 和响铃权限边界**

```xml
<receiver
    android:name=".receiver.AlarmReceiver"
    android:exported="false" />

<receiver
    android:name=".receiver.BootReceiver"
    android:enabled="true"
    android:exported="true">
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED" />
    </intent-filter>
</receiver>
```

- [ ] **Step 5: 运行调度器单测**

Run: `./gradlew :app:testDebugUnitTest --tests "com.example.alarmclock.scheduler.AndroidAlarmSchedulerTest"`
Expected: `BUILD SUCCESSFUL`

- [ ] **Step 6: 提交调度恢复链路**

```bash
git add app/src/main/java/com/example/alarmclock/scheduler app/src/main/java/com/example/alarmclock/receiver app/src/main/AndroidManifest.xml
git commit -m "feat: 添加闹钟调度与重启恢复入口"
```

### Task 5: 完成闹钟列表页和开关操作

**Files:**
- Create: `app/src/main/java/com/example/alarmclock/ui/list/AlarmListViewModel.kt`
- Create: `app/src/main/java/com/example/alarmclock/ui/list/AlarmListScreen.kt`
- Create: `app/src/main/java/com/example/alarmclock/ui/component/AlarmItemCard.kt`
- Test: `app/src/test/java/com/example/alarmclock/ui/list/AlarmListViewModelTest.kt`
- Modify: `app/src/main/java/com/example/alarmclock/ui/AlarmClockApp.kt`

- [ ] **Step 1: 先写列表 ViewModel 单测**

```kotlin
class AlarmListViewModelTest {

    @Test
    fun `toggle updates repository and local state`() = runTest {
        val repository = FakeAlarmRepository()
        val viewModel = AlarmListViewModel(repository)

        viewModel.onToggleChanged(alarmId = 1, enabled = false)

        val first = viewModel.uiState.value.alarms.first { it.id == 1L }
        assertFalse(first.enabled)
    }
}
```

- [ ] **Step 2: 实现 ViewModel，把列表流和切换逻辑接上**

```kotlin
data class AlarmListUiState(
    val alarms: List<Alarm> = emptyList(),
)

class AlarmListViewModel(
    private val repository: AlarmRepository,
) : ViewModel() {

    val uiState: StateFlow<AlarmListUiState> = repository.observeAlarms()
        .map { alarms -> AlarmListUiState(alarms = alarms) }
        .stateIn(viewModelScope, SharingStarted.WhileSubscribed(5_000), AlarmListUiState())

    fun onToggleChanged(alarmId: Long, enabled: Boolean) {
        viewModelScope.launch {
            repository.updateEnabled(alarmId, enabled)
        }
    }
}
```

- [ ] **Step 3: 写列表页和卡片组件**

```kotlin
@Composable
fun AlarmListScreen(
    uiState: AlarmListUiState,
    onAddClick: () -> Unit,
    onAlarmClick: (Long) -> Unit,
    onToggleChanged: (Long, Boolean) -> Unit,
) {
    Scaffold(
        floatingActionButton = {
            FloatingActionButton(onClick = onAddClick) {
                Icon(Icons.Default.Add, contentDescription = "新增闹钟")
            }
        }
    ) { padding ->
        LazyColumn(contentPadding = padding) {
            items(uiState.alarms, key = { it.id }) { alarm ->
                AlarmItemCard(
                    alarm = alarm,
                    onClick = { onAlarmClick(alarm.id) },
                    onToggleChanged = { onToggleChanged(alarm.id, it) },
                )
            }
        }
    }
}
```

- [ ] **Step 4: 将 `AlarmClockApp()` 替换为列表入口**

```kotlin
@Composable
fun AlarmClockApp(viewModel: AlarmListViewModel) {
    val uiState by viewModel.uiState.collectAsStateWithLifecycle()
    MaterialTheme {
        AlarmListScreen(
            uiState = uiState,
            onAddClick = {},
            onAlarmClick = {},
            onToggleChanged = viewModel::onToggleChanged,
        )
    }
}
```

- [ ] **Step 5: 运行列表单测**

Run: `./gradlew :app:testDebugUnitTest --tests "com.example.alarmclock.ui.list.AlarmListViewModelTest"`
Expected: `BUILD SUCCESSFUL`

- [ ] **Step 6: 提交列表功能**

```bash
git add app/src/main/java/com/example/alarmclock/ui/list app/src/main/java/com/example/alarmclock/ui/component app/src/main/java/com/example/alarmclock/ui/AlarmClockApp.kt app/src/test/java/com/example/alarmclock/ui/list
git commit -m "feat: 添加闹钟列表与启停交互"
```

### Task 6: 完成新建编辑页与保存逻辑

**Files:**
- Create: `app/src/main/java/com/example/alarmclock/ui/edit/AlarmEditViewModel.kt`
- Create: `app/src/main/java/com/example/alarmclock/ui/edit/AlarmEditScreen.kt`
- Create: `app/src/main/java/com/example/alarmclock/ui/component/RepeatChipRow.kt`
- Test: `app/src/test/java/com/example/alarmclock/ui/edit/AlarmEditViewModelTest.kt`
- Modify: `app/src/main/java/com/example/alarmclock/ui/AlarmClockApp.kt`

- [ ] **Step 1: 先写编辑保存单测**

```kotlin
class AlarmEditViewModelTest {

    private class FakeEditableAlarmRepository : AlarmRepository {
        val saved = mutableListOf<Alarm>()

        override fun observeAlarms(): Flow<List<Alarm>> = flowOf(emptyList())
        override suspend fun getAlarm(alarmId: Long): Alarm? = null
        override suspend fun upsert(alarm: Alarm): Long {
            saved += alarm
            return alarm.id
        }
        override suspend fun delete(alarmId: Long) = Unit
        override suspend fun updateEnabled(alarmId: Long, enabled: Boolean) = Unit
    }

    @Test
    fun `save creates enabled alarm with computed next trigger`() = runTest {
        val repository = FakeEditableAlarmRepository()
        val calculator = CalculateNextTriggerUseCase()
        val viewModel = AlarmEditViewModel(repository, calculator)

        viewModel.updateHour(7)
        viewModel.updateMinute(30)
        viewModel.save()

        val saved = repository.saved.single()
        assertTrue(saved.enabled)
        assertNotNull(saved.nextTriggerAt)
    }
}
```

- [ ] **Step 2: 实现编辑状态与保存逻辑**

```kotlin
data class AlarmEditUiState(
    val alarmId: Long? = null,
    val hour: Int = 7,
    val minute: Int = 0,
    val label: String = "",
    val repeatMask: Set<DayOfWeek> = emptySet(),
    val vibrate: Boolean = true,
    val snoozeMinutes: Int = 10,
)

class AlarmEditViewModel(
    private val repository: AlarmRepository,
    private val calculateNextTriggerUseCase: CalculateNextTriggerUseCase,
) : ViewModel() {
    var uiState by mutableStateOf(AlarmEditUiState())
        private set

    fun updateHour(hour: Int) { uiState = uiState.copy(hour = hour) }
    fun updateMinute(minute: Int) { uiState = uiState.copy(minute = minute) }

    fun save(now: ZonedDateTime = ZonedDateTime.now()) {
        viewModelScope.launch {
            val draft = Alarm(
                id = uiState.alarmId ?: 0,
                hour = uiState.hour,
                minute = uiState.minute,
                label = uiState.label,
                repeatMask = uiState.repeatMask,
                enabled = true,
                vibrate = uiState.vibrate,
                snoozeMinutes = uiState.snoozeMinutes,
                nextTriggerAt = null,
            )
            repository.upsert(
                draft.copy(nextTriggerAt = calculateNextTriggerUseCase(draft, now))
            )
        }
    }
}
```

- [ ] **Step 3: 写编辑页面，按设计文档保持单屏顺序**

```kotlin
@Composable
fun AlarmEditScreen(
    uiState: AlarmEditUiState,
    onHourChanged: (Int) -> Unit,
    onMinuteChanged: (Int) -> Unit,
    onSaveClick: () -> Unit,
) {
    Column(modifier = Modifier.fillMaxSize().padding(24.dp)) {
        Text(text = "设置闹钟", style = MaterialTheme.typography.headlineMedium)
        Row(horizontalArrangement = Arrangement.spacedBy(12.dp)) {
            OutlinedTextField(
                value = uiState.hour.toString(),
                onValueChange = { value ->
                    value.toIntOrNull()?.takeIf { it in 0..23 }?.let(onHourChanged)
                },
                label = { Text("小时") },
                keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Number),
                modifier = Modifier.weight(1f),
            )
            OutlinedTextField(
                value = uiState.minute.toString(),
                onValueChange = { value ->
                    value.toIntOrNull()?.takeIf { it in 0..59 }?.let(onMinuteChanged)
                },
                label = { Text("分钟") },
                keyboardOptions = KeyboardOptions(keyboardType = KeyboardType.Number),
                modifier = Modifier.weight(1f),
            )
        }
        Spacer(modifier = Modifier.height(24.dp))
        Button(onClick = onSaveClick, modifier = Modifier.fillMaxWidth()) {
            Text("保存")
        }
    }
}
```

- [ ] **Step 4: 在应用入口上接通列表到编辑的导航状态**

```kotlin
sealed interface AlarmRoute {
    data object List : AlarmRoute
    data class Edit(val alarmId: Long?) : AlarmRoute
}
```

```kotlin
val listState by listViewModel.uiState.collectAsStateWithLifecycle()
val editState = editViewModel.uiState
var route by rememberSaveable { mutableStateOf<AlarmRoute>(AlarmRoute.List) }
when (val current = route) {
    AlarmRoute.List -> AlarmListScreen(
        uiState = listState,
        onAddClick = { route = AlarmRoute.Edit(null) },
        onAlarmClick = { route = AlarmRoute.Edit(it) },
        onToggleChanged = listViewModel::onToggleChanged,
    )
    is AlarmRoute.Edit -> AlarmEditScreen(
        uiState = editState,
        onHourChanged = editViewModel::updateHour,
        onMinuteChanged = editViewModel::updateMinute,
        onSaveClick = {
            editViewModel.save()
            route = AlarmRoute.List
        },
    )
}
```

- [ ] **Step 5: 运行编辑单测**

Run: `./gradlew :app:testDebugUnitTest --tests "com.example.alarmclock.ui.edit.AlarmEditViewModelTest"`
Expected: `BUILD SUCCESSFUL`

- [ ] **Step 6: 提交编辑页**

```bash
git add app/src/main/java/com/example/alarmclock/ui/edit app/src/main/java/com/example/alarmclock/ui/component/RepeatChipRow.kt app/src/main/java/com/example/alarmclock/ui/AlarmClockApp.kt app/src/test/java/com/example/alarmclock/ui/edit
git commit -m "feat: 添加闹钟编辑与保存流程"
```

### Task 7: 完成响铃页、关闭与贪睡闭环

**Files:**
- Create: `app/src/main/java/com/example/alarmclock/alarm/RingingController.kt`
- Create: `app/src/main/java/com/example/alarmclock/alarm/AlarmRingingActivity.kt`
- Create: `app/src/main/java/com/example/alarmclock/ui/ringing/AlarmRingingViewModel.kt`
- Create: `app/src/main/java/com/example/alarmclock/ui/ringing/AlarmRingingScreen.kt`
- Modify: `app/src/main/AndroidManifest.xml`
- Modify: `app/src/main/java/com/example/alarmclock/receiver/AlarmReceiver.kt`

- [ ] **Step 1: 先定义响铃控制器接口**

```kotlin
class RingingController(
    private val context: Context,
) {
    fun start(alarmId: Long) {
        val intent = Intent(context, AlarmRingingActivity::class.java)
            .putExtra("alarm_id", alarmId)
            .addFlags(Intent.FLAG_ACTIVITY_NEW_TASK or Intent.FLAG_ACTIVITY_NO_USER_ACTION)
        context.startActivity(intent)
    }
}
```

- [ ] **Step 2: 写响铃 ViewModel，分别处理关闭和贪睡**

```kotlin
class AlarmRingingViewModel(
    private val repository: AlarmRepository,
    private val scheduler: AlarmScheduler,
    private val calculateNextTriggerUseCase: CalculateNextTriggerUseCase,
) : ViewModel() {

    fun dismiss(alarm: Alarm, now: ZonedDateTime = ZonedDateTime.now()) {
        viewModelScope.launch {
            if (alarm.repeatMask.isEmpty()) {
                repository.updateEnabled(alarm.id, false)
                scheduler.cancel(alarm.id)
            } else {
                val next = calculateNextTriggerUseCase(alarm, now)
                repository.upsert(alarm.copy(nextTriggerAt = next))
                scheduler.schedule(alarm.id, next)
            }
        }
    }

    fun snooze(alarm: Alarm, now: ZonedDateTime = ZonedDateTime.now()) {
        val trigger = now.plusMinutes(alarm.snoozeMinutes.toLong()).toInstant().toEpochMilli()
        scheduler.schedule(alarm.id, trigger)
    }
}
```

- [ ] **Step 3: 写高对比度响铃页**

```kotlin
@Composable
fun AlarmRingingScreen(
    alarmLabel: String,
    onDismissClick: () -> Unit,
    onSnoozeClick: () -> Unit,
) {
    Surface(
        modifier = Modifier.fillMaxSize(),
        color = Color.Black,
    ) {
        Column(
            modifier = Modifier.fillMaxSize().padding(24.dp),
            verticalArrangement = Arrangement.SpaceBetween,
        ) {
            Text(text = alarmLabel, color = Color.White, style = MaterialTheme.typography.headlineLarge)
            Column(verticalArrangement = Arrangement.spacedBy(16.dp)) {
                Button(
                    onClick = onDismissClick,
                    modifier = Modifier.fillMaxWidth().height(64.dp),
                ) { Text("关闭") }
                OutlinedButton(
                    onClick = onSnoozeClick,
                    modifier = Modifier.fillMaxWidth().height(64.dp),
                ) { Text("贪睡") }
            }
        }
    }
}
```

- [ ] **Step 4: 在 Manifest 声明全屏响铃 Activity**

```xml
<activity
    android:name=".alarm.AlarmRingingActivity"
    android:excludeFromRecents="true"
    android:exported="false"
    android:showWhenLocked="true"
    android:turnScreenOn="true"
    android:theme="@style/Theme.AlarmClock" />
```

- [ ] **Step 5: 做一次主链路验证**

Run: `./gradlew :app:assembleDebug`
Expected: `BUILD SUCCESSFUL`

- [ ] **Step 6: 提交响铃闭环**

```bash
git add app/src/main/java/com/example/alarmclock/alarm app/src/main/java/com/example/alarmclock/ui/ringing app/src/main/java/com/example/alarmclock/receiver/AlarmReceiver.kt app/src/main/AndroidManifest.xml
git commit -m "feat: 添加响铃页与关闭贪睡逻辑"
```

### Task 8: 接通应用初始化和恢复逻辑，完成首版收口

**Files:**
- Create: `app/src/main/java/com/example/alarmclock/AlarmClockApplication.kt`
- Modify: `app/src/main/java/com/example/alarmclock/ui/AlarmClockApp.kt`
- Modify: `app/src/main/AndroidManifest.xml`
- Modify: `app/src/main/java/com/example/alarmclock/data/repository/AlarmRepository.kt`

- [ ] **Step 1: 写应用级恢复入口**

```kotlin
class AlarmClockApplication : Application() {
    lateinit var repository: AlarmRepository
    lateinit var scheduler: AlarmScheduler
    lateinit var calculateNextTriggerUseCase: CalculateNextTriggerUseCase

    override fun onCreate() {
        super.onCreate()
        val database = Room.databaseBuilder(this, AlarmDatabase::class.java, "alarm.db").build()
        repository = RoomAlarmRepository(database.alarmDao())
        scheduler = AndroidAlarmScheduler(
            getSystemService(AlarmManager::class.java),
            AlarmIntentFactory(this),
        )
        calculateNextTriggerUseCase = CalculateNextTriggerUseCase()
    }

    fun restoreEnabledAlarms(now: ZonedDateTime = ZonedDateTime.now()) {
        CoroutineScope(SupervisorJob() + Dispatchers.IO).launch {
            repository.observeAlarms().first()
                .filter { it.enabled }
                .forEach { alarm ->
                    val next = calculateNextTriggerUseCase(alarm, now)
                    repository.upsert(alarm.copy(nextTriggerAt = next))
                    scheduler.schedule(alarm.id, next)
                }
        }
    }
}
```

- [ ] **Step 2: 给仓储补上启动恢复需要的方法**

```kotlin
interface AlarmRepository {
    fun observeAlarms(): Flow<List<Alarm>>
    suspend fun getAlarm(alarmId: Long): Alarm?
    suspend fun upsert(alarm: Alarm): Long
    suspend fun delete(alarmId: Long)
    suspend fun updateEnabled(alarmId: Long, enabled: Boolean)
}
```

- [ ] **Step 3: 将 `MainActivity` 和 `AlarmClockApp()` 改为从 `Application` 拿依赖**

```kotlin
data class AlarmDependencies(
    val repository: AlarmRepository,
    val scheduler: AlarmScheduler,
    val calculateNextTriggerUseCase: CalculateNextTriggerUseCase,
)

@Composable
fun rememberAlarmDependencies(): AlarmDependencies {
    val context = LocalContext.current.applicationContext as AlarmClockApplication
    return remember(context) {
        AlarmDependencies(
            repository = context.repository,
            scheduler = context.scheduler,
            calculateNextTriggerUseCase = context.calculateNextTriggerUseCase,
        )
    }
}
```

- [ ] **Step 4: 做一次完整单测与编译回归**

Run: `./gradlew :app:testDebugUnitTest :app:assembleDebug`
Expected: `BUILD SUCCESSFUL`

- [ ] **Step 5: 提交首版闭环**

```bash
git add app/src/main/java/com/example/alarmclock/AlarmClockApplication.kt app/src/main/java/com/example/alarmclock/ui/AlarmClockApp.kt app/src/main/java/com/example/alarmclock/data/repository/AlarmRepository.kt app/src/main/AndroidManifest.xml
git commit -m "feat: 接通首版闹钟初始化与恢复闭环"
```

## 自检

### Spec Coverage

- 首版功能范围：Task 5、Task 6、Task 7、Task 8 覆盖闹钟列表、新建编辑、响铃、贪睡、重启恢复
- 技术架构：Task 1、Task 2、Task 3、Task 4 覆盖 Compose、MVVM、Room、AlarmManager、Receiver
- 关键流程：Task 2 和 Task 4 至 Task 8 覆盖创建、调度、关闭、贪睡和恢复
- 非功能重点：Task 4、Task 7、Task 8 聚焦后台可靠性、锁屏响铃和恢复链路

### Placeholder Scan

- 未保留 `TBD`、`TODO` 或“后续补充”类占位
- 所有任务都给出明确文件、代码片段、命令和期望结果

### Type Consistency

- 统一使用 `Alarm`、`AlarmRepository`、`CalculateNextTriggerUseCase`、`AlarmScheduler`
- 响铃、编辑、列表、恢复均围绕同一 `Alarm` 数据结构推进

## 执行交接

Plan complete and saved to `docs/superpowers/plans/2026-10-29-android-alarm.md`. Two execution options:

**1. Subagent-Driven (recommended)** - I dispatch a fresh subagent per task, review between tasks, fast iteration

**2. Inline Execution** - Execute tasks in this session using executing-plans, batch execution with checkpoints

Which approach?
