# 元素处理

此处整理用wda查找元素、点击元素等常见代码段。

## getiOSElementQuery 生成wda的元素的query

```python
def getiOSElementQuery(self, locator):
    """from element locator to generate iOS element query"""
    query = None
    # if isinstance(locator, list):
    #     logging.error("TODO: add support in future for locator is list in getiOSElementQuery")
    # elif isinstance(locator, dict):
    # {'name': '公众号'}
    # query = {}
    # query = locator
    query = copy.deepcopy(locator)

    # Note: not auto convert key for compatible old Android key
    # {'text': '通讯录'}, {'desc': '添加'}
    # locatorText = locator.get("text")
    # locatorDesc = locator.get("desc")
    # if locatorText:
    #     query = {"name": locatorText}
    # elif locatorDesc:
    #     query = {"label": locatorDesc}
    # else:
    #     logging.warning("TODO: add support later for %s", locator)

    # auto add type for iOS
    hasTag = "tag" in query.keys()
    noType = "type" not in query.keys()
    if hasTag and noType:
        query["type"] = query["tag"]
        del query["tag"]

    # 20200402: facebook-wda+WebDriverAgent internally
    #     (1) not support visible
    #     (2) sometime support accessible
    # -> now, not use visible anymore
    # 20200430: updated latest WebDriverAgent source code, now has supported visible
    #     so above not need delete visible
    # 20200430: but after add visible, still not find (some) element, so remove again
    if "visible" in query.keys():
        del query["visible"]

    logging.debug("locator=%s -> query=%s", locator, query)
    return query
```

说明：

对于输入的query的dict，进行一定处理：

1. 去掉visible
    * 截止20200615，WebDriverAgent内部还是不能完美支持元素的visible值的判断
    * -》有时候支持 有时候不支持，导致传入visible=true，却找不到元素
    * -》所以去掉visible
2. 把tag改为type

调用举例：

```python
def isFoundElement_iOS(self, locator):
    isFound = False
    foundElement = None
    query = self.getiOSElementQuery(locator)
    if query:
        isFound, foundElement = self.findElement(query=query)
    return isFound, foundElement
```

## findElement 查找元素

```python
# def findElement(self, query={}, timeout=WaitFind):
# def findElement(self, query={}, timeout=WaitFind, isRetryNoYWhenFail=False):
def findElement(self, query={}, timeout=WaitFind, isRetryNoYWhenFail=True):
    """Find element from query

    Args:
        query (dict): query condition
        timeout(float): max wait time
        isRetryNoYWhenFail(bool): True to do retry but remove y from query when found fail, False to not retry
    Returns:
        bool, Element/str
            True, Element
            False, error message
    Raises:
    """
    logging.debug("query=%s", query)
    isFound, respInfo = False, "Unkown error"

    elementSelector = self.curSession(**query, timeout=timeout)
    logging.debug("elementSelector=%s", elementSelector)
    isFound, respInfo = elementSelector.get()
    logging.debug("query=%s -> isFound=%s, respInfo=%s", query, isFound, respInfo)
    if not isFound:
        if isRetryNoYWhenFail and ("y" in query):
            # Note:
            # for sometime / many cases:
            #     not found element due to y position is not correct but other propery(x, width, height, name, ...) all correct
            #     for these cases, retry to find without y
            noYQuery = copy.deepcopy(query)
            del noYQuery["y"]
            elementSelectorNoY = self.curSession(**noYQuery, timeout=timeout)
            logging.debug("elementSelectorNoY=%s", elementSelectorNoY)
            isFound, respInfo = elementSelectorNoY.get()
            logging.debug("retry no y: noYQuery=%s -> isFound=%s, respInfo=%s", noYQuery, isFound, respInfo)
            # somtime here found out element but y is negative:
            # <XCUIElementTypeImage type="XCUIElementTypeImage" name="main_avatar" enabled="true" visible="false" x="16" y="-146" width="35" height="36"/>
            # actually is not visible -> not our expected -> should filter out, consider as not found
            if isFound:
                curRect = respInfo.bounds
                logging.debug("curRect=%s", curRect)
                isValidY = curRect.y >= 0
                isVisible = respInfo.visible
                logging.debug("isVisible=%s", isVisible)
                isReallyFound = isValidY and isVisible
                logging.debug("isReallyFound=%s", isReallyFound)
                if not isReallyFound:
                    isFound = False
                    respInfo = respInfo

    return isFound, respInfo
```

