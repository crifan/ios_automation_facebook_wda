# 微信相关

## 判断是否处于微信

```python
def iOSisInWeixin(self):
    tabBarClassChain = "/XCUIElementTypeTabBar[`rect.width = %d`]" % self.X

    """
        <XCUIElementTypeTabBar type="XCUIElementTypeTabBar" enabled="true" visible="true" x="0" y="680" width="414" height="56">
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="680" width="414" height="56">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="680" width="414" height="56">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="0" y="680" width="414" height="56"/>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="0" y="680" width="414" height="56"/>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="680" width="414" height="56"/>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>
            <XCUIElementTypeButton type="XCUIElementTypeButton" value="2" name="微信" label="微信" enabled="true" visible="true" x="2" y="681" width="100" height="55"/>
            <XCUIElementTypeButton type="XCUIElementTypeButton" name="通讯录" label="通讯录" enabled="true" visible="true" x="106" y="681" width="99" height="55"/>
            <XCUIElementTypeButton type="XCUIElementTypeButton" value="未读" name="发现" label="发现" enabled="true" visible="true" x="209" y="681" width="100" height="55"/>
            <XCUIElementTypeButton type="XCUIElementTypeButton" name="我" label="我" enabled="true" visible="true" x="313" y="681" width="99" height="55"/>
        </XCUIElementTypeTabBar>
    """
    weixinTabQuery = {"type":"XCUIElementTypeButton", "name": "微信", "label": "微信", "enabled": "true"}
    weixinTabQuery["parent_class_chains"] = [ tabBarClassChain ]
    isFoundWeixin, respInfo = self.findElement(query=weixinTabQuery, timeout=0.2)
    iOSisInWeixin = isFoundWeixin
    return iOSisInWeixin
```

调用：

```python
beInWeixin = self.iOSisInWeixin()
```

## get_navigationBar_bounds 计算微信顶部系统导航栏的区域bounds

```python
# def get_navigationBar_bounds(self, pageSrcXml):
def get_navigationBar_bounds(self):
    """
        计算微信顶部系统导航栏的区域bounds
    """
    bounds = None
    """
        微信 顶部 导航栏：
            <XCUIElementTypeNavigationBar type="XCUIElementTypeNavigationBar" name="公众号" enabled="true" visible="true" x="0" y="20" width="375" height="44">
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="通讯录" label="通讯录" enabled="true" visible="true" x="10" y="20" width="64" height="44"/>
                <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="公众号" name="公众号" label="公众号" enabled="true" visible="true" x="187" y="24" width="1" height="36"/>
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="添加" label="添加" enabled="true" visible="true" x="317" y="20" width="58" height="44"/>
            </XCUIElementTypeNavigationBar>
    """
    naviBarQuery = {"type":"XCUIElementTypeNavigationBar", "enabled":"true", "x": "0"}
    isFound, naviBarElement = self.findElement(naviBarQuery)
    if isFound:
        isVisible = naviBarElement.visible
        if isVisible:
            curRect = naviBarElement.bounds
            logging.debug("curRect=%s", curRect)
            x0 = curRect.x
            y0 = curRect.y
            x1 = curRect.x1
            y1 = curRect.y1
            bounds = [x0, y0, x1, y1] # [0, 20, 375, 64]

    logging.debug("bounds=%s", bounds)
    return bounds
```

调用举例：

```python
def calcHeadY(curSession):
    bounds = get_navigationBar_bounds(curSession)
    y1 = bounds[-1]
    headY = y1
    return headY
```

## iOSWeixinExceptionNextStep 当发生异常提示时，一直点击（多数都是 下一步），直到退出异常提示

