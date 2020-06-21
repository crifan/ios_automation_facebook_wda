# 首次登录引导页

首次登录app时，引导页，多次左滑进入app主页

```python
def swipeLeftGuideToMain(self):
    """
        处理iOS中app，在首次登录后，引导页，需要多次左滑，最后进入主页
    """

    """
        途虎养车 左滑引导页 1页/3页：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                    <XCUIElementTypeScrollView type="XCUIElementTypeScrollView" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                        <XCUIElementTypeImage type="XCUIElementTypeImage" name="guide0" enabled="true" visible="true" x="0" y="0" width="414" height="736"/>
                    </XCUIElementTypeScrollView>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" value="1" name="loading sign nonsel" label="loading sign nonsel" enabled="true" visible="true" x="177" y="630" width="10" height="10"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="loading sign nonsel" label="loading sign nonsel" enabled="true" visible="true" x="202" y="630" width="10" height="10"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="loading sign nonsel" label="loading sign nonsel" enabled="true" visible="true" x="227" y="630" width="10" height="10"/>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>
        
        途虎养车 左滑引导页 2页/3页：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                    <XCUIElementTypeScrollView type="XCUIElementTypeScrollView" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                        <XCUIElementTypeImage type="XCUIElementTypeImage" name="guide1" enabled="true" visible="true" x="0" y="0" width="414" height="736"/>
                    </XCUIElementTypeScrollView>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="loading sign nonsel" label="loading sign nonsel" enabled="true" visible="true" x="177" y="630" width="10" height="10"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" value="1" name="loading sign nonsel" label="loading sign nonsel" enabled="true" visible="true" x="202" y="630" width="10" height="10"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="loading sign nonsel" label="loading sign nonsel" enabled="true" visible="true" x="227" y="630" width="10" height="10"/>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>
        
        途虎养车 左滑引导页 3页/3页 立即进入：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                    <XCUIElementTypeScrollView type="XCUIElementTypeScrollView" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                        <XCUIElementTypeImage type="XCUIElementTypeImage" name="guide2" enabled="true" visible="true" x="0" y="0" width="414" height="736"/>
                    </XCUIElementTypeScrollView>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="loading btn" label="loading btn" enabled="true" visible="true" x="141" y="628" width="132" height="30"/>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>
    """
    # GuideText = "guide"
    # parentScrollViewClassChain = "/XCUIElementTypeScrollView[`rect.x = 0 AND rect.y = 0 AND rect.width = %d AND rect.height = %d`]" % (ScreenX, ScreenY)
    # guideImgeQuery = {"type":"XCUIElementTypeImage", "nameContains": GuideText, "enabled": "true", "x":"0", "y":"0", "width": "%s" % ScreenX, "height":"%s" % ScreenY}
    # guideImgeQuery["parent_class_chains"] = [ parentScrollViewClassChain ]
    # isFound, guideImgeElement = findElement(curSession, guideImgeQuery, timeout=0.1)
    # if isFound:
    #     # foundAndClicked = clickElement(curSession, guideImgeElement)
    #     swipeLeft(curSession)

    foundAndSwipeGuideToMain = False

    swipeNum = 0

    guideP = re.compile("guide") # guide0, guide1, guide2
    guideImageChainList = [
        {
            "tag": "XCUIElementTypeOther",
            "attrs": self.FullScreenAttrDict,
        },
        {
            "tag": "XCUIElementTypeScrollView",
            "attrs": self.FullScreenAttrDict,
        },
        {
            "tag": "XCUIElementTypeImage",
            "attrs": {"name": guideP, **self.FullScreenAttrDict}
        },
    ]

    while True:
        curPageXml = self.get_page_source()
        soup = CommonUtils.xmlToSoup(curPageXml)

        guideImageSoup = CommonUtils.bsChainFind(soup, guideImageChainList)
        if not guideImageSoup:
            break

        parentScrollViewSoup = guideImageSoup.parent
        if not parentScrollViewSoup:
            break

        validButtonSoupList = []

        nextSiblingList = parentScrollViewSoup.next_siblings
        for eachNextSiblingSoup in nextSiblingList:
            if hasattr(eachNextSiblingSoup, "attrs"):
                soupAttrDict = eachNextSiblingSoup.attrs # {'enabled': 'true', 'height': '30', 'label': 'loading btn', 'name': 'loading btn', 'type': 'XCUIElementTypeButton', 'visible': 'true', 'width': '132', 'x': '141', 'y': '628'}
                soupType = soupAttrDict.get("type") # 'XCUIElementTypeButton'
                soupName = soupAttrDict.get("name") # 'loading btn'
                soupVisible = soupAttrDict.get("visible") # 'true'
                isButton = soupType == "XCUIElementTypeButton"
                # isLoadingName = bool(re.search(soupName, "loading"))
                isLoadingName = bool(re.search("loading", soupName))
                isVisible = soupVisible == "true"
                isValid = isButton and isLoadingName and isVisible
                if isValid:
                    validButtonSoupList.append(eachNextSiblingSoup)

        if validButtonSoupList:
            validButtonNum = len(validButtonSoupList)
            if validButtonNum == 1:
                # end page, click button, into main page
                lastPageButtonSoup = validButtonSoupList[0]
                self.clickElementCenterPosition(lastPageButtonSoup)
                foundAndSwipeGuideToMain = True
                break
            elif validButtonNum > 1:
                swipeNum += 1
                # not end, should continue to swipe left
                self.swipe("SwipeLeft")
                logging.info("Swipe left for guide page %d", swipeNum)

    return foundAndSwipeGuideToMain
```

对应页面：

* 恒易贷
  * 第一页：
    * ![hengyidai_left_guide_page_1](../../../assets/img/hengyidai_left_guide_page_1.jpg)
  * 最后一页：
    * ![hengyidai_left_guide_page_3](../../../assets/img/hengyidai_left_guide_page_3.jpg)
* 途虎养车
  * 第一页
    * ![tuhucar_left_guide_3_1](../../../assets/img/tuhucar_left_guide_3_1.jpg)
  * 第二页
    * ![tuhucar_left_guide_3_2](../../../assets/img/tuhucar_left_guide_3_2.jpg)
  * 第三页
    * ![tuhucar_left_guide_3_3](../../../assets/img/tuhucar_left_guide_3_3.jpg)