调用举例：

（1）简单的

（2）复杂的，带parent的class chain：设置 WiFi列表页 中查找 无线局域网

对于xml

```xml
<XCUIElementTypeNavigationBar type="XCUIElementTypeNavigationBar" name="无线局域网" enabled="true" visible="true" x="0" y="20" width="414" height="44">
    <XCUIElementTypeButton type="XCUIElementTypeButton" name="设置" label="设置" enabled="true" visible="true" x="0" y="20" width="66" height="44"/>
    <XCUIElementTypeOther type="XCUIElementTypeOther" name="无线局域网" label="无线局域网" enabled="true" visible="true" x="163" y="31" width="88" height="21"/>
</XCUIElementTypeNavigationBar>
```

写法是：

```python
wifiName = "无线局域网"
parentNaviBarClassChain = "/XCUIElementTypeNavigationBar[`name = '%s' AND rect.x = 0 AND rect.width = %d`]" % (wifiName, self.X)
wifiQuery = {"type":"XCUIElementTypeOther", "name": wifiName, "enabled": "true"}
wifiQuery["parent_class_chains"] = [ parentNaviBarClassChain ]
isFoundWifi, respInfo = self.findElement(query=wifiQuery, timeout=0.1)
```

和：

```xml
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
```

代码 判断iOS中页面是否处于 微信：

```python
weixinTabQuery = {"type":"XCUIElementTypeButton", "name": "微信", "label": "微信", "enabled": "true"}
weixinTabQuery["parent_class_chains"] = [ tabBarClassChain ]
isFoundWeixin, respInfo = self.findElement(query=weixinTabQuery, timeout=0.2)
iOSisInWeixin = isFoundWeixin
return iOSisInWeixin
```

（3）复杂的，name中包含的

```xml
<XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="↓下拉返回商品详情" name="↓下拉返回商品详情" label="↓下拉返回商品详情" enabled="true" visible="false" x="0" y="9" width="414" height="54"/>
```

写法：

```python
DropDownReturnDetailStr = "下拉返回商品详情"
dropDownReturnDetailQuery = {"type":"XCUIElementTypeStaticText", "nameContains": DropDownReturnDetailStr}
isFound, foundElement = self.findElement(dropDownReturnDetailQuery, timeout=0.2)
```

* AppStore 详情页 不折叠输入法 带金额的购买按钮 ¥1.00：

```xml
<XCUIElementTypeCollectionView type="XCUIElementTypeCollectionView" enabled="true" visible="true" x="0" y="0" width="414" height="736">
    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="0" y="-672" width="414" height="736"/>
    <XCUIElementTypeCell type="XCUIElementTypeCell" enabled="true" visible="true" x="0" y="64" width="414" height="211">
        <XCUIElementTypeImage type="XCUIElementTypeImage" value="不折叠输入法" name="插图" label="插图" enabled="true" visible="true" x="20" y="64" width="118" height="118"/>
        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="不折叠输入法" name="不折叠输入法" label="不折叠输入法" enabled="true" visible="true" x="154" y="71" width="134" height="27"/>
        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="JUNJIE HUANG" name="JUNJIE HUANG" label="JUNJIE HUANG" enabled="true" visible="false" x="154" y="101" width="108" height="19"/>
        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="新版发文不再一行的小键盘" name="新版发文不再一行的小键盘" label="新版发文不再一行的小键盘" enabled="true" visible="true" x="154" y="101" width="184" height="19"/>
        <XCUIElementTypeButton type="XCUIElementTypeButton" name="¥1.00" label="¥1.00" enabled="true" visible="true" x="154" y="152" width="74" height="30"/>
        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="App 内购买项目" name="App 内购买项目" label="App 内购买项目" enabled="true" visible="true" x="234" y="152" width="62" height="30"/>
        <XCUIElementTypeButton type="XCUIElementTypeButton" name="更多" label="更多" enabled="true" visible="true" x="366" y="154" width="28" height="28"/>
        <XCUIElementTypeOther type="XCUIElementTypeOther" name="三颗星, 793个评分, 3, 工具, 4+, 年龄" label="三颗星, 793个评分, 3, 工具, 4+, 年龄" enabled="true" visible="true" x="20" y="202" width="374" height="50"/>
    </XCUIElementTypeCell>
```

