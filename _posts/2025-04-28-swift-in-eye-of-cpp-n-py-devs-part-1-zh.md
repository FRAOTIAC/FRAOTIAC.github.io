# Swift in Eye of C++ and Python Devs · Part 1

## Swift 给 C++/Python 开发者的快速迁移指南

## 👋 引子

作为一个长期的 C++ 开发者，偶尔会用 Python 进行训练和部署，其实我一直以为自己对编程语言的了解算是蛮深的。

C++ 是公认很难的语言，对我来说，有时候确实是这样。但在多年与这样一门复杂且接近底层的语言打交道之后，我也相应积累了很多计算机系统知识，比如寄存器、缓存、实时系统等优化技巧。而且在现代 C++ 的加持下，写多线程变得更加简单，各种 STL 容器与算法也让日常使用更顺手了。

Python 则是另一种体验。在阅读一些复杂的 Python 项目，尤其是公司内部自研的项目时，最让我头疼的是——**不知道函数到底接受和返回什么类型**。（虽然 Python 有类型注解，但在自研项目中，算法人员往往不会太在意工程规范。）

Python 的设计哲学很奇特：表面上是弱类型，实际上是强类型（运行时类型检查）。在学习 Python 的早期，由于战术上的轻视，我走了不少弯路，直到后来接触到 pydantic，情况才好转。

而 Swift，作为号称“现代的编程语言”，一开始我也轻视了它（好像找到一个模式了😂）。毕竟 Python 语法糖丰富，Modern C++ 也打着 modern 的旗号，Swift又能复杂到哪里去呢？

这篇文章就是写给像我这样，有一定 C++ / Python 背景，并且遇到了契机想学习 Swift 的开发者。

我希望用 cpp / Python 的视角，聊聊 Swift 的学习体验和坑点，**So let's have fun!**

---

## Swift ≠ SwiftUI

在正式开始之前，我想澄清一个常见的误区：Swift 和 SwiftUI 是两个不同的东西。

- **Swift**：一门编程语言。
- **SwiftUI**：一个声明式 UI 框架，用 Swift 编写界面和交互。

当你开始学习 Swift 时，很多教程会让你打开 Xcode 的 Playground。

在 Playground 中，Swift 的体验非常接近 Python：可以按行或按块执行代码，像是 Jupyter Notebook 那样轻快。无需像 C++ 那样手动编译链接，而是可以直接运行。

所以 Swift 是不是像 Python 一样的 JIT（Just-In-Time）语言呢？

其实不是。Swift 是 **AOT（Ahead-Of-Time）** 编译语言，只不过 Xcode 帮你自动化了构建和运行流程，体验上像极了脚本语言。

这样的设计有充分的理由：在 iOS 设备上运行 App，若采用 JIT，性能和资源消耗都会成为大问题。而 AOT 编译的 Swift，既保证了 App 的流畅体验，也符合苹果一直以来对性能的追求。（当然，从商业角度来看，JIT有助于苹果“卖金子内存”XD）

---

## 🤔 小小的警告

虽然 Swift 常被描述为“现代”、“安全”的语言，但从一个 C++ 开发者的角度来看，我认为它有很多地方是需要批判性思考的。

尤其是在类型系统、错误处理、Optionals、Protocol 等设计上，网上充斥着过于正面的描述，但实际上有些设计也是妥协的产物。

这些，我们都会在后续慢慢聊到。

# 📝 Ready? Let’s dive in.

---

## 基本语法


