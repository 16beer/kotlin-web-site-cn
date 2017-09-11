---
type: doc
layout: reference
category: "Classes and Objects"
title: "数据类"
---

# 数据类

我们经常创建一些只保存数据的类。在这些类中，一些标准函数往往是从<!--
-->数据机械推导而来的。在 Kotlin 中，这叫做 _数据类_ 并标记为 `data`：

``` kotlin
data class User(val name: String, val age: Int)
```

编译器自动从主构造函数中声明的所有属性导出以下成员：

  * `equals()`/`hashCode()` 对，
  * `toString()` 格式是 `"User(name=John, age=42)"`，
  * [`componentN()` 函数](multi-declarations.html) 按声明顺序对应于所有属性，
  * `copy()` 函数（见下文）。

为了确保生成的代码的一致性和有意义的行为，数据类必须满足以下要求：

  * 主构造函数需要至少有一个参数；
  * 主构造函数的所有参数需要标记为 `val` 或 `var`；
  * 数据类不能是抽象、开放、密封或者内部的；
  * （在1.1之前）数据类只能实现接口。

Additionally, the members generation follows these rules with regard to the members inheritance:

* If there are explicit implementations of `equals()`, `hashCode()` or `toString()` in the data class body or 
*final*{: .keyword } implementations in a superclass, then these functions are not generated, and the existing 
implementations are used;
* If a supertype has the `componentN()` functions that are *open*{: .keyword } and return compatible types, the 
corresponding functions are generated for the data class and override those of the supertype. If the functions of the 
supertype cannot be overridden due to incompatible signatures or being final, an error is reported; 
* Providing explicit implementations for the `componentN()` and `copy()` functions is not allowed.

自 1.1 起，数据类可以扩展其他类（示例请参见[密封类](sealed-classes.html)）。

在 JVM 中，如果生成的类需要含有一个无参的构造函数，则所有的属性必须指定默认值。
（参见[构造函数](classes.html#构造函数)）。

``` kotlin
data class User(val name: String = "", val age: Int = 0)
```

## 复制

在很多情况下，我们需要复制一个对象改变它的一些属性，但其余部分保持不变。
`copy()` 函数就是为此而生成。对于上文的 `User` 类，其实现会类似下面这样：

``` kotlin
fun copy(name: String = this.name, age: Int = this.age) = User(name, age)     
```

这让我们可以写

``` kotlin
val jack = User(name = "Jack", age = 1)
val olderJack = jack.copy(age = 2)
```

## 数据类和解构声明

为数据类生成的 _Component 函数_ 使它们可在[解构声明](multi-declarations.html)中使用：

``` kotlin
val jane = User("Jane", 35)
val (name, age) = jane
println("$name, $age years of age") // 输出 "Jane, 35 years of age"
```

## 标准数据类

标准库提供了 `Pair` 和 `Triple`。尽管在很多情况下命名数据类是更好的设计选择，
因为它们通过为属性提供有意义的名称使代码更具可读性。
