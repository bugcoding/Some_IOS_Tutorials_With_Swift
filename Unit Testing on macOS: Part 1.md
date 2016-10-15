#Unit Testing on macOS: Part 1
##

***

>* 原文链接 : [Unit Testing on macOS: Part 1/2](https://www.raywenderlich.com/141405/unit-testing-macos-part-12)
* 原文作者 : [Sarah Reichelt](https://www.raywenderlich.com/u/sarah)
* 译者 : []()

***


单元测试就是，我们心里都知道应该去做，但是却感觉它是繁杂无趣而且工作量巨大的一件麻烦事儿。

写代码如此愉悦，为啥还有人愿意花费写代码大半的时间来做代码检查？

理由就是自信！这在这个macOS上的单元测试入门教程中，你会学会如何测试你写的代码，还能获得自信，自信让代码按你的意愿去做事，自信即使对代码进行大改动也不会破坏原有的代码。

## 入门

示例工程使用Swift 3，同时必须使用至少是Xcode 8 beta 6的版本。下载[初始工程](https://cdn5.raywenderlich.com/wp-content/uploads/2016/08/HighRoller-starter.zip)并在Xcode中打开。

如果你看过raywenderlich.com中的任何一个教程，在这步你都想去构建运行程序了。但这次不同--你得去测试。打开**Product**菜单，选择**Test** ，提示你一个快捷键-- **Command-U** -- 之后你会多次用到这个快捷键。


当运行测试的时候，Xcode会先构建应用程序，然后应用程序的窗口出现几次之后，会看到消息提示“Test Succeeded”。在左侧的**Navigator**(导航栏)中，选中**Test Navigator**

![](https://cdn4.raywenderlich.com/wp-content/uploads/2016/08/TestNavigator2.png)


这里显示三个默认加入的测试栏目；每个测试栏目的边上都有一个绿色的小对勾，表示测试已经通过。如果想看测试栏目都包含了哪些文件，点击**Test Navigator**的里面第二行有大写T字母图标开头的叫**High Roller Tests**的栏目。

![](https://cdn1.raywenderlich.com/wp-content/uploads/2016/08/DefaultTests3.png)

有几个要点需要提示一下:

- import引用: **XCTest**是Xcode提供的测试框架。`@testable import High_Roller`引入是用来给测试代码访问`High_Roller`模块用的。每个测试文件都需要引入这二个。
- `setup()`和`tearDown()` : 这二个方法会分别在每个单独的测试方法调用前后被调用。
- `testExample()`和`testPerformanceExample()`:真正的测试部分。第一个方法用来测试代码功能，第二个用来测试代码性能。每个测试方法都必须以test开头，这样Xcode才能区分出这些代码是用于执行测试的。

### 单元测试是什么

在你开始写单元测试之前，有必要简要了解一下什么是单元测试以及我们为什么要用单元测试。

单元测试就是测试单独的代码片段或者一组代码片段的一个函数。它并不会被包含到你开始的应用程序当中，只是在开发阶段用来测试你所写的代码是否按你的期望来运行的。

大部分人对单元测试的第一反应是：“你让我写二份代码？一份是应用程序自己内部的功能代码，另一份是测试这个功能的代码？”，但事实可能比二份还要多--一些大的工程到最后的时候测试代码比功能本身的代码还要多得多。

首先这看起来非常耗费时间和精力--但是一旦你通过测试发现了你代码中不曾注意到的错误，或者发觉了重构代码产生的副作用，你就会体会到单元测试是个多么棒的工具。任何没有单元测试的工程在用不了多久之后就会显得很不靠谱，你不太敢对工程做改动，因为你不知道改动之后会发生什么。

## 测试驱动开发

测试驱动开发(TDD)是单元测试的一个分支，就是只针对必须的测试步骤编写对应的代码。还有，首先这种方式看起来十分奇怪而且你能看到在一分钟内就产生了十分怪异的代码。结果就是你还得去想在开始写真正的代码之前产生的这些代码是干什么用的。

测试驱动 开发有三个重复的步骤:

1. 红色: 写一个失败的测试。
2. 绿色: 写下很少的代码让测试通过。
3. 重构: 可选的；斜体app或者测试代码能通过重构变得更好，那就马上重构。

这几步非常重要，也是TDD起作用的关键步骤。修复失败的测试这会给你一个清晰的印象让你知道你的代码干了什么。如果第一次就测试通过，同时也没有写任何新代码，那你对下一阶段的开发就没有了针对性。

开始，你需要写下一系列测试同时还有通过TDD产生的代码。

## 测试工程

这个工程是用于棋盘游戏玩家的一个掷骰子的工具。是否曾经和家人一起玩游戏的时候发现在骰子被狗吞掉？现在app来拯救你了。如果有任意人说"我不相信电脑不作弊!"你就可以大声的对他说这app已以通过单元测试，绝对没问题。这肯定会给家人留下深刻的印象—晚上你可以保存游戏进度。

这个app的模型有二个主要的对象类型: `Dice`，有一个`value`属性，还有一个用于生成随机值的方法。还有`Roll`，一个`Dice`对象的集合，还有掷这些骰子的方法，所有骰子的总和等。

第一个测试类是给`Dice`对象用的。

## Dice测试类

在Xcode的文件导航栏中选择`High RollerTests`组，选中`File/New/File...`选择`macOS\Unit Test Case Class`。点击`Next`命名类为`DiceTests`。确保语言设置的是Swift。点击`Next`和`Create`。

选中类中的所有代码删掉。在`DiceTests.swift`文件中的`import XCTest`下加入如下语句：

```swift
@testable import High_Roller
```

现在你可以删掉`HighRollerTests.swift`文件了，因为你不再需要这个默认的测试了。

### 你的第一个测试

在`DiceTests`类中加入下面的测试函数:

```swift
func testForDice(){
  let _ = Dice()
}
```

在你运行测试之前这会报一个编译错误:"Use of unresolved identifier 'Dice'"。在TDD中，编译错误就是测试失败，所以你必须完成TDD测试步骤中的第一步。

用最少的代码让这个测试通过，在文件导航栏中选中在`High Roller`组中的`Model`组。用`File\New\File...`创建一个新的Swift文件并命名为`Dice.swift`。

文件中加入如下代码:

```swift
struct Dice{
  
}
```

返回`DiceTests.swift`文件中，下次构建工程的时候错误才会消失。现在你可通过几种不同的方式来运行测试。

如果你直接点击测试函数边栏旁边的菱形，则只会运行这一个单独的函数。试着运行一下，这个菱形就会变成绿色表示测试已经通过。

你可以在任何时候点击绿色(红色表示测试失败)符号来运行测试。在类名的旁边还有另一个红色的符号。点击就会运行在这个类中的所有测试。此刻，这与单独运行函数测试是一样的，但是这很快就会改变。

最后一种方式就是运行所有的测试。

![](https://koenig-media.raywenderlich.com/uploads/2016/08/RunningTests.png)

按下`Command-U`来运行所有的测试同时去文件导航栏查看在High RollerTests文件中的单个测试；你需要展开文件来看。绿色的对勾会出现在每个测试的旁边。如果你把鼠标在每个测试栏目上上下去一下会有小的运行按钮出现，你可以用它运行任意的测试或者测试集。

在文件导航栏，你能看到`High RollerUITest`也在运行。UI测试有个问题就是跑起来非常慢。而你又想尽可能快的测试，没有任何缺陷的频繁测试。为解决这个问题，需要改动一下scheme让UI测试不自动运行。

在工具栏中弹出scheme菜单，选择`Edit scheme...`在左边面板中点击`Test`，取消选中`High RollerUITests`。关闭scheme窗口，再用`Command-U`跑一下测试。UI测试已在测试导航栏中变灰，但是你还可以手动去运行它。

![](https://koenig-media.raywenderlich.com/uploads/2016/08/TurnOffUITests.png)

### 选择运行哪个测试

那么哪个方法需要你跑测试呢？单个的函数，类还是全部测试？











