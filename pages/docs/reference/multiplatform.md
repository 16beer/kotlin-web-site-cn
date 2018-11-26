---
type: doc
layout: reference
category: "Other"
title: "多平台程序设计"
---

# 多平台程序设计

> 多平台项目是 Kotlin 1.2 与 1.3 中的实验性特性。本文档中描述的所有的语言<!--
-->与工具特性在未来的版本中都可能会发生变化。
{:.note}

在所有平台上都能用是 Kotlin 的一个明确目标，但我们将其视为一个更重要的目标<!--
-->——在多个平台之间共享代码的前提。有了对 JVM、Android、JavaScript、iOS、Linux、Windows、
Mac 甚至像 STM32 这样的嵌入式系统的支持，Kotlin 可以处理现代应用程序的任何组件与所有组件。
这为代码与专业知识的复用带来了宝贵的收益，节省了工作量去完成更具<!--
-->挑战任务，而不是将所有东西都实现两次或多次。

## 它是如何工作的

总得来说，多平台并不是为所有平台编译全部代码。这个模型有其明显的<!--
-->局限性，我们知道现代应用程序需要访问<!--
-->其所运行平台的独有特性。Kotlin 并不会限制你只使用其中所有 API 的公共子集。
每个组件都可以根据需要与其他组件共享尽可能多的代码，
而通过语言所提供的 [`expect`/`actual` 机制](platform-specific-declarations.html)可以随时访问平台 API。

以下是在极简版日志框架中公共逻辑与平台逻辑之间代码共享与交互的<!--
-->示例。其公共代码如下所示：




```kotlin
// 为所有平台编译
enum class LogLevel {
    DEBUG, WARN, ERROR
}

// 预期的平台相关 API
internal expect fun writeLogMessage(message: String, logLevel: LogLevel)

// 可在公共代码中使用预期的 API
fun logDebug(message: String) = writeLogMessage(message, LogLevel.DEBUG)
fun logWarn(message: String) = writeLogMessage(message, LogLevel.WARN)
fun logError(message: String) = writeLogMessage(message, LogLevel.ERROR)
```



这段代码需要平台提供 `writeLogMessage` 的实现，而通用代码<!--
-->增加这个声明之后可以不用考虑具体的平台实现，就可以直接使用。

在JVM中我们可以提供一个将日志写入标准输出中的实现：



```kotlin
internal actual fun writeLogMessage(message: String, logLevel: LogLevel) {
    println("[$logLevel]: $message")
}
```



在 JavaScript 中，会有一套完全不一样的APIs，
我们可以为它提供另一个日志输出的实现：



```kotlin
internal actual fun writeLogMessage(message: String, logLevel: LogLevel) {
    when (logLevel) {
        LogLevel.DEBUG -> console.log(message)
        LogLevel.WARN -> console.warn(message)
        LogLevel.ERROR -> console.error(message)
    }
}
```



在1.3中我们重构了整个多平台模型。 [new DSL](building-mpp-with-gradle.html) 使构建<!--
-->多平台的 Gradle 项目更加灵活，我们还会持续推进让项目配置更加<!--
-->简洁。

## 多平台库

通用的代码可以依赖一些已经实现的比较常用的库比如 [HTTP](https://ktor.kotlincn.net/clients/http-client/multiplatform.html)， [serialization](https://github.com/Kotlin/kotlinx.serialization)， 和 [managing
coroutines](https://github.com/Kotlin/kotlinx.coroutines)。并且，我们还有一个针对全平台的扩展通用库。

当然你也可以写一个包含通用API和不同平台实现的库。

## 使用范例

### Android — iOS

在不同手机平台上共享代码是 Kotlin 多平台的重要内容，现在你
在构建很多的项目时，将业务逻辑、网络连接
等代码在Android和IOS中间共享将成为可能。

见: [Multiplatform Project: iOS and Android](https://www.kotlincn.net/docs/tutorials/native/mpp-ios-android.html)

### 客户端 — 服务端

另一种情况代码共享也会带来好处，在互联系统中部分逻辑代码可以
同时运行在服务端和浏览器的客户端上。这也在 Kotlin
的多平台范畴内。

[Ktor framework](https://ktor.io/) 是一个很适合构建服务器和客户端异步通讯系统的框架。

The [Ktor framework](https://ktor.io/) is suitable for building asynchronous servers and clients in connected systems.

## 如何开始

<div style="display: flex; align-items: center; margin-bottom: 20px">
    <img src="/assets/images/landing/native/book.png" height="38p" width="55" style="margin-right: 10px;">
    <b>Tutorials and Documentation</b>
</div>

还不熟悉 Kotlin ? 看一下这个 [Getting Started](basic-syntax.html) 。

参考文档：
- [Setting up a Multiplatform Project](building-mpp-with-gradle.html#setting-up-a-multiplatform-project)
- [Platform-Specific Declarations](platform-specific-declarations.html)

推荐教程：
- [Multiplatform Kotlin Library](https://www.kotlincn.net/docs/tutorials/multiplatform-library.html)
- [Multiplatform Project: iOS and Android](https://www.kotlincn.net/docs/tutorials/native/mpp-ios-android.html)

<div style="display: flex; align-items: center; margin-bottom: 10px;">
    <img src="/assets/images/landing/native/try.png" height="38p" width="55" style="margin-right: 10px;">
    <b>Example Projects</b>
</div>

- [KotlinConf app](https://github.com/JetBrains/kotlinconf-app)
- [KotlinConf Spinner app](https://github.com/jetbrains/kotlinconf-spinner)

更多的例子请参考 [GitHub](https://github.com/JetBrains/kotlin-examples)
