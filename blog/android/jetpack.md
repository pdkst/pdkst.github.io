# Jetpack 组件详解

Jetpack 是 Android 官方推出的现代化开发组件库，旨在帮助开发者构建高质量的 Android 应用。

## 核心组件

### 1. ViewModel

ViewModel 用于管理 UI 相关的数据，在配置更改（如屏幕旋转）时自动保存数据。

```kotlin
class UserViewModel : ViewModel() {
    private val _users = MutableLiveData<List<User>>()
    val users: LiveData<List<User>> = _users
    
    fun loadUsers() {
        viewModelScope.launch {
            val result = repository.getUsers()
            _users.value = result
        }
    }
}

// 在 Activity/Fragment 中使用
viewModel.users.observe(this) { users ->
    // 更新 UI
}
```

### 2. LiveData

LiveData 是可观察的数据持有者，自动感知生命周期。

```kotlin
class MyViewModel : ViewModel() {
    private val _data = MutableLiveData<String>()
    val data: LiveData<String> = _data
    
    fun updateData(newData: String) {
        _data.value = newData
    }
}

// 使用 map 和 switchMap 转换数据
val userName: LiveData<String> = user.map { it.name }
val userDetails: LiveData<UserDetails> = userId.switchMap { id ->
    repository.getUserDetails(id)
}
```

### 3. Room

Room 是 SQLite 的抽象层，提供类型安全的数据库访问。

```kotlin
// 实体类
@Entity(tableName = "users")
data class User(
    @PrimaryKey(autoGenerate = true)
    val id: Long = 0,
    val name: String,
    val email: String
)

// DAO
@Dao
interface UserDao {
    @Query("SELECT * FROM users")
    fun getAll(): LiveData<List<User>>
    
    @Query("SELECT * FROM users WHERE id = :id")
    fun getById(id: Long): User?
    
    @Insert
    fun insert(user: User): Long
    
    @Update
    fun update(user: User)
    
    @Delete
    fun delete(user: User)
}

// 数据库
@Database(entities = [User::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun userDao(): UserDao
    
    companion object {
        private const val DATABASE_NAME = "app_database"
    }
}

// 使用
val db = Room.databaseBuilder(
    context,
    AppDatabase::class.java,
    DATABASE_NAME
).build()
```

### 4. Navigation

Navigation 组件简化了应用内的导航。

```kotlin
// 导航图
<navigation xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    app:startDestination="@id/homeFragment">
    
    <fragment
        android:id="@+id/homeFragment"
        android:name="com.example.HomeFragment">
        <action
            android:id="@+id/action_home_to_detail"
            app:destination="@id/detailFragment" />
    </fragment>
    
    <fragment
        android:id="@+id/detailFragment"
        android:name="com.example.DetailFragment">
        <argument
            android:name="itemId"
            android:defaultValue="-1" />
    </fragment>
</navigation>

// 导航
findNavController().navigate(R.id.action_home_to_detail)
```

### 5. WorkManager

WorkManager 管理后台任务，确保任务执行。

```kotlin
// 定义任务
class UploadWorker(context: Context, params: WorkerParameters) : Worker(context, params) {
    override fun doWork(): Result {
        // 执行任务
        return Result.success()
    }
}

// 创建并执行任务
val uploadRequest = OneTimeWorkRequestBuilder<UploadWorker>()
    .setConstraints(
        Constraints.Builder()
            .setRequiredNetworkType(NetworkType.CONNECTED)
            .setRequiresBatteryNotLow(true)
            .build()
    )
    .build()

WorkManager.getInstance(context).enqueue(uploadRequest)

// 周期性任务
val periodicRequest = PeriodicWorkRequestBuilder<SyncWorker>(
    24, TimeUnit.HOURS
).build()
```

### 6. DataStore

DataStore 是 SharedPreferences 的替代品。

```kotlin
// 定义数据类
data class UserPreferences(
    val showCompleted: Boolean = false,
    val sortOrder: String = "ASC"
)

// 创建 DataStore
val Context.dataStore by preferencesDataStore(name = "settings")

// 读取数据
val showCompleted: Flow<Boolean> = context.dataStore.data
    .map { preferences ->
        preferences[SHOW_COMPLETED_KEY] ?: false
    }

// 写入数据
suspend fun saveShowCompleted(showCompleted: Boolean) {
    context.dataStore.edit { preferences ->
        preferences[SHOW_COMPLETED_KEY] = showCompleted
    }
}
```

## 架构模式

### MVVM 架构

```
┌─────────────────────────────────────┐
│             UI Layer                │
│  (Activity, Fragment, Compose)      │
└──────────────┬──────────────────────┘
               │ observes
┌──────────────▼──────────────────────┐
│          ViewModel                  │
└──────────────┬──────────────────────┘
               │ calls
┌──────────────▼──────────────────────┐
│          Repository                 │
└──────────────┬──────────────────────┘
               │ uses
    ┌──────────┴──────────┐
    │                     │
┌───▼────┐          ┌────▼───┐
│  API   │          │ Database│
└────────┘          └─────────┘
```

### 示例实现

```kotlin
// ViewModel
class ProductViewModel(
    private val repository: ProductRepository
) : ViewModel() {
    
    private val _products = MutableLiveData<List<Product>>()
    val products: LiveData<List<Product>> = _products
    
    fun loadProducts() {
        viewModelScope.launch {
            try {
                val result = repository.getProducts()
                _products.value = result
            } catch (e: Exception) {
                // 处理错误
            }
        }
    }
}

// Repository
class ProductRepository(
    private val apiService: ApiService,
    private val database: AppDatabase
) {
    suspend fun getProducts(): List<Product> {
        // 先从本地数据库获取
        val cached = database.productDao().getAll()
        if (cached.isNotEmpty()) {
            return cached
        }
        
        // 从网络获取并缓存
        val products = apiService.getProducts()
        database.productDao().insertAll(products)
        return products
    }
}
```

## 最佳实践

1. **使用 ViewModel** 分离 UI 逻辑和业务逻辑
2. **通过 LiveData** 或 **Flow** 传递数据
3. **使用 Repository** 模式管理数据源
4. **使用 Hilt** 进行依赖注入
5. **遵循单一数据源原则**
6. **避免内存泄漏**，正确使用生命周期

## 参考资料

- [Jetpack 官方文档](https://developer.android.com/jetpack)
- [Android 架构指南](https://developer.android.com/topic/architecture)

---

*最后更新时间: {docsify-updated}*