查找写法是：

```python
parentCellClassChain = "/XCUIElementTypeCell[`rect.x = 0 AND rect.width = %d`]" % self.X
moneyButtonQuery = {"type":"XCUIElementTypeButton", "nameContains": "¥", "enabled": "true"}
moneyButtonQuery["parent_class_chains"] = [ parentCellClassChain ]
foundMoneyButton, respInfo = self.findElement(query=moneyButtonQuery, timeout=0.1)
```

（4）复杂的，name符合条件的

场景：寻找 微信公众号列表页中 上下滚动后的 顶部浮动的大写字母横条的元素

```xml
<XCUIElementTypeOther type="XCUIElementTypeOther" name="D" enabled="true" visible="true" x="0" y="64" width="375" height="22">
    <XCUIElementTypeOther type="XCUIElementTypeOther" name="D" label="D" enabled="true" visible="true" x="10" y="64" width="12" height="22"/>
</XCUIElementTypeOther>

<XCUIElementTypeOther type="XCUIElementTypeOther" name="E" enabled="true" visible="true" x="0" y="64" width="375" height="22">
    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="E" name="E" label="E" enabled="true" visible="true" x="10" y="64" width="10" height="22"/>
</XCUIElementTypeOther>

<XCUIElementTypeOther type="XCUIElementTypeOther" name="J" enabled="true" visible="true" x="0" y="64" width="375" height="22">
    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="J" name="J" label="J" enabled="true" visible="true" x="10" y="64" width="9" height="22"/>
</XCUIElementTypeOther>
```

代码：

```python
floatUpperLetterElement = None
# floatUpperLetterQuery = {"type":"XCUIElementTypeStaticText", "valueMatches":"^[A-Z]$"}
# isFound, foundElement = findElement(curSession, floatUpperLetterQuery)
floatUpperLetterQuery = {"type":"XCUIElementTypeOther", "nameMatches":"^[A-Z]$", "enabled":"true", "x":"0", "width": "%s" % self.X}
isFound, foundElement = self.findElement(floatUpperLetterQuery)
```

（5）复杂的，value包含的

```python
curNoticeQuery = {"type":"XCUIElementTypeStaticText", "valueContains": curStepNotice, "enabled": "true"}
curNoticeQuery["parent_class_chains"] = [scrollViewClassChain]
isFoundCurNotice, respInfo = self.findElement(query=curNoticeQuery, timeout=0.5)
```

## findAndClickElement 查找并点击元素

```python
# def findAndClickElement(self, query={}, timeout=WaitFind, enableDebug=False):
# def findAndClickElement(self, query={}, timeout=WaitFind, isShowErrLog=True):
# def findAndClickElement(self, query={}, timeout=WaitFind, isShowErrLog=True, isRetryNoYWhenFail=True):
def findAndClickElement(self, query={}, timeout=WaitFind, isShowErrLog=True):
    """Find and click element

    Args:
        query (dict): query parameter for find element
        timeout (float): max timeout seconds for find element
        enableDebug (bool): if enable debug, then draw clicked rectange for current screen
        isShowErrLog(bool): True to show error log, False to not show logging.error when error
    Returns:
        bool
    Raises:
    """
    foundAnClicked = False

    # isFound, respInfo = self.findElement(query=query, timeout=timeout)
    # isFound, respInfo = self.findElement(query=query, timeout=timeout, isRetryNoYWhenFail=isRetryNoYWhenFail)
    isFound, respInfo = self.findElement(query=query, timeout=timeout)
    logging.debug("isFound=%s, respInfo=%s", isFound, respInfo)
    if isFound:
        curElement = respInfo

        # if enableDebug:
        #     # for debug
        #     curScreenFile = debugSaveScreenshot(curScale=self.curSession.scale)
        #     utils.imageDrawRectangle(curScreenFile, rectLocation=curElement.bounds)

        clickOk = self.clickElement(curElement)
        logging.info("%s to Clicked element %s", clickOk, query)

        foundAnClicked = clickOk
    else:
        if isShowErrLog:
            logging.error("Not found element %s", query)
        else:
            logging.debug("Not found element %s", query)

    return foundAnClicked
```

