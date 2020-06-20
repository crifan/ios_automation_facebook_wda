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


