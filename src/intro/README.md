# 简介

之前已在：

[移动端自动化测试概览](https://book.crifan.com/books/mobile_automation_overview/website/)

中提到过，iOS自动化测试的主流框架之一是：`facebook-wda`

此处去详细介绍一下。

* facebook-wda
  * 主页
    * [openatx/facebook-wda: Facebook WebDriverAgent Python Client Library (not official)](https://github.com/openatx/facebook-wda)
  * 作者：`openatx`
  * 语言：`Python`
  * 实现原理
    * 基于`Appium`的`WebDriverAgent`
      * 关于`WebDriverAgent`
        * 简称：`WDA`
        * 是什么=一句话描述：一个基于`W3C`的`WebDriver`的`server`（的具体实现）
        * 底层依赖：`Apple`的[XCUITest](https://developer.apple.com/library/archive/documentation/DeveloperTools/Conceptual/testing_with_xcode/chapters/09-ui_testing.html)（测试框架）
        * 起源和状态
          * 最早：`Facebook`开发的
              * Facebook的WebDriverAgent
                  * 现已**暂停维护**=`archived`=`read-only`
                  * 主页
                      * [facebookarchive/WebDriverAgent: A WebDriver server for iOS that runs inside the Simulator](https://github.com/facebookarchive/WebDriverAgent)
          * 现在：已停止维护
              * `Appium`接手继续维护和更新
                  * Appium的WebDriverAgent
                      * 主页
                          * [appium/WebDriverAgent: A WebDriver server for iOS that runs inside the Simulator](https://github.com/appium/WebDriverAgent)
      * 关于`WebDriver`
        * 作者：`W3C`
        * 是什么：一套协议规范
          * 特点：与平台协议无关
          * 目的=作用：远程控制设备
        * 主页
          * https://w3c.github.io/webdriver/
      * 关于：`Apple`的`XCUITest`
        * 是什么：苹果的测试框架
        * 官网文档
          * 之前：
            * [XCUITest](https://developer.apple.com/library/archive/documentation/DeveloperTools/Conceptual/testing_with_xcode/chapters/09-ui_testing.html)
          * （貌似）最新
          * [XCTest | Apple Developer Documentation](https://developer.apple.com/documentation/xctest)
      * 关于：`Appium`
        * [Appium: Mobile App Automation](http://appium.io)