调用举例：

（1）

```python
# <XCUIElementTypeButton type="XCUIElementTypeButton" name="Search" label="Search" enabled="true" visible="true" x="281" y="620" width="94" height="47"/>
# <XCUIElementTypeButton type="XCUIElementTypeButton" name="Search" label="搜索" enabled="true" visible="true" x="309" y="685" width="103" height="50"/>
# searchButtonQuery = {"name": "Search"}
searchButtonQuery = {"name": "Search", "type": "XCUIElementTypeButton"}

foundAndClickedDoSearch = self.findAndClickElement(searchButtonQuery, timeout=1.0)
```

（2）场景：设置 WiFi 配置代理 从手动切换到 关闭 存储

xml:

```xml
<XCUIElementTypeNavigationBar type="XCUIElementTypeNavigationBar" name="配置代理" enabled="true" visible="true" x="0" y="20" width="414" height="44">
    <XCUIElementTypeButton type="XCUIElementTypeButton" name="xxx_guest_5G" label="xxx_guest_5G" enabled="true" visible="true" x="0" y="20" width="155" height="44"/>
    <XCUIElementTypeOther type="XCUIElementTypeOther" name="配置代理" label="配置代理" enabled="true" visible="true" x="172" y="31" width="70" height="21"/>
    <XCUIElementTypeButton type="XCUIElementTypeButton" name="存储" label="存储" enabled="true" visible="true" x="359" y="20" width="43" height="44"/>
</XCUIElementTypeNavigationBar>
```

代码：

```python
storeName = "存储"
parentNaviBarClassChain = "/XCUIElementTypeNavigationBar[`name = '配置代理' AND rect.x = 0 AND rect.width = %d`]" % self.X
storeButtonQuery = {"type":"XCUIElementTypeButton", "name": storeName, "enabled": "true"}
storeButtonQuery["parent_class_chains"] = [ parentNaviBarClassChain ]
foundAndClickedStore = self.findAndClickElement(query=storeButtonQuery, timeout=0.1)
```

（3）isShowErrLog=False 当找不到，不要报错

场景：

AppStore 详情页 淘宝 弹框 安装

xml

```xml
<XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="515" width="414" height="221">
    <XCUIElementTypeTable type="XCUIElementTypeTable" enabled="true" visible="true" x="0" y="515" width="414" height="120">
        <XCUIElementTypeCell type="XCUIElementTypeCell" enabled="true" visible="true" x="0" y="515" width="414" height="77">
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="16" y="590" width="398" height="1"/>
            <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="55" y="533" width="40" height="41"/>
            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="手机淘宝 - 淘到你说好 ￼ TAOBAO (CHINA) SOFTWARE CO.,LTD APP" name="手机淘宝 - 淘到你说好 ￼ TAOBAO (CHINA) SOFTWARE CO.,LTD APP" label="手机淘宝 - 淘到你说好 ￼ TAOBAO (CHINA) SOFTWARE CO.,LTD APP" enabled="true" visible="true" x="110" y="530" width="234" height="48"/>
        </XCUIElementTypeCell>
        <XCUIElementTypeCell type="XCUIElementTypeCell" enabled="true" visible="true" x="0" y="591" width="414" height="45">
            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="帐户" name="帐户" label="帐户" enabled="true" visible="true" x="16" y="606" width="79" height="16"/>
            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="xxx@xxx.com" name="xxx@xxx.com" label="xxx@xxx.com" enabled="true" visible="true" x="111" y="606" width="134" height="16"/>
        </XCUIElementTypeCell>
    </XCUIElementTypeTable>
    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="635" width="414" height="101">
        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="16" y="635" width="398" height="1"/>
        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="655" width="414" height="69"/>
        <XCUIElementTypeButton type="XCUIElementTypeButton" name="安装" label="安装" enabled="true" visible="true" x="165" y="667" width="84" height="37"/>
    </XCUIElementTypeOther>
</XCUIElementTypeOther>
```

