# 常用代码段

在折腾`facebook-wda`期间，把各种常用的功能封装成了函数，现整理如下，供需要的参考：

## get_cmd_lines 执行命令返回结果

```python
def get_cmd_lines(cmd, text=False):
    # 执行cmd命令，将结果保存为列表
    resultStr = ""
    resultStrList = []
    try:
        consoleOutputByte = subprocess.check_output(cmd, shell=True) # b'C02Y3N10JHC8\n'
        try:
            resultStr = consoleOutputByte.decode("utf-8")
        except UnicodeDecodeError:
            # TODO: use chardet auto detect encoding
            # consoleOutputStr = consoleOutputByte.decode("gbk")
            resultStr = consoleOutputByte.decode("gb18030")

        if not text:
            resultStrList = resultStr.splitlines()
    except Exception as err:
        print("err=%s when run cmd=%s" % (err, cmd))

    if text:
        return resultStr
    else:
        return resultStrList
```

## multipleRetry 多次尝试执行某个函数直到成功

```python
    def multipleRetry(functionInfoDict, maxRetryNum=5, sleepInterval=0.1, isShowErrWhenFail=True):
        """
        do something, retry mutiple time if fail

        Args:
            functionInfoDict (dict): function info dict contain functionCallback and [optional] functionParaDict
            maxRetryNum (int): max retry number
            sleepInterval (float): sleep time of each interval when fail
            isShowErrWhenFail (bool): show error when fail if true
        Returns:
            bool
        Raises:
        """
        doSuccess = False
        functionCallback = functionInfoDict["functionCallback"]
        functionParaDict = functionInfoDict.get("functionParaDict", None)

        curRetryNum = maxRetryNum
        while curRetryNum > 0:
            if functionParaDict:
                doSuccess = functionCallback(**functionParaDict)
            else:
                doSuccess = functionCallback()
            
            if doSuccess:
                break
            
            time.sleep(sleepInterval)
            curRetryNum -= 1

        if not doSuccess:
            if isShowErrWhenFail:
                functionName = str(functionCallback)
                # '<bound method DevicesMethods.switchToAppStoreSearchTab of <src.AppCrawler.AppCrawler object at 0x1053abe80>>'
                logging.error("Still fail after %d retry for %s", maxRetryNum, functionName)
        return doSuccess
```

## isPageHasNaviBar_iOS 是否包含导航栏