```python
def iOSMakesureIntoWeixin(self):
    """Makesure into weixin main page
        if exception, process it, until into weixin page
    """
    # maxRetryNum = 3
    maxRetryNum = 5
    beInWeixin = self.iOSisInWeixin()

    while (not beInWeixin) and (maxRetryNum > 0):
        beInWeixin = self.iOSisInWeixin()
        if not beInWeixin:
            # try process for exception
            foundAndProcessedException = self.iOSWeixinExceptionNextStep()
            if foundAndProcessedException:
                beInWeixin = self.iOSisInWeixin()

        maxRetryNum -= 1

    return beInWeixin

def iOSWeixinExceptionNextStep(self):
    foundAndClicked = False

    scrollViewClassChain = "/XCUIElementTypeScrollView[`rect.width = %d AND rect.height = %d`]" % (self.X, self.totalY)

    ButtonLabelNextStep = "下一步"
    ButtonLabelIntoWeixin = "进入微信"

    # nextStepButtonQuery = {"type":"XCUIElementTypeButton", "label": "下一步", "enabled": "true"}
    # nextStepButtonQuery["parent_class_chains"] = [scrollViewClassChain]

    # intoWeixinButtonQuery = {"type":"XCUIElementTypeButton", "label": "进入微信", "enabled": "true"}
    # intoWeixinButtonQuery["parent_class_chains"] = [scrollViewClassChain]

    """
        <XCUIElementTypeScrollView type="XCUIElementTypeScrollView" enabled="true" visible="true" x="0" y="0" width="414" height="736">
            <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="147" y="106" width="120" height="120"/>
            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="当前检测出微信连续异常，你可以尝试以下方法修复：" name="当前检测出微信连续异常，你可以尝试以下方法修复：" label="当前检测出微信连续异常，你可以尝试以下方法修复：" enabled="true" visible="true" x="18" y="262" width="378" height="39"/>
            <XCUIElementTypeButton type="XCUIElementTypeButton" name="下一步" label="下一步" enabled="true" visible="true" x="18" y="319" width="378" height="47">
                <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="下一步" name="下一步" label="下一步" enabled="true" visible="true" x="179" y="331" width="56" height="23"/>
            </XCUIElementTypeButton>
        </XCUIElementTypeScrollView>
    """
    # continousExceptionQuery = {"type":"XCUIElementTypeStaticText", "value": "当前检测出微信连续异常，你可以尝试以下方法修复：", "enabled": "true"}
    # continousExceptionQuery["parent_class_chains"] = [scrollViewClassChain]
    # isFoundContinousException, respInfo = self.findElement(query=continousExceptionQuery, timeout=0.5)
    # if isFoundContinousException:
    #     isFoundNextStep, respInfo = self.findElement(query=nextStepButtonQuery)
    #     if isFoundNextStep:
    #         nextStepElement = respInfo
    #         foundAndClicked = self.clickElement(nextStepElement)

    """
        <XCUIElementTypeScrollView type="XCUIElementTypeScrollView" enabled="true" visible="true" x="0" y="0" width="414" height="736">
            <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="147" y="106" width="120" height="120"/>
            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="建议你重启手机，避免微信启动异常" name="建议你重启手机，避免微信启动异常" label="建议你重启手机，避免微信启动异常" enabled="true" visible="true" x="18" y="262" width="378" height="15"/>
            <XCUIElementTypeButton type="XCUIElementTypeButton" name="重启手机" label="重启手机" enabled="true" visible="true" x="18" y="285" width="378" height="47">
                <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="重启手机" name="重启手机" label="重启手机" enabled="true" visible="true" x="170" y="297" width="74" height="23"/>
            </XCUIElementTypeButton>
            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="不重启手机，进入下一步" name="不重启手机，进入下一步" label="不重启手机，进入下一步" enabled="true" visible="true" x="18" y="356" width="378" height="15"/>
            <XCUIElementTypeButton type="XCUIElementTypeButton" name="下一步" label="下一步" enabled="true" visible="true" x="18" y="379" width="378" height="47">
                <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="下一步" name="下一步" label="下一步" enabled="true" visible="true" x="179" y="391" width="56" height="23"/>
            </XCUIElementTypeButton>
            <XCUIElementTypeOther type="XCUIElementTypeOther" value="0%" name="垂直滚动条, 1页" label="垂直滚动条, 1页" enabled="true" visible="true" x="381" y="64" width="30" height="672"/>
            <XCUIElementTypeOther type="XCUIElementTypeOther" value="0%" name="水平滚动条, 1页" label="水平滚动条, 1页" enabled="true" visible="true" x="0" y="703" width="414" height="30"/>
        </XCUIElementTypeScrollView>
    """
    # # if not foundAndClicked:
    # rebootWeixinQuery = {"type":"XCUIElementTypeStaticText", "value": "建议你重启手机，避免微信启动异常", "enabled": "true"}
    # rebootWeixinQuery["parent_class_chains"] = [scrollViewClassChain]
    # isFoundRebootWeixin, respInfo = self.findElement(query=rebootWeixinQuery, timeout=0.5)
    # if isFoundRebootWeixin:
    #     isFoundNextStep, respInfo = self.findElement(query=nextStepButtonQuery)
    #     if isFoundNextStep:
    #         nextStepElement = respInfo
    #         foundAndClicked = self.clickElement(nextStepElement)
    
    """
        <XCUIElementTypeScrollView type="XCUIElementTypeScrollView" enabled="true" visible="true" x="0" y="0" width="414" height="736">
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="64" width="414" height="243">
                <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="115" y="94" width="184" height="183"/>
                <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="115" y="94" width="184" height="183"/>
            </XCUIElementTypeOther>
            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="清理缓存会清理你的手机本地缓存文件，但不会清理你的消息数据，使用后需要重新登录微信" name="清理缓存会清理你的手机本地缓存文件，但不会清理你的消息数据，使用后需要重新登录微信" label="清理缓存会清理你的手机本地缓存文件，但不会清理你的消息数据，使用后需要重新登录微信" enabled="true" visible="true" x="18" y="330" width="378" height="29"/>
            <XCUIElementTypeButton type="XCUIElementTypeButton" name="清理缓存" label="清理缓存" enabled="true" visible="true" x="18" y="367" width="378" height="47">
                <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="清理缓存" name="清理缓存" label="清理缓存" enabled="true" visible="true" x="170" y="379" width="74" height="23"/>
            </XCUIElementTypeButton>
            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="不清理缓存，进入下一步" name="不清理缓存，进入下一步" label="不清理缓存，进入下一步" enabled="true" visible="true" x="18" y="438" width="378" height="15"/>
            <XCUIElementTypeButton type="XCUIElementTypeButton" name="下一步" label="下一步" enabled="true" visible="true" x="18" y="461" width="378" height="47">
                <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="下一步" name="下一步" label="下一步" enabled="true" visible="true" x="179" y="473" width="56" height="23"/>
            </XCUIElementTypeButton>
            <XCUIElementTypeOther type="XCUIElementTypeOther" value="0%" name="垂直滚动条, 1页" label="垂直滚动条, 1页" enabled="true" visible="true" x="381" y="64" width="30" height="672"/>
            <XCUIElementTypeOther type="XCUIElementTypeOther" value="0%" name="水平滚动条, 1页" label="水平滚动条, 1页" enabled="true" visible="true" x="0" y="703" width="414" height="30"/>
        </XCUIElementTypeScrollView>
    """
    # # if not foundAndClicked:
    # clearCacheQuery = {"type":"XCUIElementTypeStaticText", "value": "清理缓存会清理你的手机本地缓存文件，但不会清理你的消息数据，使用后需要重新登录微信", "enabled": "true"}
    # clearCacheQuery["parent_class_chains"] = [scrollViewClassChain]
    # isFoundClearCache, respInfo = self.findElement(query=clearCacheQuery, timeout=0.5)
    # if isFoundClearCache:
    #     isFoundNextStep, respInfo = self.findElement(query=nextStepButtonQuery)
    #     if isFoundNextStep:
    #         nextStepElement = respInfo
    #         foundAndClicked = self.clickElement(nextStepElement)
    
    """
        <XCUIElementTypeScrollView type="XCUIElementTypeScrollView" enabled="true" visible="true" x="0" y="0" width="414" height="736">
            <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="147" y="106" width="120" height="120"/>
            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="如果问题还没解决，你可以上传手机日志文件，协助技术人员解决问题。所上传的文件不会包含聊天记录等私人内容，且不会被对外传播" name="如果问题还没解决，你可以上传手机日志文件，协助技术人员解决问题。所上传的文件不会包含聊天记录等私人内容，且不会被对外传播" label="如果问题还没解决，你可以上传手机日志文件，协助技术人员解决问题。所上传的文件不会包含聊天记录等私人内容，且不会被对外传播" enabled="true" visible="true" x="18" y="262" width="378" height="29"/>
            <XCUIElementTypeButton type="XCUIElementTypeButton" name="上传文件" label="上传文件" enabled="true" visible="true" x="18" y="298" width="378" height="47">
                <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="上传文件" name="上传文件" label="上传文件" enabled="true" visible="true" x="170" y="310" width="74" height="23"/>
            </XCUIElementTypeButton>
            <XCUIElementTypeButton type="XCUIElementTypeButton" name="显示上传文件列表" label="显示上传文件列表" enabled="true" visible="true" x="17" y="353" width="100" height="15">
                <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="显示上传文件列表" name="显示上传文件列表" label="显示上传文件列表" enabled="true" visible="true" x="17" y="352" width="100" height="16"/>
            </XCUIElementTypeButton>
            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="不上传文件，进入下一步" name="不上传文件，进入下一步" label="不上传文件，进入下一步" enabled="true" visible="true" x="18" y="391" width="378" height="15"/>
            <XCUIElementTypeButton type="XCUIElementTypeButton" name="下一步" label="下一步" enabled="true" visible="true" x="18" y="413" width="378" height="47">
                <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="下一步" name="下一步" label="下一步" enabled="true" visible="true" x="179" y="425" width="56" height="23"/>
            </XCUIElementTypeButton>
            <XCUIElementTypeOther type="XCUIElementTypeOther" value="0%" name="垂直滚动条, 1页" label="垂直滚动条, 1页" enabled="true" visible="true" x="381" y="64" width="30" height="672"/>
            <XCUIElementTypeOther type="XCUIElementTypeOther" value="0%" name="水平滚动条, 1页" label="水平滚动条, 1页" enabled="true" visible="true" x="0" y="703" width="414" height="30"/>
        </XCUIElementTypeScrollView>
    """

    """
        <XCUIElementTypeScrollView type="XCUIElementTypeScrollView" enabled="true" visible="true" x="0" y="0" width="414" height="736">
            <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="147" y="106" width="120" height="120"/>
            <XCUIElementTypeButton type="XCUIElementTypeButton" enabled="true" visible="true" x="147" y="106" width="120" height="120"/>
            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="如果使用过程中还出现问题，建议你重启手机，更新系统，或者联系我们的客服人员。 当前设备标识：7a3e060db5c52291******" name="如果使用过程中还出现问题，建议你重启手机，更新系统，或者联系我们的客服人员。 当前设备标识：7a3e060db5c52291******" label="如果使用过程中还出现问题，建议你重启手机，更新系统，或者联系我们的客服人员。 当前设备标识：7a3e060db5c52291******" enabled="true" visible="true" x="18" y="262" width="378" height="58"/>
            <XCUIElementTypeButton type="XCUIElementTypeButton" name="进入微信" label="进入微信" enabled="true" visible="true" x="18" y="338" width="378" height="47">
                <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="进入微信" name="进入微信" label="进入微信" enabled="true" visible="true" x="170" y="350" width="74" height="23"/>
            </XCUIElementTypeButton>
            <XCUIElementTypeOther type="XCUIElementTypeOther" value="0%" name="垂直滚动条, 1页" label="垂直滚动条, 1页" enabled="true" visible="true" x="381" y="64" width="30" height="672"/>
            <XCUIElementTypeOther type="XCUIElementTypeOther" value="0%" name="水平滚动条, 1页" label="水平滚动条, 1页" enabled="true" visible="true" x="0" y="703" width="414" height="30"/>
        </XCUIElementTypeScrollView>
    """

    EachStepNoticeList = [
        # ("当前检测出微信连续异常，你可以尝试以下方法修复：", ButtonLabelNextStep),
        ("当前检测出微信连续异常", ButtonLabelNextStep),
        ("建议你重启手机，避免微信启动异常", ButtonLabelNextStep),
        ("清理缓存会清理你的手机本地缓存文件，但不会清理你的消息数据，使用后需要重新登录微信", ButtonLabelNextStep),
        ("如果问题还没解决，你可以上传手机日志文件，协助技术人员解决问题。所上传的文件不会包含聊天记录等私人内容，且不会被对外传播", ButtonLabelNextStep),
        ("如果使用过程中还出现问题，建议你重启手机，更新系统，或者联系我们的客服人员。", ButtonLabelIntoWeixin),
    ]
    for (curStepNotice, buttonLabel) in EachStepNoticeList:
        # curNoticeQuery = {"type":"XCUIElementTypeStaticText", "value": curStepNotice, "enabled": "true"}
        # curNoticeQuery = {"type":"XCUIElementTypeStaticText", "value_part": curStepNotice, "enabled": "true"}
        curNoticeQuery = {"type":"XCUIElementTypeStaticText", "valueContains": curStepNotice, "enabled": "true"}
        curNoticeQuery["parent_class_chains"] = [scrollViewClassChain]
        isFoundCurNotice, respInfo = self.findElement(query=curNoticeQuery, timeout=0.5)
        if isFoundCurNotice:
            # isFoundNextStep, respInfo = self.findElement(query=nextStepButtonQuery)
            # isFoundButton, respInfo = self.findElement(query=buttonQuery)
            buttonQuery = {"type":"XCUIElementTypeButton", "label": buttonLabel, "enabled": "true"}
            buttonQuery["parent_class_chains"] = [scrollViewClassChain]
            isFoundButton, respInfo = self.findElement(query=buttonQuery)
            if isFoundButton:
                buttonElement = respInfo
                clickOk = self.clickElement(buttonElement)
                if clickOk:
                    foundAndClicked = clickOk

    return foundAndClicked
```

说明：

通过wda多次调试iOS微信，会导致退出再打开，时不时的会出现异常提示

希望一直点击，直到提示消失

![weixin_detect_exception](../../../assets/img/weixin_detect_exception.png)

![need_reboot_computer](../../../assets/img/need_reboot_computer.png)

![clear_cache_next_step](../../../assets/img/clear_cache_next_step.png)

![upload_file_next_step](../../../assets/img/upload_file_next_step.png)

![finnal_into_weixin](../../../assets/img/finnal_into_weixin.png)

详见：

【已解决】自动抓包工具适配iOS：当前检测出微信连续异常，你可以尝试一下方法修复 下一步