代码：

```python
parentOtherClassChain = "/XCUIElementTypeOther[`rect.x = 0 AND rect.width = %d`]" % self.X
installButtonQuery = {"type":"XCUIElementTypeButton", "name": "安装", "enabled": "true"}
installButtonQuery["parent_class_chains"] = [ parentOtherClassChain ]
foundAndClickedInstall = self.findAndClickElement(query=installButtonQuery, timeout=0.1, isShowErrLog=False)
```

## findAndClickButtonElementBySoup

```python
    def findAndClickButtonElementBySoup(self, curButtonSoup=None, curButtonName=None):
        """
            iOS的bug：根据bs找到了soup元素（往往是一个button）后，用 clickCenterPosition=clickElementCenterPosition 去点击中间坐标，往往会有问题
                实际上点击的是别的位置，别的元素
            为了规避此bug，所以去：
                通过soup，再去找button的wda的元素，然后根据元素去点击
                    则都是可以正常点击，不会有误点击的问题
        """
        # # change to wda element query then click by element
        # if not curButtonName:
        #     curSoupAttrs = curButtonSoup.attrs
        #     curButtonName = curSoupAttrs["name"]
        # # rights close white
        # # login close
        # curButtonQuery = {"type":"XCUIElementTypeButton", "enabled":"true", "name": curButtonName}
        # # foundAndClicked = self.findAndClickElement(curButtonQuery)

        curButtonQuery = {"type":"XCUIElementTypeButton", "enabled":"true"}
        extraQuery = {}

        # change to wda element query then click by element
        if curButtonName:
            extraQuery["name"] = curButtonName
        else:
            if curButtonSoup:
                curSoupAttrs = curButtonSoup.attrs
                if hasattr(curSoupAttrs, "name"):
                    curButtonName = curSoupAttrs["name"]
                    # rights close white
                    # login close
                    extraQuery["name"] = curButtonName
                else:
                    # no name attribute, use position
                    x = curSoupAttrs["x"]
                    y = curSoupAttrs["y"]
                    width = curSoupAttrs["width"]
                    height = curSoupAttrs["height"]
                    extraQuery["x"] = x
                    extraQuery["y"] = y
                    extraQuery["width"] = width
                    extraQuery["height"] = height
                    # {'enabled': 'true', 'height': '32', 'type': 'XCUIElementTypeButton', 'width': '31', 'x': '339', 'y': '122'}

        # merge query 
        # curButtonQuery = {**curButtonQuery, **extraQuery}
        curButtonQuery.update(extraQuery) # {'enabled': 'true', 'height': '32', 'type': 'XCUIElementTypeButton', 'width': '32', 'x': '338', 'y': '150'}

        foundAndClicked = self.findAndClickElement(curButtonQuery, isShowErrLog=False)
        return foundAndClicked
```

不同的地方的各种调用：

```python
foundAndProcessedPopup = self.findAndClickButtonElementBySoup(possibleCloseSoup)

foundAndProcessedPopup = self.findAndClickButtonElementBySoup(commonCloseSoup)

foundAndProcessedPopup = self.findAndClickButtonElementBySoup(wifiCellularSoup)

foundAndProcessedPopup = self.findAndClickButtonElementBySoup(okSoup)

foundAndProcessedPopup = self.findAndClickButtonElementBySoup(allowSoup)

foundAndProcessedPopup = self.findAndClickButtonElementBySoup(curButtonName="取消")
```