```python
def isPageHasNaviBar_iOS(self, page):
    """Check whether current page has XCUIElementTypeNavigationBar"""
    hasNaviBar = False
    naviBarName = ""
    """
        has:
            <XCUIElementTypeNavigationBar type="XCUIElementTypeNavigationBar" name="微信" enabled="true" visible="true" x="0" y="20" width="414" height="44">
                <XCUIElementTypeOther type="XCUIElementTypeOther" name="微信," label="微信," enabled="true" visible="true" x="206" y="24" width="2" height="36"/>
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="更多" label="更多" enabled="true" visible="true" x="338" y="20" width="56" height="44"/>
            </XCUIElementTypeNavigationBar>

            <XCUIElementTypeNavigationBar type="XCUIElementTypeNavigationBar" name="通讯录" enabled="true" visible="true" x="0" y="20" width="414" height="44">
                <XCUIElementTypeOther type="XCUIElementTypeOther" name="通讯录," label="通讯录," enabled="true" visible="true" x="206" y="24" width="2" height="36"/>
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="添加朋友" label="添加朋友" enabled="true" visible="true" x="354" y="20" width="40" height="44"/>
            </XCUIElementTypeNavigationBar>

            <XCUIElementTypeNavigationBar type="XCUIElementTypeNavigationBar" name="公众号" enabled="true" visible="true" x="0" y="20" width="414" height="44">
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="返回" label="返回" enabled="true" visible="true" x="20" y="20" width="30" height="44"/>
                <XCUIElementTypeOther type="XCUIElementTypeOther" name="公众号," label="公众号," enabled="true" visible="true" x="206" y="24" width="2" height="36"/>
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="添加" label="添加" enabled="true" visible="true" x="354" y="20" width="40" height="44"/>
            </XCUIElementTypeNavigationBar>

            某个公众号：动卡空间
            <XCUIElementTypeNavigationBar type="XCUIElementTypeNavigationBar" name="动卡空间" enabled="true" visible="true" x="0" y="20" width="414" height="44">
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="返回" label="返回" enabled="true" visible="true" x="20" y="20" width="30" height="44"/>
                <XCUIElementTypeOther type="XCUIElementTypeOther" name="动卡空间," label="动卡空间," enabled="true" visible="true" x="206" y="24" width="2" height="36"/>
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="聊天详情" label="聊天详情" enabled="true" visible="true" x="354" y="20" width="40" height="44"/>
            </XCUIElementTypeNavigationBar>
        not:
            <XCUIElementTypeNavigationBar type="XCUIElementTypeNavigationBar" name="通讯录" enabled="true" visible="true" x="0" y="20" width="414" height="44">
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="返回" label="返回" enabled="true" visible="true" x="20" y="20" width="30" height="44"/>
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="206" y="24" width="2" height="36"/>
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="搜索" label="搜索" enabled="true" visible="true" x="306" y="20" width="40" height="44"/>
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="更多" label="更多" enabled="true" visible="true" x="354" y="20" width="40" height="44"/>
            </XCUIElementTypeNavigationBar>
    """
    soup = CommonUtils.xmlToSoup(page)
    foundNaviBar = soup.find(
        'XCUIElementTypeNavigationBar',
        attrs={"type": "XCUIElementTypeNavigationBar", "enabled": "true"},
    )
    if foundNaviBar:
        # maybeFakeNaviBarName = foundNaviBar.attrs["name"]
        maybeFakeNaviBarName = foundNaviBar.attrs.get("name")
        typeOtherNameP = re.compile("%s,?" % maybeFakeNaviBarName)
        foundTypeOther = foundNaviBar.find("XCUIElementTypeOther", attrs={"type": "XCUIElementTypeOther", "name": typeOtherNameP})
        if foundTypeOther:
            hasNaviBar = True
            naviBarName = maybeFakeNaviBarName

    return hasNaviBar, naviBarName
```

调用：

```python
OfflinePageNaviBarNameList = [
    "微信",
    "通讯录",
    "公众号",
]
hasNaviBar, naviBarName = self.isPageHasNaviBar_iOS(page)
```

## 获取页面源码getCurPageSource_iOS

```python
def getCurPageSource_iOS(self, sourceFormat="xml"):
    """Get iOS current page source of xml/json

    Args:
        sourceFormat (str): source format: xml/json
    Returns:
        str
    Raises:
    """
    logging.info("start get iOS page source")
    pageSource = ""
    beforeGetTime = time.time()
    if sourceFormat == "xml":
        curPageXml = self.wdaClient.source() # format XML
        pageSource = curPageXml
    elif sourceFormat == "json":
        curPageJson = self.wdaClient.source(accessible=True) # default false, format JSON
        pageSource = curPageJson
    else:
        logging.error("Unsupported source format: %s", sourceFormat)
    afterGetTime = time.time()
    getSourceTime = afterGetTime - beforeGetTime
    logging.info("Cost %.2f seconds to get iOS page source", getSourceTime)
    return pageSource
```

调用：

```python
def getCurPageSource(self):
    if self.isAndroid:
        return self.getCurPageSource_Android()
    elif self.isiOS:
        return self.getCurPageSource_iOS()
```

## back_iOS 返回前一页

