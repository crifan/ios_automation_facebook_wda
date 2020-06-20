# 常用代码段

在折腾`facebook-wda`期间，把各种常用的功能，封装成了函数，现整理如下，供需要的参考：

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
