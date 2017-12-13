#iOS打印

> [gitHub链接](https://github.com/tangdaoyong/print)

### 几种查看日志的方式

 1. Xcode中运行代码程序查看。
 2. Xcode ->Window ->Devices 中查看手机中运行的进程的输出日志。
 3. 使用Mac自带的控制台应用程序查看日志，在LaunchPad中搜索`Console`应用。

### 几种常用的打印方式

#### Objective-C

1.记得以前写OC的时候，总是定义一个打印宏。(记得以前看过一篇博客，将宏的，就拿这个输出打印举例子，写的很好)

``` Objective-C
//打印输出(定位)
#define NSLog(FORMAT, ...) fprintf(stderr,"%s:%d  \t%s\n",[[[NSString stringWithUTF8String:__FILE__] lastPathComponent] UTF8String], __LINE__, [[NSString stringWithFormat:FORMAT, ##__VA_ARGS__] UTF8String]);
```

#### Swift

1. `println`

 > println是swift2中删除的print变体(末尾加换行符)。可以直接使用print替换。

2. `print`

* print在Swift2之前，没有在打印的末尾添加换行符，这也就是println存在的意义，现在print末尾默认添加了换行符。

* print有几个参数：separator连接符，terminator末尾符，toStream决定文本信息被发送到哪里。

* print还有个非常重要的性质：在调试应用程序的时候在Xcode控制台中输出信息，简单的说就是：上面三种查看打印的方式，print只在第一中可以看到打印信息，第二种和第三种方式，是看不到打印信息的。

    ```swift
     print(<#T##items: Any...##Any#>, separator: <#T##String#>, terminator: "", to: &<#T##TextOutputStream#>)
    ```

3.`NSLog`

* NSLog比较慢。
* 使用NSLog的好处是前面有时间戳也会有标识符添加到输出。
* NSLog语句会出现在设备的控制台和调试器的控制台中，而print只出现在调试器控制台中。即上面说的打印方式都会看到打印。
* 使用打印有一点需要了解一下：使用 print()/println()输出的话，它会简单地使用 流（stream） 的概念，把需要输出的每个字符一个一个的输出到控制台。普通使用并没有问题，可是当多线程同步输出的时候问题就来了，如果很多 print()/println() 同时打印，就会导致控制台上的字符混乱的堆在一起，而NSLog() 就没有这个问题。

4.`os_log`

* os_log即*统一日志记录*系统的一部分。(iOS 10/macOS 10.12)
* 使用os_log前先导入：
``` Swift
	import os.log
```
* 同NSLog一样，os_log将输出消息到Xcode调试控制台和设备控制台。
* 关于os_log的具体使用，[请参考](https://stackoverflow.com/questions/25951195/swift-print-vs-println-vs-nslog#)

### 打印

> 为了不进行一些无谓的print，最好使用宏自定义打印方法
> Swift中设置宏定义: build Settings -> Other Swift Flags ->Debug添加宏定义

``` Swift
public func YXPrint(_ message:Any?, columnNumber: Int = #column, fileName: String = #file, methodName: String = #function, lineNumber:Int = #line) {

    #if DEBUGSWIFT
        let title = "文件名:\(fileName)\n方法:\(methodName)\n行号:\(lineNumber)\n当前北京时间:" + YX_Time.default.myNewTime
        print(title)
        if message == nil  {
            print("空")
            return
        }
        print(message!)
    #endif
}
```