```python
def back_iOS(self):
    isFoundAndClicked = False
    # iOS: NO phisical back button
    # so try support following case to find and click to implement back

    backQueryList = []

    parentNaviBarClassChain = "/XCUIElementTypeNavigationBar[`rect.width = %d`]" % self.X

    # case 1: 左上角 导航栏 返回
    """
        <XCUIElementTypeNavigationBar type="XCUIElementTypeNavigationBar" name="动卡空间" enabled="true" visible="true" x="0" y="20" width="414" height="44">
            <XCUIElementTypeButton type="XCUIElementTypeButton" name="返回" label="返回" enabled="true" visible="true" x="20" y="20" width="30" height="44"/>
            <XCUIElementTypeOther type="XCUIElementTypeOther" name="动卡空间," label="动卡空间," enabled="true" visible="true" x="206" y="24" width="2" height="36"/>
            <XCUIElementTypeButton type="XCUIElementTypeButton" name="聊天详情" label="聊天详情" enabled="true" visible="true" x="354" y="20" width="40" height="44"/>
        </XCUIElementTypeNavigationBar>
    """
    NaviBarReturnText = "返回"
    returnButtonQuery = {"type":"XCUIElementTypeButton", "name": NaviBarReturnText, "label": NaviBarReturnText, "enabled": "true"}
    returnButtonQuery["parent_class_chains"] = [ parentNaviBarClassChain ]
    backQueryList.append(returnButtonQuery)

    """
        善友筹 二级页面 小满 左上角返回按钮：
        <XCUIElementTypeNavigationBar type="XCUIElementTypeNavigationBar" name="小满丨人生最好的状态是小满" enabled="true" visible="true" x="0" y="20" width="414" height="44">
            <XCUIElementTypeButton type="XCUIElementTypeButton" name="icon fanhui" label="icon fanhui" enabled="true" visible="true" x="20" y="24" width="35" height="36"/>
            <XCUIElementTypeOther type="XCUIElementTypeOther" name="小满丨人生最好的状态是小满" label="小满丨人生最好的状态是小满" enabled="true" visible="true" x="94" y="31" width="226" height="21"/>
        </XCUIElementTypeNavigationBar>
    """
    NaviBarFanhuiText = "fanhui"
    fanhuiButtonQuery = {"type":"XCUIElementTypeButton", "nameContains": NaviBarFanhuiText, "enabled": "true"}
    fanhuiButtonQuery["parent_class_chains"] = [ parentNaviBarClassChain ]
    backQueryList.append(fanhuiButtonQuery)

    """
        恒易贷 验证码登录页面 左上角 返回：
            <XCUIElementTypeNavigationBar type="XCUIElementTypeNavigationBar" name="HYDLoginView" enabled="true" visible="true" x="0" y="20" width="414" height="44">
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="back black" label="back black" enabled="true" visible="true" x="20" y="20" width="40" height="44"/>
            </XCUIElementTypeNavigationBar>
    """
    NaviBarBackText = "back"
    naviContainBackQuery = {"type":"XCUIElementTypeButton", "nameContains": NaviBarBackText, "enabled": "true"}
    naviContainBackQuery["parent_class_chains"] = [ parentNaviBarClassChain ]
    backQueryList.append(naviContainBackQuery)

    # case 2: 左上角 导航栏 关闭
    """
        <XCUIElementTypeNavigationBar type="XCUIElementTypeNavigationBar" name="通讯录" enabled="true" visible="true" x="0" y="20" width="375" height="44">
            <XCUIElementTypeButton type="XCUIElementTypeButton" name="关闭" label="关闭" enabled="true" visible="true" x="16" y="20" width="40" height="44"/>
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="187" y="24" width="1" height="36"/>
            <XCUIElementTypeButton type="XCUIElementTypeButton" name="更多" label="更多" enabled="true" visible="true" x="319" y="20" width="40" height="44"/>
        </XCUIElementTypeNavigationBar>
    """
    NaviBarCloseText = "关闭"
    closeButtonQuery = {"type":"XCUIElementTypeButton", "name": NaviBarCloseText, "label": NaviBarCloseText, "enabled": "true"}
    closeButtonQuery["parent_class_chains"] = [ parentNaviBarClassChain ]
    backQueryList.append(closeButtonQuery)

    # case 3: 公众号搜索页的左上角的返回
    """
        <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="true" x="0" y="0" width="414" height="70">
            <XCUIElementTypeButton type="XCUIElementTypeButton" name="返回" label="返回" enabled="true" visible="true" x="0" y="20" width="33" height="50"/>
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="33" y="20" width="381" height="56">
                ...
            </XCUIElementTypeOther>
        </XCUIElementTypeImage>
    """
    SearchReturnName = "返回"
    parentImageClassChain = "/XCUIElementTypeImage[`rect.width = %d`]" % self.X
    searchReturnQuery = {"type":"XCUIElementTypeButton", "name": SearchReturnName, "label": SearchReturnName, "enabled": "true"}
    searchReturnQuery["parent_class_chains"] = [ parentImageClassChain ]
    backQueryList.append(searchReturnQuery)

    # case 3.1: app 益路通行 左上角 返回 按钮， name中包含back
    """
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="20" width="414" height="44">
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="ic back gray" label="ic back gray" enabled="true" visible="true" x="8" y="24" width="40" height="40"/>
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="56" y="29" width="298" height="30">
                    <XCUIElementTypeImage type="XCUIElementTypeImage" name="ic_search_gray.png" enabled="true" visible="false" x="68" y="29" width="30" height="30"/>
                    <XCUIElementTypeTextField type="XCUIElementTypeTextField" value="输入关键字查找创想" label="" enabled="true" visible="true" x="106" y="29" width="232" height="30"/>
                </XCUIElementTypeOther>
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="搜索" label="搜索" enabled="true" visible="true" x="358" y="29" width="46" height="30"/>
            </XCUIElementTypeOther>

            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="20" width="414" height="44">
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="ic back white" label="ic back white" enabled="true" visible="true" x="10" y="22" width="40" height="40"/>
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="374" y="27" width="30" height="30">
                    <XCUIElementTypeImage type="XCUIElementTypeImage" name="ic_search.png" enabled="true" visible="false" x="374" y="27" width="30" height="30"/>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="374" y="27" width="30" height="30"/>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>
    """
    NameContainBackText = "back"
    parentOtherClassChain = "/XCUIElementTypeOther[`rect.width = %d`]" % self.X
    backReturnQuery = {"type":"XCUIElementTypeButton", "nameContains": NameContainBackText, "enabled": "true"}
    backReturnQuery["parent_class_chains"] = [ parentOtherClassChain ]
    backQueryList.append(backReturnQuery)

    # case 4: 全屏显示的图片 full screen image, click any position to back
    """
        <XCUIElementTypeScrollView type="XCUIElementTypeScrollView" enabled="true" visible="true" x="-10" y="0" width="395" height="667">
            <XCUIElementTypeScrollView type="XCUIElementTypeScrollView" enabled="true" visible="true" x="0" y="0" width="375" height="667">
                <XCUIElementTypeImage type="XCUIElementTypeImage" name="关闭" label="关闭" enabled="true" visible="true" x="0" y="127" width="375" height="412"/>
            </XCUIElementTypeScrollView>
        </XCUIElementTypeScrollView>

        <XCUIElementTypeScrollView type="XCUIElementTypeScrollView" enabled="true" visible="true" x="-10" y="0" width="395" height="667">
            <XCUIElementTypeScrollView type="XCUIElementTypeScrollView" enabled="true" visible="true" x="0" y="0" width="375" height="667">
                <XCUIElementTypeImage type="XCUIElementTypeImage" name="关闭" label="关闭" enabled="true" visible="true" x="0" y="17" width="375" height="632"/>
            </XCUIElementTypeScrollView>
        </XCUIElementTypeScrollView>

        <XCUIElementTypeScrollView type="XCUIElementTypeScrollView" enabled="true" visible="true" x="-10" y="0" width="434" height="736">
            <XCUIElementTypeScrollView type="XCUIElementTypeScrollView" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeImage type="XCUIElementTypeImage" name="关闭" label="关闭" enabled="true" visible="true" x="0" y="140" width="414" height="455"/>
            </XCUIElementTypeScrollView>
        </XCUIElementTypeScrollView>
    """
    FullScreenImageText = "关闭"
    parentParentScrollClassChain = "/XCUIElementTypeScrollView[`rect.height = %d`]" % self.totalY
    parentScrollClassChain = "/XCUIElementTypeScrollView[`rect.width = %d AND rect.height = %d`]" % (self.X, self.totalY)
    fullScreenImageQuery = {"type":"XCUIElementTypeImage", "name": FullScreenImageText, "label": FullScreenImageText, "enabled": "true"}
    fullScreenImageQuery["parent_class_chains"] = [ parentParentScrollClassChain, parentScrollClassChain ]
    backQueryList.append(fullScreenImageQuery)

    # case 5: 小程序页面 左上角 返回 按钮
    """
        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="375" height="64">
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="15" y="20" width="30" height="44">
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="返回" label="返回" enabled="true" visible="true" x="15" y="20" width="30" height="44"/>
            </XCUIElementTypeOther>
            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="个人信息" name="个人信息" label="个人信息" enabled="true" visible="true" x="152" y="30" width="71" height="20"/>
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="280" y="24" width="88" height="32">
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="更多" label="更多" enabled="true" visible="true" x="280" y="24" width="44" height="32"/>
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="关闭" label="关闭" enabled="true" visible="true" x="324" y="24" width="44" height="32"/>
            </XCUIElementTypeOther>
        </XCUIElementTypeOther>
    """
    MiniprogramBackButtonText = "返回"
    parentParentOtherClassChain = "/XCUIElementTypeOther[`enabled = 1 AND visible = 1 AND rect.width = %d`]" % self.X
    parentOtherClassChain = "/XCUIElementTypeOther[`enabled = 1 AND visible = 1`]"
    miniprogramBackButtonQuery = {"type": "XCUIElementTypeButton", "name": MiniprogramBackButtonText, "label": MiniprogramBackButtonText, "enabled":"true", "visible":"true"}
    miniprogramBackButtonQuery["parent_class_chains"] = [ parentParentOtherClassChain, parentOtherClassChain ]
    backQueryList.append(miniprogramBackButtonQuery)

    # case 6: 小程序页面 右上角 关闭 按钮
    """
        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="320" y="24" width="87" height="32">
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="320" y="24" width="88" height="32">
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="更多" label="更多" enabled="true" visible="true" x="320" y="24" width="44" height="32"/>
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="关闭" label="关闭" enabled="true" visible="true" x="363" y="24" width="45" height="32"/>
            </XCUIElementTypeOther>
        </XCUIElementTypeOther>
    """
    MiniprogramCloseButtonText = "关闭"
    parentParentOtherClassChain = "/XCUIElementTypeOther[`enabled = 1 AND visible = 1`]"
    parentOtherClassChain = "/XCUIElementTypeOther[`enabled = 1 AND visible = 1`]"
    miniprogramCloseButtonQuery = {"type": "XCUIElementTypeButton", "name": MiniprogramCloseButtonText, "label": MiniprogramCloseButtonText, "enabled":"true", "visible":"true"}
    miniprogramCloseButtonQuery["parent_class_chains"] = [ parentParentOtherClassChain, parentOtherClassChain ]
    backQueryList.append(miniprogramCloseButtonQuery)

    # case 6.1：京东金融 瑞幸咖啡页 图片全屏
    """
        <XCUIElementTypeScrollView type="XCUIElementTypeScrollView" enabled="true" visible="false" x="0" y="64" width="414" height="66">
            <XCUIElementTypeScrollView type="XCUIElementTypeScrollView" enabled="true" visible="false" x="0" y="64" width="414" height="66">
                <XCUIElementTypeButton type="XCUIElementTypeButton" value="1" name="头条" label="头条" enabled="true" visible="false" x="0" y="64" width="207" height="66"/>
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="问答" label="问答" enabled="true" visible="false" x="207" y="64" width="207" height="66"/>
                <XCUIElementTypeImage type="XCUIElementTypeImage" name="/var/containers/Bundle/Application/9D6D5A0D-967F-4411-B1AE-E389C077DB8A/JDJRAppShell.app/JRHomeChannel.bundle/HC_centerSlider_tag@3x.png" enabled="true" visible="false" x="0" y="73" width="18" height="47"/>
                <XCUIElementTypeImage type="XCUIElementTypeImage" name="/var/containers/Bundle/Application/9D6D5A0D-967F-4411-B1AE-E389C077DB8A/JDJRAppShell.app/JRHomeChannel.bundle/HC_centerSlider_Refresh@3x.png" enabled="true" visible="false" x="145" y="81" width="31" height="31"/>
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="90" y="111" width="27" height="5"/>
            </XCUIElementTypeScrollView>
        </XCUIElementTypeScrollView>
    """
    FullScreenImagePartName = "centerSlider"
    parentParentScrollViewClassChain = "/XCUIElementTypeScrollView[`rect.x = 0 AND rect.width = %d`]" % self.X
    parentScrollViewClassChain = "/XCUIElementTypeScrollView[`rect.x = 0 AND rect.width = %d`]" % self.X
    fullScreenImageQuery = {"type": "XCUIElementTypeImage", "nameContains": FullScreenImagePartName, "enabled":"true", "x":"0"}
    fullScreenImageQuery["parent_class_chains"] = [ parentParentScrollViewClassChain, parentScrollViewClassChain ]
    backQueryList.append(fullScreenImageQuery)

    # find and click
    for eachBackQuery in backQueryList:
        # isCurFound, respInfo = self.findElement(query=eachBackQuery)
        SearchBackTimeout = 0.5
        isCurFound, respInfo = self.findElement(query=eachBackQuery, timeout=SearchBackTimeout)
        logging.debug("eachBackQuery=%s -> isCurFound=%s", eachBackQuery, isCurFound)
        if isCurFound:
            curElement = respInfo
            clickOk = self.clickElement(curElement)
            isFoundAndClicked = clickOk

            if isFoundAndClicked:
                break

    if not isFoundAndClicked:
        isFoundAndClicked = self.iOS_special_back_BiYao_TabMine_ReturnToTop()

    if not isFoundAndClicked:
        # try find back area element then click
        page = self.get_page_source()
        # headElementList = self.get_BaseElements(page,head=True)
        # for eachHeadElement in headElementList:
        #     isInBackArea = self.is_element_InCertainArea(eachHeadElement, self.BackImageGivenBounds)
        #     isVisible = self.is_element_visible_iOS(eachHeadElement)
        #     if isInBackArea and isVisible:
        #         self.clickElementCenterPosition(eachHeadElement)
        #         isFoundAndClicked = True
        #         break

        # backElement = self.findUpperLeftBackTypeButtonElement(page)
        backElement, newPage = self.findRealBackElement(page)
        # if backElement:
        if backElement is not None:
            isFoundAndClicked = self.clickElementCenterPosition(backElement)

    return isFoundAndClicked
```

