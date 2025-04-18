# Arc Funpay API

This project provides an implementation of the **Funpay API** in Kotlin, enabling seamless interaction with Funpay's marketplace. It includes authentication, order management, lot raising, event handling, and modular extensions.

## 📌 Features
- ✅ **Authentication** via API token
- 📊 **Retrieve account information** (balance, ID, name)
- 📦 **Fetch order statistics** (seller & buyer orders)
- 🔼 **Raise lots** in specific game categories
- ⚡ **Event-driven system** (handle Funpay events such as new orders, purchases, and lot raises)
- 🔌 **Modular architecture** (extend functionality with custom modules)

## 🛠️ Installation

1. Configure dependencies by adding the following Maven repository to `build.gradle.kts`:
   ```kotlin
   repositories {
       mavenCentral()
       maven {
           name = "reposiliteRepositoryReleases"
           url = uri("http://89.39.121.106:8080/releases")
           isAllowInsecureProtocol = true
       }
   }
   ```  
2. Add the dependency:
   ```kotlin
   implementation("arc:funpay:1.5.1")
   ```  


## 🚀 Usage

### 🔹 Basic API Usage
The main entry point is `arc.examples.MainKt`. Below is an example demonstrating interaction with the Funpay API:

```kotlin
suspend fun main() {
    val configFile = local("config.yml").readText()
    val config = Yaml.default.decodeFromString<Config>(configFile)
    
    val app = FunpayApplication(config.token) // Golden key in cookies

    val api = app.koin.get<FunpayAPI>()
    val accountInfo = api.getInfo()

    println(
        """Account info:
        - Balance: ${accountInfo.balance.amount}
        - Currency: ${accountInfo.balance.currency}
        - ID: ${accountInfo.id}
        - Name: ${accountInfo.name}""".trimIndent()
    )

    app.start()
}
```

### 🔹 Fetching Order Information
```kotlin
val orderCount = api.getOrders()
println(
    """Orders:
    - As Seller: ${orderCount.seller}
    - As Buyer: ${orderCount.buyer}""".trimIndent()
)
```

### 🔹 Sending Chat messages and Getting user by name
```kotlin
val chatId = api.getChatNodeByUsername("JIeT") ?: "0"
println("Chat ID: $chatId")
api.sendMessage(chatId, "Hello from Arc!")
```

### 🔹 Raising Lots on Funpay
```kotlin
val lots = api.raiseLots("41", "504") // Example: Dota 2 - Other
println("Lots raised: ${lots.success}, Message: ${lots.msg}")
```

---

## 📜 Arc Examples Module

The project includes a separate `arc.examples` module, providing ready-to-use **examples** for interacting with the Funpay API.

### 🔹 Example Module: `LotsRaiseModule`
This module automates the process of raising lots in specific categories.

```kotlin
app.addModule(LotsRaiseModule())
```

Parse automatically the lots 
Starting by default, this is just example


### 🔹 Handling Events
This API supports an **event-driven system**, allowing you to listen to various Funpay actions.

```kotlin
    app.eventBus.on<PreLotsRaiseEvent> {
   println("PreLotsRaiseEvent: ${it.category.name} -> ${it.category.nextCheck}")
   // it.isCancelled = true // Can be cancelled if event is Pre
}

app.eventBus.on<LotsRaiseEvent> {
   println("LotsRaiseEvent: ${it.category.name} -> ${it.message}")
}

app.eventBus.on<NewOrderEvent> {
   println("NewOrderEvent: ${it.order}")
}

app.eventBus.on<OrderCloseEvent> {
   println("OrderCloseEvent: ${it.order}")
}

app.eventBus.on<NewPurchaseEvent> {
   println("NewPurchaseEvent: ${it.oldCount} -> ${it.newCount}")
}

app.eventBus.on<NewChatEvent> {
   println("NewChatEvent: ${it.nodeId} -> ${it.userName}")
}

app.eventBus.on<NewMessageEvent> {
   println("NewMessageEvent: ${it.userName} -> ${it.message}")
}

app.eventBus.on<NewReviewEvent> {
   println("NewReviewEvent: ${it.userId} -> ${it.text}")
}
```

---

## 📝 License

This project is open-source for **personal** and **non-commercial** use.

### ⚠️ Commercial Use
For any **commercial** use, explicit permission from the project owner is required.

---

🚀 Happy coding!
