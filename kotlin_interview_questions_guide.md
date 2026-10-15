# Top 20 Frequently Asked Kotlin Interview Questions

This comprehensive guide covers the 20 most frequent Kotlin interview questions, structured across fundamentals, scope functions, advanced features, and coroutines, complete with code examples.

---

## Part 1: Fundamentals & Null Safety

### 1. What is Kotlin, and what are its core advantages over Java?
* **Concept:** Kotlin is a modern, JVM-compatible language designed to eliminate boilerplate code and prevent common errors like NullPointerExceptions.
* **Code Example:**
```kotlin
// Concise syntax with property inference and string templates
val name = "Kotlin"
val greeting = "Hello, $name!"
```

### 2. What is the difference between `val` and `var`?
* **Concept:** `val` is read-only (immutable reference, equivalent to `final` in Java), whereas `var` is mutable and can be reassigned.
* **Code Example:**
```kotlin
val immutablePi = 3.14159 // Cannot be changed
var score = 0            // Can be reassigned
score = 10               // Valid
```

### 3. How does Kotlin achieve null safety? Explain safe calls and the Elvis operator.
* **Concept:** Kotlin's type system distinguishes nullable (`?`) and non-nullable types at compile time.
* **Code Example:**
```kotlin
val name: String? = null
// Safe call returns null if name is null, Elvis operator provides a fallback value
val length: Int = name?.length ?: 0 
```

### 4. What is the difference between structural equality (`==`) and referential equality (`===`)?
* **Concept:** `==` checks if contents/values are equal (calls `.equals()`), while `===` checks if both references point to the exact same object in memory.
* **Code Example:**
```kotlin
val list1 = listOf(1, 2, 3)
val list2 = listOf(1, 2, 3)

println(list1 == list2)  // true (structural equality)
println(list1 === list2) // false (different object instances in memory)
```

### 5. How does the `when` expression work, and how does it replace the Java `switch` statement?
* **Concept:** `when` is a powerful, exhaustive conditional expression that doesn't require explicit `break` statements.
* **Code Example:**
```kotlin
val day = 2
val result = when (day) {
    1 -> "Monday"
    2, 3 -> "Tuesday or Wednesday"
    in 4..5 -> "Thursday or Friday"
    else -> "Weekend"
}
```

---

## Part 2: Scope Functions & Idiomatic Kotlin

### 6. What are scope functions, and how do they differ in object reference and return value?
* **Concept:** Scope functions execute a block of code within the context of an object. They differ by whether they reference the object as `this` or `it`, and whether they return the context object or the lambda result.

| Function | Object Reference | Return Value | Common Use Case |
| :--- | :--- | :--- | :--- |
| **`let`** | `it` | Lambda result | Null checks (`?.let`), transforming data |
| **`run`** | `this` | Lambda result | Object configuration + computation |
| **`with`** | `this` | Lambda result | Grouping calls on a single object without null safety |
| **`apply`** | `this` | Context object (`this`) | Object configuration / Builder pattern |
| **`also`** | `it` | Context object (`this`) | Logging, debugging, side effects |

### 7. How is `let` used for null safety and data transformation?
* **Concept:** Combined with safe calls (`?.let`), it executes code blocks only when an object is not null.
* **Code Example:**
```kotlin
val email: String? = "test@kotlin.org"

email?.let { 
    // 'it' is non-null String here
    println("Sending email to $it") 
}
```

### 8. What is the difference between `apply` and `also`?
* **Concept:** Both return the context object itself, but `apply` accesses the object via `this` (ideal for configuration/builders), while `also` uses `it` (ideal for side effects like logging).
* **Code Example:**
```kotlin
// apply uses 'this' for object configuration
val button = Button(context).apply {
    text = "Submit"
    isEnabled = true
}

// also uses 'it' for auxiliary logging/actions
val numbers = mutableListOf(1, 2, 3).also {
    println("Created list with elements: $it")
}
```

### 9. When should you use `run` versus `with`?
* **Concept:** Both access the object via `this` and return the lambda result. `run` is called as an extension function on an object, whereas `with` takes the object as an argument.
* **Code Example:**
```kotlin
val text = "Hello"
val uppercaseLength = with(text) {
    println(this)
    length
}
```

### 10. What are extension functions, and how do they work under the hood?
* **Concept:** They allow you to add new functions to existing classes without inheritance. Under the hood, the compiler compiles them into static utility methods where the receiver object is passed as the first parameter.
* **Code Example:**
```kotlin
// Adding a function to String class
fun String.isLong(): Boolean = this.length > 10

val check = "Hello World!".isLong() // true
```

