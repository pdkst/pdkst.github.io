# Android 性能优化

性能优化是提升 Android 应用用户体验的关键。本文介绍常见的性能优化技巧。

## 1. 启动优化

### 应用启动时间分析

使用 `adb shell am start -W` 命令：

```bash
adb shell am start -W [PackageName]/[ActivityName]
```

关键指标：
- `ThisTime`: 最后一个 Activity 启动耗时
- `TotalTime`: 启动 Activity 总耗时
- `WaitTime`: 系统启动应用总耗时

### 优化策略

```kotlin
// Application 初始化优化
class MyApp : Application() {
    override fun onCreate() {
        super.onCreate()
        
        // 延迟初始化非必要组件
        Handler(Looper.getMainLooper()).postDelayed({
            initAnalytics()
            initPush()
        }, 2000)
        
        // 使用懒加载
        val sdk by lazy { SDKManager.init() }
    }
}

// Activity 启动优化
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        
        // 避免在 onCreate 中执行耗时操作
        lifecycleScope.launch {
            val data = loadDataInBackground()
            updateUI(data)
        }
    }
    
    private suspend fun loadDataInBackground(): Data =
        withContext(Dispatchers.IO) {
            // 在后台线程加载数据
        }
}
```

## 2. 布局优化

### 减少布局层级

```xml
<!-- 使用 ConstraintLayout 替代多层嵌套 -->
<androidx.constraintlayout.widget.ConstraintLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    
    <TextView
        android:id="@+id/title"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Hello World"
        app:layout_constraintTop_toTopOf="parent"
        app:layout_constraintStart_toStartOf="parent" />
        
</androidx.constraintlayout.widget.ConstraintLayout>
```

### 使用 ViewStub

```xml
<!-- 延迟加载布局 -->
<ViewStub
    android:id="@+id/loadingStub"
    android:layout="@layout/loading_layout"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

```kotlin
// 需要时才加载
val loadingView = loadingStub.inflate()
```

### 使用 include 和 merge

```xml
<!-- 使用 include 复用布局 -->
<include layout="@layout/common_header" />

<!-- 使用 merge 减少层级 -->
<merge xmlns:android="http://schemas.android.com/apk/res/android">
    <TextView ... />
    <Button ... />
</merge>
```

## 3. 内存优化

### 内存泄漏检测

使用 LeakCanary：

```kotlin
// build.gradle
dependencies {
    debugImplementation 'com.squareup.leakcanary:leakcanary-android:2.12'
}
```

### 避免内存泄漏

```kotlin
// 1. 静态变量持有 Context
// 错误 ❌
class MyManager {
    companion object {
        var context: Context? = null
    }
}

// 正确 ✅
class MyManager {
    companion object {
        @SuppressLint("StaticFieldLeak")
        lateinit var appContext: Context
    }
}

// 2. Handler 导致的泄漏
// 错误 ❌
inner class MyHandler : Handler() {
    override fun handleMessage(msg: Message) {
        // 处理消息
    }
}

// 正确 ✅
class MyHandler(context: Context) : Handler(Looper.getMainLooper()) {
    private val weakRef = WeakReference(context)
    
    override fun handleMessage(msg: Message) {
        weakRef.get()?.let {
            // 处理消息
        }
    }
}
```

### 优化 Bitmap

```kotlin
// 使用 Glide 或 Coil 加载图片
Glide.with(context)
    .load(url)
    .placeholder(R.drawable.placeholder)
    .override(targetWidth, targetHeight)
    .into(imageView)

// 手动优化
val options = BitmapFactory.Options().apply {
    inJustDecodeBounds = true
    BitmapFactory.decodeFile(path, options)
    
    inSampleSize = calculateInSampleSize(options, reqWidth, reqHeight)
    inJustDecodeBounds = false
}

fun calculateInSampleSize(
    options: BitmapFactory.Options,
    reqWidth: Int,
    reqHeight: Int
): Int {
    val (height, width) = options.outHeight to options.outWidth
    var inSampleSize = 1
    
    if (height > reqHeight || width > reqWidth) {
        val halfHeight = height / 2
        val halfWidth = width / 2
        
        while (halfHeight / inSampleSize >= reqHeight && 
               halfWidth / inSampleSize >= reqWidth) {
            inSampleSize *= 2
        }
    }
    
    return inSampleSize
}
```

## 4. 网络优化

### 使用 OkHttp

```kotlin
// 配置 OkHttp
val client = OkHttpClient.Builder()
    .connectTimeout(30, TimeUnit.SECONDS)
    .readTimeout(30, TimeUnit.SECONDS)
    .writeTimeout(30, TimeUnit.SECONDS)
    .cache(Cache(cacheDir, cacheSize))
    .addInterceptor(loggingInterceptor)
    .build()

// 请求复用和连接池
val connectionPool = ConnectionPool(5, 5, TimeUnit.MINUTES)
```

### 图片加载优化

```kotlin
// 使用 Coil（推荐）
Coil.imageLoader(context).enqueue(
    ImageRequest.Builder(context)
        .data(url)
        .crossfade(true)
        .memoryCachePolicy(CachePolicy.ENABLED)
        .diskCachePolicy(CachePolicy.ENABLED)
        .target(imageView)
        .build()
)
```

## 5. 电量优化

### 减少后台工作

```kotlin
// 使用 WorkManager 代替后台服务
val constraints = Constraints.Builder()
    .setRequiresDeviceIdle(true)
    .setRequiresCharging(true)
    .build()

val workRequest = OneTimeWorkRequestBuilder<SyncWorker>()
    .setConstraints(constraints)
    .build()
```

### 优化定位

```kotlin
// 使用 FusedLocationProviderClient
val locationRequest = LocationRequest.Builder(
    Priority.PRIORITY_BALANCED_POWER_ACCURACY,
    10000 // 10 秒
).apply {
    setMinUpdateIntervalMillis(5000) // 最小 5 秒
}.build()

locationClient.requestLocationUpdates(locationRequest, callback, Looper.getMainLooper())
```

## 6. 卡顿优化

### 使用 Systrace 和 Profiler

```kotlin
// 使用 Trace API 追踪性能
Trace.beginSection("loadData")
try {
    loadData()
} finally {
    Trace.endSection()
}
```

### 优化列表

```kotlin
// 使用 RecyclerView 代替 ListView
class MyAdapter : RecyclerView.Adapter<MyViewHolder>() {
    
    // 使用 DiffUtil 优化刷新
    fun updateItems(newItems: List<Item>) {
        val diffCallback = ItemDiffCallback(items, newItems)
        val diffResult = DiffUtil.calculateDiff(diffCallback)
        
        items.clear()
        items.addAll(newItems)
        
        diffResult.dispatchUpdatesTo(this)
    }
}

// 使用 ViewHolder 复用
class MyViewHolder(view: View) : RecyclerView.ViewHolder(view) {
    private val title: TextView = view.findViewById(R.id.title)
    private val image: ImageView = view.findViewById(R.id.image)
    
    fun bind(item: Item) {
        title.text = item.title
        Glide.with(image).load(item.imageUrl).into(image)
    }
}
```

## 性能监控工具

- **Systrace**: 分析系统级性能
- **Profiler**: 监控 CPU、内存、网络
- **Layout Inspector**: 检查布局
- **Perfetto**: 高级性能分析
- **LeakCanary**: 检测内存泄漏

## 参考资料

- [Android 性能最佳实践](https://developer.android.com/topic/performance)
- [Vitals 性能指标](https://developer.android.com/topic/performance/vitals)

---

*最后更新时间: {docsify-updated}*