说明：

正常来说，iOS的app的页面，返回前一页，都是点击左上角的返回

但是很多页面比较特殊，其xml内容有各种类型，所以就有了上述的代码，为了兼容各种页面的返回，支持各种特殊格式

相关代码：

```python
def findRealBackElement(self, page):
    """
        对于正常情况下，就是找左上角返回按钮区域内容的按钮，就是真正的返回按钮
        对于个别特殊页面，比如 必要app中商品详情页(其中带 下拉返回商品详情 字样)
            左上角按钮，第一次点击，只是 相当于top按钮，返回页面顶部，而不是返回
                第二次点击返回按钮，才是真正的返回
    """
    newPage = page
    backElement = self.findUpperLeftBackTypeButtonElement(page)
    if self.isiOS:
        # if backElement:
        # foundBack = bool(backElement) # if text=None, will False
        # foundBack = hasattr(backElement, "attrib") # lxml Element
        foundBack = backElement is not None
        if foundBack:
            # 检查是否是特殊的情况：必要app中商品详情页(其中带 下拉返回商品详情 字样)
            DropDownReturnDetailStr = "下拉返回商品详情"
            if DropDownReturnDetailStr in page:
                # <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="↓下拉返回商品详情" name="↓下拉返回商品详情" label="↓下拉返回商品详情" enabled="true" visible="false" x="0" y="9" width="414" height="54"/>
                dropDownReturnDetailQuery = {"type":"XCUIElementTypeStaticText", "nameContains": DropDownReturnDetailStr}
                isFound, foundElement = self.findElement(dropDownReturnDetailQuery, timeout=0.2)
                if isFound:
                    isFoundAndClicked = self.clickElementCenterPosition(backElement)
                    if isFoundAndClicked:
                        newPage = self.get_page_source()
                        backElement = self.findUpperLeftBackTypeButtonElement(newPage)

    return backElement, newPage
```