---

## Part 3: Object-Oriented & Advanced Features

### 11. What are data classes, and what methods are automatically generated?
* **Concept:** Classes designed specifically to hold state. The compiler automatically generates `equals()`, `hashCode()`, `toString()`, `copy()`, and `componentN()` functions.
* **Code Example:**
```kotlin
data class User(val name: String, val age: Int)

val user1 = User("Alice", 25)
val user2 = user1.copy(age = 26) // Using auto-generated copy()
```

### 12. What is the difference between a `sealed class` and an `enum`?
* **Concept:** Enums represent a fixed set of constant values. Sealed classes represent restricted class hierarchies where subclasses can have multiple distinct instances with their own unique states.
* **Code Example:**
```kotlin
sealed class Result {
    data class Success(val data: String) : Result()
    data class Error(val exception: Throwable) : Result()
    object Loading : Result()
}
```

### 13. What is the difference between `lateinit` and `lazy` initialization?
* **Concept:** `lateinit` is for mutable (`var`) properties initialized later (e.g., dependency injection). `lazy` is for read-only (`val`) properties initialized on first access using a lambda block.
* **Code Example:**
```kotlin
// lateinit for mutable vars
lateinit var database: Database 

// lazy for read-only vals
val heavyConfiguration: Config by lazy {
    Config().loadFromDisk()
}
```

### 14. What are inline functions, and why are they used?
* **Concept:** Marking a function as `inline` tells the compiler to copy the function's body directly to the call site, eliminating the memory and performance overhead of lambda object allocations.
* **Code Example:**
```kotlin
inline fun measureTime(block: () -> Unit) {
    val start = System.currentTimeMillis()
    block()
    println("Time taken: ${System.currentTimeMillis() - start}ms")
}
```

### 15. What are reified type parameters?
* **Concept:** Normally, generic types suffer from type erasure at runtime in the JVM. By combining `inline` with `reified`, Kotlin allows you to check or access the exact generic type at runtime.
* **Code Example:**
```kotlin
inline fun <reified T> printIfInstance(value: Any) {
    if (value is T) {
        println("Value matches type: $value")
    }
}
```

---

## Part 4: Asynchronous Programming & Coroutines

### 16. What are Kotlin Coroutines, and why are they preferred over standard threads?
* **Concept:** Coroutines are "lightweight threads" used for non-blocking asynchronous programming. Multiple coroutines can multiplex on a tiny thread pool, avoiding the massive memory overhead of OS threads.
* **Code Example:**
```kotlin
// Spawning a lightweight coroutine
GlobalScope.launch {
    delay(1000L) // Non-blocking delay
    println("Coroutines rock!")
}
```

### 17. What does the `suspend` keyword do?
* **Concept:** It marks a function that can be paused and resumed later without blocking the underlying thread. The compiler transforms it into a state machine using a continuation-passing style.
* **Code Example:**
```kotlin
suspend fun fetchUserData(): User {
    // Network call that pauses execution without blocking the thread
    return apiService.getUser() 
}
```

### 18. What is the difference between `launch` and `async` coroutine builders?
* **Concept:** `launch` is a fire-and-forget builder returning a `Job`. `async` is used when you need a computed result returned later, returning a `Deferred<T>` that you consume via `.await()`.
* **Code Example:**
```kotlin
suspend fun loadDashboard() = coroutineScope {
    val deferredData = async { fetchRemoteData() }
    val jobLog = launch { logAnalytics() }
    
    val data = deferredData.await() // Retrieve result from async
}
```

### 19. What is structured concurrency?
* **Concept:** Structured concurrency guarantees that every coroutine is bound to a specific lifecycle scope. If a parent scope is cancelled, all child coroutines are automatically cleaned up, preventing memory leaks and orphaned background tasks.
* **Code Example:**
```kotlin
class MyViewModel : ViewModel() {
    // viewModelScope ensures automatic cancellation when the ViewModel is cleared
    fun loadData() {
        viewModelScope.launch {
            val result = fetchUserData()
        }
    }
}
```

### 20. What is the difference between `StateFlow` and `SharedFlow`?
* **Concept:** `StateFlow` is a specialized hot flow designed to hold state; it always retains and emits the *latest value* to new subscribers and requires an initial value. `SharedFlow` is a broader emission stream that does not necessarily maintain state or require an initial value.
* **Code Example:**
```kotlin
// StateFlow requires an initial value and replays the latest state
private val _uiState = MutableStateFlow<UiState>(UiState.Loading)
val uiState: StateFlow<UiState> = _uiState.asStateFlow()
```