1. 定义变量
    
    如何定义变量？Swift的方式是只有两个选择，`let`和`var`，`let`定义值不可改变的语言，而`var`则定义变量。
    
    emm，但是Swift同样是强类型语言，所以你最好记住，或者在函数中写清楚你所定义的值是什么类型，否则估计也会陷入到Python类似的类型迷宫。
    
    那么怎么理解`let`呢，从C++的角度上来说，`let`可能是`const`变量，比如`const int`
    
    ```swift
    // swift
    let s = "Hello, World"
    var strVar = "Variable Hello, World"
    // cpp
    const std::string s {"Hello, World"}
    std::string str_var {"Variable Hello, World"}
    
    # python
    s = "Hello, World"
    # Python有const变量吗？
    # 它本身没有const关键字。通常开发者约定：大写字母变量视为常量（比如 `PI = 3.1415`），
    # 从Python 3.8起，可以使用 `typing.final` 来标记不可变，但只是静态检查，不是强制限制。
    ```
    
    而`let，var`这样的类型推导标识，让我想起了cpp的`auto`，经常写`cpp`的人知道，面对`auto`的接受度，cpp开发群体的意见是不一致的，比如我们就有这样的一个`auto` meme：https://www.reddit.com/r/ProgrammerHumor/comments/sp792s/this_is_how_i_see_modern_c/，当然我自己是会倾向于写`auto`的，但是在另一些情况下，最好清楚声明变量定义。
2. 函数定义
    1. 无参函数
        
        ```Swift
        // Swift function
        func printInfo() {
            print("Welcome!")
        }
        
        // cpp function
        #include <iostream>
        void PrintInfo() {
        	 std::cout << "Welcome!" << std::endl;
        }
        ```
        
        可以看到基本上是相同的，Swift在这里有几个不同的地方
        
        - Swift 需要关键字`func`来定义函数
        - Swift 无需包含头文件，`print`是内建函数
    2. 有参函数
        
        ```swift
        // swift
        func printTimesTables(number: Int, end: Int) {
            // ...
        }
        printTimesTables(number: 5, end: 20)
        
        // cpp
        void PrintTimesTables(int number, int end) {
            // ...
        }
        PrintTimesTables(5, 20);  
        
        // python
        
        ```
        
        可以看到，在函数参数这里，Swift的写法与cpp是不同的，首先是变量定义，紧跟冒号，最后是类型定义，而在调用的时候，Swift需要指定参数名称，然后才可以调用。这个设计对于Cpp/Python开发者来说是需要是适应的。当然你可以使用`_`来关闭参数名强制，也可以指定外部和内部参数名，但是作为cpp开发者，对这个特性并不会感到特别. 
        
        cpp开发者在这里可以看出来，`number,end`这两个参数是值传递的，因此会经过一次拷贝，在函数内部这两个值是可以改变的。cpp也允许隐式类型转换。
        
        但是对于Swift来说，函数参数默认是常量，也就是不可修改的，而在调用的时候，也是不允许隐式类型转换的。
        
        这两个函数是对参数生命周期的处理结果是相同的，尽管Swift用的是ARC（自动引用计数）而cpp是栈。
        
    3. 返回值
        
        ```swift
        // swift
        func getVaule() -> Int {
            return 6
        }
        
        // cpp
        int GetValue() {
            return 6;
        }
        
        ```
        
        两者都需要指定返回值类型，Swift这里的写法是`→` 的方式，而cpp直接写在函数名称前。
        
        有一个很大的区别是，cpp是允许在有返回值定义的函数中不去return的（返回值是个UB），而Swift则不允许，这里也是安全设计上的考虑。 
        
        这里不得不提一个swift的语法糖：
        
        ```swift
        // swift
        func square(_ n: Int) -> Int {
        n * n 
        }
        ```
        
        上面这个函数是否会返回计算值？如果cpp中写类似的函数，那么这个函数是不会返回计算值的，甚至会被编译优化掉这个函数。但是swift在这里会返回，特别的，当函数体只有一行表达式且指定了返回值，return是可以省略的。 
        
        这个类似 C++ 的 lambda 或 Python 的单行函数，当然我更愿意相信这里可能只是swift特别提供的一个语法糖。在编译器中写明一个 `if 单行函数且返回值，则插入return` ，which is … fine. 
        
    4. 自定义参数标签（customize parameter labels）
        
        上面我们提到Swift支持外部参数名和内部参数名，我一开始看到这个内容的时候，非常难以理解这里是在说什么😂，是指有两个参数吗？这里揭开谜底，就是函数的参数名称可以有两个，一个是外部调用使用的参数名称，一个是函数内部使用的参数名称。
        
        ```swift
        func getSeverance(outie innie:String) {
        	print ("is Mark S")
        }
        
        getSeverance(outie : "Mark Scout")
        ```
        
        至于为什么，一开始我不懂，但是看过Severance的我懂了。
        
        还记得我们之前提到过swift也支持在调用函数的时候省略参数名称吗，其实不是省略，而是用`_`定义这个参数的outie，这样在调用的时候，实际上你使用的是`_`，在语义上就是可以省略的。
        
        ```swift
        func getSeverance(_ innie:String) {
        	print ("Farewell Irving B")
        }
        
        getSeverance("Irving Bailiff")
        ```
        
        最后总结一下:
        
        | 写法 | 外部调用形式 |
        | --- | --- |
        | `func greet(name: String)` | `greet(name: "Lucas")` |
        | `func greet(_ name: String)` | `greet("Lucas")` |
        | `func greet(for name: String)` | `greet(for: "Lucas")` |
    5. 函数参数默认值
    
        ```swift
        func makeInfo(for number: Int, end: Int = 12) {
            for i in 1...end {
                print("\(i) x \(number) is \(i * number)")
            }
        }
        makeInfo(for: 5, end: 20) 
        // use default value
        makeInfo(for: 8) 
        
        ```
    
        在这里感觉也和C++或Python差不多，在这里的区别在于Swift含有默认值的参数必须连续，必须用右往左连续默认，但是Python和C++则要求比较宽松。