## search_iOS 点击弹出的键盘中的搜索按钮 触发搜索

```python
def search_iOS(self, wait=1):
    # 触发点击搜索按钮
    foundAndClickedDoSearch = False
    # <XCUIElementTypeButton type="XCUIElementTypeButton" name="Search" label="Search" enabled="true" visible="true" x="281" y="620" width="94" height="47"/>
    # <XCUIElementTypeButton type="XCUIElementTypeButton" name="Search" label="搜索" enabled="true" visible="true" x="309" y="685" width="103" height="50"/>
    # searchButtonQuery = {"name": "Search"}
    searchButtonQuery = {"name": "Search", "type": "XCUIElementTypeButton"}
    # Note: occasionally not found Search, change to find multiple time to avoid this kind of case
    MaxRetryNumber = 5
    curRetryNumber = MaxRetryNumber
    while curRetryNumber > 0:
        foundAndClickedDoSearch = self.findAndClickElement(searchButtonQuery, timeout=wait)
        if foundAndClickedDoSearch:
            break

        curRetryNumber -= 1

    if not foundAndClickedDoSearch:
        logging.error("Not found and/or clicked for %s", searchButtonQuery)
    return foundAndClickedDoSearch
```

调用举例：

```python
isSearchOk = self.search_iOS(wait=0.2)
```

