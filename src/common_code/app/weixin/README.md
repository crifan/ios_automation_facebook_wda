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