## 错误处理

错误处理是swift与cpp/python差别较大的一个部分

首先要定义可能的错误类型，对于swift来说，集成Error协议的enum是最推荐的方式。

```swift
enum PasswordError: Error {
    case short, obvious
}

```

第二，用throws来抛出错误

```swift
func checkPassword(_ password: String) throws -> String {
    if password.count < 5 {
        throw PasswordError.short
    }
    if password == "12345" {
        throw PasswordError.obvious
    }
    return "Excellent"
}

```

注意到这个函数定义中，包含了throws关键字，来表示该函数可能会抛出错误，调用者必须用try或者显示处理。

第三，如何调用

```swift
do {
    let result = try checkPassword("12345")
    print("Password rating: \(result)")
} catch PasswordError.short {
    print("Please use a longer password.")
} catch PasswordError.obvious {
    print("I have the same combination on my luggage!")
} catch {
    print("Unknown error.")
}

```

在这里可能最大的区别，就是比python/cpp多了一个do字段。

### 语法糖

当然，这是Swift，不加入语法糖怎么行呢？

在这里有两个语法糖

1. `try!` 不做错误处理
    
    ```swift
    let result = try! checkPassword("SuperSecure") // ❌ 若失败，直接崩溃
    ```
    
2. `try?`：**失败时返回 nil**
    
    ```swift
    let result = try? checkPassword("12345")
    print(result ?? "Weak password") // ?? 表示如果返回nil，则打印"Weak password"
    ```

# Little Break

到这里，作为一名有 C++ 和 Python 背景的开发者，相信你已经能感受到 Swift 既熟悉又陌生的节奏了。

它吸收了很多现代语言的优点，比如安全性、表达力、语法简洁，但同时也保留了一些令人挠头的设计，比如 Optionals 的滥用、Protocol 的奇妙扩展、隐藏在糖衣下的陷阱们。

不过别担心，我们才刚刚开始。

在接下来的 Part 2 中，我会继续带你从 C++ / Python 的视角，探索 Swift 中更深的一些概念

🛤️ **下篇预告：**

**《Swift in Eye of C++ & Python Devs · Part 2》 —— 深入 Optionals / ARC / Protocol Extensions，打破“Swift 永远正确”的幻觉。**

如果你觉得 Part 1 还算有趣或有帮助，记得关注我的 GitHub / Notion 更新，咱们很快再见！