## wait_element_setText_iOS 给元素输入文字，设置值

```python
def wait_element_setText_iOS(self, query, text, isNeedClear=True):
    """iOS set element text

    Args:
        query (dict): wda element query
        text (str): new text to set
        isNeedClear (bool): before set new text, is need clear current value or not
    Returns:
    Raises:
    """
    isInputOk = False
    isFound, respInfo = self.findElement(query=query)
    logging.debug("isFound=%s, respInfo=%s", isFound, respInfo)
    if isFound:
        curElement = respInfo
        if isNeedClear:
            # before set new value, clear current value
            self.iOSClearText(curElement)
        curElement.set_text(text)
        logging.info("has input text: %s", text)
        isInputOk = True
    return isInputOk
```

相关函数：

```python
def iOSClearText(self, curElement):
    """iOS clear current element's text value
        Note: clear_text not working, so need use other workaround to do clear text

    Args:
        curElement (Element): wda element
    Returns:
    Raises:
    """
    # curElement.click()
    # curElement.clear_text()
    # curElement.tap_hold(2.0) # then try select All -> Delete
    backspaceChar = '\b'
    maxDeleteNum = 50
    curElement.set_text(maxDeleteNum * backspaceChar)
    return
```

调用举例：

（1）

对于xml

```xml
<XCUIElementTypeCell type="XCUIElementTypeCell" enabled="true" visible="true" x="0" y="269" width="414" height="46">
    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="269" width="414" height="1"/>
    <XCUIElementTypeTextField type="XCUIElementTypeTextField" value="your_auto_proxy_url" name="URL" label="URL" enabled="true" visible="true" x="72" y="281" width="314" height="21"/>
    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="313" width="414" height="2"/>
    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="15" y="313" width="305" height="1"/>
</XCUIElementTypeCell>
```

调用：

```python
newUrlValue = newAutoProxyValue["url"]
parentCellClassChain = "/XCUIElementTypeCell[`rect.x = 0 AND rect.width = %d`]" % self.X
urlFieldQuery = {"type":"XCUIElementTypeTextField", "name": "URL", "enabled": "true"}
urlFieldQuery["parent_class_chains"] = [ parentCellClassChain ]
# foundUrl, respInfo = self.findElement(urlFieldQuery, timeout=0.1)
# if not foundUrl:
#     return False
isInputUrlOk = self.wait_element_setText_iOS(urlFieldQuery, newUrlValue)
```

（2）

```python
newAuthUserValue = newManualProxyValue["authUser"]
userFieldQuery = {"type":"XCUIElementTypeTextField", "name": "用户名", "enabled": "true"}
userFieldQuery["parent_class_chains"] = [ parentCellClassChain ]
isInputUserOk = self.wait_element_setText_iOS(userFieldQuery, newAuthUserValue)
```