详见：

【已解决】自动抓包iOS的app：优化clickElementCenterPosition点击失效时换用wda寻找元素并点击逻辑

## findAndClickCenterPosition 查找并点击元素中间位置

```python
def findAndClickCenterPosition(self, bsChainList, soup=None, isUseWdaQueryAndClick=False):
    """use Beautifulsoup chain list to find soup node then click node center position

    Args:
        bsChainList (list): dict list for dict of tag and attrs
        soup (Soup)): BeautifulSoup soup
        isUseWdaQueryAndClick (bool): for special node bs click not work, so need change to wda query element then click by element
    Returns:
        bool: found and cliked or not
    Raises:
    """
    foundAndClicked = False
    if not soup:
        curPageXml = self.get_page_source()
        soup = CommonUtils.xmlToSoup(curPageXml)
    foundSoup = CommonUtils.bsChainFind(soup, bsChainList)
    if foundSoup:
        if isUseWdaQueryAndClick:
            foundAndClicked = self.findAndClickButtonElementBySoup(foundSoup)
        else:
            self.clickElementCenterPosition(foundSoup)
            foundAndClicked = True

    return foundAndClicked
```

场景：米家 弹框 立即体验 xml

```xml
<XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
            <XCUIElementTypeImage type="XCUIElementTypeImage" name="sort_guide_icon" enabled="true" visible="false" x="54" y="115" width="306" height="310"/>
            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="设备排序全新升级" name="设备排序全新升级" label="设备排序全新升级" enabled="true" visible="true" x="111" y="426" width="192" height="34"/>
            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="1.新增快速置顶，排序操作更方便 2.顶部排序固定，不会被随意打乱" name="1.新增快速置顶，排序操作更方便 2.顶部排序固定，不会被随意打乱" label="1.新增快速置顶，排序操作更方便 2.顶部排序固定，不会被随意打乱" enabled="true" visible="true" x="95" y="468" width="224" height="42"/>
            <XCUIElementTypeButton type="XCUIElementTypeButton" name="立即体验" label="立即体验" enabled="true" visible="true" x="24" y="657" width="366" height="43"/>
        </XCUIElementTypeOther>
    </XCUIElementTypeOther>
</XCUIElementTypeOther>
```

调用举例：

```python
immediatelyExperienceChainList = [
    {
        "tag": "XCUIElementTypeOther",
        "attrs": self.FullScreenAttrDict
    },
    {
        "tag": "XCUIElementTypeOther",
        "attrs": self.FullScreenAttrDict
    },
    {
        "tag": "XCUIElementTypeButton",
        "attrs": {"enabled":"true", "visible":"true", "name":"立即体验"}
    },
]
foundAndProcessedPopup = self.findAndClickCenterPosition(immediatelyExperienceChainList, soup)
return foundAndProcessedPopup
```

## findAndClickButtonElementBySoup 通过soup去用wda的query查找元素并点击

