# 开发心得

## wda如何同时测试多个设备

* **问**：如何用wda同时测试多个设备？
* **答**：使用不同端口转发

具体做法举例：

```bash
iproxy 8100 8100
iproxy 8101 8101
iproxy 8102 8102
```

代码中，本地连接不同端口：

```python
gWdaClient = wda.Client('http://localhost:8100’)
gWdaClient = wda.Client('http://localhost:8101’)
gWdaClient = wda.Client('http://localhost:8102')
```

即可。

## 感慨：对于apple的态度

见到[别人](https://www.lizenghai.com/archives/19660.html)有提到：

> Apple公司因其无与伦比的设计，让无数果粉为之迷恋
> 
> 但作为iOS测试人员，也因为iOS系统封闭和不开放库苦不堪言，羡慕死Android测试

对此深有体会，不能再同意更多：

* 消费者：对于apple产品觉得很好看，很喜欢
* 测试、自动化人员：苦不堪言
    * 原因：apple生态封闭，不开放
        * 虽然提供了XCTest，但是很不好用
        * iOS，Mac等内部的库是不开放的
            * 没法直接用来做测试和自动化
            * -》Facebook的WebDriverAgent（后由Appium维护）已经做到了
                * 用工具从 私有的库中dump出头文件和api接口
                * 但是实际用起来，仍然是各种bug和不兼容
                    * 包括但不限于（后续会介绍到的）[各种坑](https://book.crifan.com/books/ios_automation_facebook_wda/website/ios_pitfall/)
                        * 获取不到源码
                        * 只能获取部分源码
                        * 获取源码会导致test manager崩溃（需要重装WebDriverAgentRunner）
                        * 无法完美支持元素visible属性
                        * 获取到的源码很混乱
                          * 比如
                            * 包含了前一页（甚至几页）的xml源码
