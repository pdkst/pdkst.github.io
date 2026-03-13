# Kotlin 基础入门

Kotlin 是一种在 Java 虚拟机上运行的静态类型编程语言，被 Google 官方推荐用于 Android 开发。

## 特性概览

### 1. 简洁性

```kotlin
// 数据类自动生成 equals(), hashCode(), toString()
data class User(val name: String, val age: Int)

// 单表达式函数
fun sum(a: Int, b: Int) = a + b
```

### 2. 空安全

```kotlin
var name: String? = null  // 可空类型
val length = name?.length  // 安全调用操作符
val length2 = name?.length ?: 0  // Elvis 操作符
```

### 3. 扩展函数

```kotlin
// 为 String 添加扩展函数
fun String.isValidEmail(): Boolean {
    return this.contains("@")
}

val email = "test@example.com"
if (email.isValidEmail()) {
    println("Valid email")
}
```

### 4. 高阶函数和 Lambda

```kotlin
// 使用 Lambda
val list = listOf(1, 2, 3, 4, 5)
val doubled = list.map { it * 2 }
val even = list.filter { it % 2 == 0 }
```

## 基础语法

### 变量声明

```kotlin
val readOnly: Int = 10  // 不可变变量
var mutable: Int = 20   // 可变变量

// 类型推断
val name = "pdkst"      // 自动推断为 String
```

### 条件表达式

```kotlin
val max = if (a > b) a else b

// when 表达式
when (x) {
    1 -> print("x == 1")
    2 -> print("x == 2")
    else -> print("x is neither 1 nor 2")
}
```

### 循环

```kotlin
// for 循环
for (item in items) {
    println(item)
}

// 范围遍历
for (i in 1..5) {
    print(i)
}

// 步长遍历
for (i in 1..10 step 2) {
    print(i)
}
```

## 类和对象

### 类定义

```kotlin
class Person(val name: String, var age: Int) {
    fun introduce() {
        println("Hello, I'm $name, $age years old")
    }
}

// 使用
val person = Person("pdkst", 25)
person.introduce()
```

### 继承

```kotlin
open class Animal(val name: String) {
    open fun makeSound() {
        println("$name makes a sound")
    }
}

class Dog(name: String) : Animal(name) {
    override fun makeSound() {
        println("$name barks")
    }
}
```

## 集合操作

```kotlin
val numbers = listOf(1, 2, 3, 4, 5)

// map 转换
val squares = numbers.map { it * 2 }

// filter 过滤
val evenNumbers = numbers.filter { it % 2 == 0 }

// reduce 聚合
val sum = numbers.reduce { acc, num -> acc + num }

// 排序
val sorted = numbers.sorted()
val reversed = numbers.sortedDescending()
```

## 总结

Kotlin 的特性使得 Android 开发更加高效和安全。建议从 Kotlin 官方文档开始学习，并在实际项目中逐步应用。

## 参考资料

- [Kotlin 官方文档](https://kotlinlang.org/docs/)
- [Android Kotlin 指南](https://developer.android.com/kotlin)

---

*最后更新时间: {docsify-updated}*