```python
def findAndClickButtonElementBySoup(self, curButtonSoup=None, curButtonName=None):
    """
        iOS的bug：根据bs找到了soup元素（往往是一个button）后，用 clickCenterPosition=clickElementCenterPosition 去点击中间坐标，往往会有问题
            实际上点击的是别的位置，别的元素
        为了规避此bug，所以去：
            通过soup，再去找button的wda的元素，然后根据元素去点击
                则都是可以正常点击，不会有误点击的问题
    """
    # # change to wda element query then click by element
    # if not curButtonName:
    #     curSoupAttrs = curButtonSoup.attrs
    #     curButtonName = curSoupAttrs["name"]
    # # rights close white
    # # login close
    # curButtonQuery = {"type":"XCUIElementTypeButton", "enabled":"true", "name": curButtonName}
    # # foundAndClicked = self.findAndClickElement(curButtonQuery)

    curButtonQuery = {"type":"XCUIElementTypeButton", "enabled":"true"}
    extraQuery = {}

    # change to wda element query then click by element
    if curButtonName:
        extraQuery["name"] = curButtonName
    else:
        if curButtonSoup:
            curSoupAttrs = curButtonSoup.attrs
            if hasattr(curSoupAttrs, "name"):
                curButtonName = curSoupAttrs["name"]
                # rights close white
                # login close
                extraQuery["name"] = curButtonName
            else:
                # no name attribute, use position
                x = curSoupAttrs["x"]
                y = curSoupAttrs["y"]
                width = curSoupAttrs["width"]
                height = curSoupAttrs["height"]
                extraQuery["x"] = x
                extraQuery["y"] = y
                extraQuery["width"] = width
                extraQuery["height"] = height
                # {'enabled': 'true', 'height': '32', 'type': 'XCUIElementTypeButton', 'width': '31', 'x': '339', 'y': '122'}

    # merge query 
    # curButtonQuery = {**curButtonQuery, **extraQuery}
    curButtonQuery.update(extraQuery) # {'enabled': 'true', 'height': '32', 'type': 'XCUIElementTypeButton', 'width': '32', 'x': '338', 'y': '150'}

    foundAndClicked = self.findAndClickElement(curButtonQuery, isShowErrLog=False)
    return foundAndClicked
```

说明：

* iOS的bug：根据bs找到了soup元素（往往是一个button）后，用 clickCenterPosition=clickElementCenterPosition 去点击中间坐标，往往会有问题
    * 实际上点击的是别的位置，别的元素
* 为了规避此bug，所以去：
    * 通过soup，再去找button的wda的元素，然后根据元素去点击
        * 则都是可以正常点击，不会有误点击的问题

调用举例：

```python
commonCloseSoup = CommonUtils.bsChainFind(soup, commonCloseChainList)
if commonCloseSoup:
    # self.clickElementCenterPosition(commonCloseSoup) # sometime not work
    # foundAndProcessedPopup = True

    # so change to wda query element then click
    foundAndProcessedPopup = self.findAndClickButtonElementBySoup(commonCloseSoup)
```

## clickElementCenterPosition 点击元素（通过属性中找到元素坐标，点击中间位置）

```python
def clickElementCenterPosition(self, curElement):
    """Click center position of element

    Args:
        curElement (Element): Beautiful soup / lxml element / wda Element
    Returns:
        bool
    Raises:
    """
    hasClicked = False
    # centerPos = None
    centerX = None
    centerY = None

    hasBounds = hasattr(curElement, "bounds")
    curBounds = None
    if hasBounds:
        curBounds = curElement.bounds

    if hasBounds and curBounds:
        # wda element
        if hasattr(curBounds, "center"):
            # is wda Rect
            curRect = curBounds
            rectCenter = curRect.center
            centerX = rectCenter[0]
            centerY = rectCenter[1]
    else:
        attrDict = None
        if hasattr(curElement, "attrs"):
            # Beautiful soup node
            attrDict = curElement.attrs
        elif hasattr(curElement, "attrib"):
            # lxml element
            attrDict = dict(curElement.attrib)

        if attrDict:
            logging.info("attrDict=%s", attrDict)
            hasCoordinate = ("x" in attrDict) and ("y" in attrDict) and ("width" in attrDict) and ("height" in attrDict)
            if hasCoordinate:
                x = int(attrDict["x"])
                y = int(attrDict["y"])
                width = int(attrDict["width"])
                height = int(attrDict["height"])
                centerX = x + int(width / 2)
                centerY = y + int(height / 2)

    if centerX and centerY:
        centerPos = (centerX, centerY)
        self.tap(centerPos)
        logging.info("Clicked center position: %s", centerPos)
        hasClicked = True

    return hasClicked
```

调用举例：

```python
confirmSoup = parentOtherSoup.find(
    "XCUIElementTypeButton",
    attrs={"visible":"true", "enabled":"true", "name": "确定"}
)
if confirmSoup:
    self.clickElementCenterPosition(confirmSoup)
    foundAndProcessedPopup = True
```
