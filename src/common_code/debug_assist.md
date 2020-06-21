# 调试辅助

在开发期间，往往要调试一些内容，比如页面元素有哪些，位置大小等如何，所以会想要一些函数，可以给图片上元素加框显示等等，这类函数，属于方便调试，辅助调试方面的内容。

之前实现了很多复制调试的函数，如下，供参考。

## debugDrawElementRect debugDrawScreenRect 给图片画框

```python
def debugDrawScreenRect(self, curRect, curImgPath=None, isShow=False, isAutoSave=True, isDrawClickedPosCircle=False):
    """for debug, draw rectange for current screenshot"""
    if not curImgPath:
        curImgPath = self.getCurScreenshot()

    curImg = CommonUtils.imageDrawRectangle(
        curImgPath,
        curRect,
        isShow=isShow,
        isAutoSave=isAutoSave,
        isDrawClickedPosCircle=isDrawClickedPosCircle,
    )

    return curImg

def debugDrawElementRect(self, elementList, curImgPath=None, isShowEach=False, isSaveEach=True, isDrawInSinglePic=False):
    """for debug, to draw rectange for each element in current screenshot"""
    if not curImgPath:
        curImgPath = self.getCurScreenshot()

    curImg = Image.open(curImgPath)

    for eachElement in elementList:
        curBoundList = self.get_ElementBounds(eachElement)
        curWidth = curBoundList[2] - curBoundList[0]
        curHeight = curBoundList[3] - curBoundList[1]
        curRect = [curBoundList[0], curBoundList[1], curWidth, curHeight]
        curTimeStr = CommonUtils.getCurDatetimeStr("%H%M%S")
        curSaveTal = "_rect_{}_%x|%y|%w|%h".format(curTimeStr) # '_rect_155618_%x|%y|%w|%h'
        curInputImg = None
        if isDrawInSinglePic:
            curInputImg = curImg
        else:
            curInputImg = curImgPath
        curImg = CommonUtils.imageDrawRectangle(
            curInputImg,
            curRect,
            isShow=isShowEach,
            isAutoSave=isSaveEach,
            saveTail=curSaveTal,
            isDrawClickedPosCircle=False,
        )

    # always save final result
    curTimeStr = CommonUtils.getCurDatetimeStr("%H%M%S")
    finalSaveTail = "_rect_all_%s" % curTimeStr
    imgFolderAndName, pointSuffix = os.path.splitext(curImgPath)
    imgFolderAndName = imgFolderAndName + finalSaveTail
    finalImgPath = imgFolderAndName + pointSuffix
    curImg.save(finalImgPath)

    return
```

调用：

```python
# # for debug
# self.debugDrawScreenRect(curRect=bounds)
```

和：

```python
Elements = self.get_elements_valuable(Elements)
# for debug
# self.debugDrawElementRect(Elements, isShowEach=True, isSaveEach=False, isDrawInSinglePic=True)
self.debugDrawElementRect(Elements, isShowEach=False, isSaveEach=False, isDrawInSinglePic=True)

Elements = [element for element in Elements if self.is_element_usable(element,FilterFirstPage)]
# for debug
# self.debugDrawElementRect(Elements, isShowEach=True, isSaveEach=False, isDrawInSinglePic=True)
self.debugDrawElementRect(Elements, isShowEach=False, isSaveEach=False, isDrawInSinglePic=True)
```

用于批量给多个元素加上框，输出到单个图片中 -> 方便调试时，知道哪些元素被当前一轮循环过滤掉了

## scaleToOrginSize 缩放图片到原始大小

背景：

iPhone的scale往往都是2或3等值

用iOS的截图都是大图，希望缩写到原图

```python
def scaleToOrginSize(self, screenshotImgPath, curScale):
    """resize to original screen size, according to session scale"""
    curScreenImg = Image.open(screenshotImgPath)
    originSize = curScreenImg.size # 750x1334
    newWidthInt = int(float(originSize[0]) / curScale)
    newHeightInt = int(float(originSize[1]) / curScale)
    scaledSize = (newWidthInt, newHeightInt) # 375x667
    scaledFile = screenshotImgPath
    CommonUtils.resizeImage(curScreenImg, newSize=scaledSize, outputImageFile=scaledFile)
    return scaledFile
```

调用举例：

```python
curScale = 3
if isResizeToOriginal:
    # and resize to original screen size
    savedImgFile = self.scaleToOrginSize(savedImgFile, curScale)
```

## saveiOSScreenshot 保存当前屏幕截图（图片文件）

```python
def saveiOSScreenshot(self, filePrefix="", imageFormat="jpg", saveFolder=""):
    """
        do screehsot for ios device and saved to jpg
            same screenshot image file size compare:
                png: 734KB
                jpg: 100KB
            so better to use jpg
    """
    savedScreenFile = None


    curDatetimeStr = CommonUtils.getCurDatetimeStr()
    # screenFilename = "%s_screen.%s" % (curDatetimeStr, imageFormat)
    screenFilename = "%s.%s" % (curDatetimeStr, imageFormat)
    if filePrefix:
        screenFilename = "%s_%s" % (filePrefix, screenFilename)
        # 'com.netease.cloudmusic_20200221_170305.jpg'
    screenFilename = os.path.join(saveFolder, screenFilename)


    if imageFormat == "png":
        curPillowObj = self.wdaClient.screenshot(png_filename=screenFilename)
        savedScreenFile = screenFilename
    elif (imageFormat == "jpg") or (imageFormat == "jpeg"):
        curPillowObj = self.wdaClient.screenshot()
        # logging.debug("curPillowObj=%s", curPillowObj)
        # curPillowObj=<PIL.PngImagePlugin.PngImageFile image mode=RGB size=750x1334 at 0x10F6CEE80>
        # convert to PIL.Image and then save as jpg
        curPillowObj.save(screenFilename)
        savedScreenFile = screenFilename
    else:
        logging.debug("Unsupported image format: %s", imageFormat)


    if savedScreenFile:
        logging.debug("saved screenshot: %s", savedScreenFile)
    return savedScreenFile
```

调用举例 + 相关函数：

```python
    def debugiOSSaveScreenshot(self, saveFolder, isResizeToOriginal=True, curScale=2.0):
        """for debug, save iOS screenshot"""
        # # for if enable debug screenshot image content too long
        # # so disable debug screenshot
        # needEnableLater = False
        # if wda.DEBUG:
        #     wda.DEBUG = False
        #     needEnableLater = True
        # savedImgFile = self.saveiOSScreenshot(filePrefix="", saveFolder=saveFolder)
        # if needEnableLater:
        #     wda.DEBUG = True
        def _saveScreenshot():
            return self.saveiOSScreenshot(filePrefix="", saveFolder=saveFolder)
        savedImgFile = self.runButNotOutputWdaDebug(_saveScreenshot)


        logging.debug("Saved iOS screenshot %s", savedImgFile)
        if isResizeToOriginal:
            # and resize to original screen size
            savedImgFile = self.scaleToOrginSize(savedImgFile, curScale)
        return savedImgFile
```

调用：

```python
        elif self.isiOS:
            fullImgFilePath = self.debugiOSSaveScreenshot(saveFolder=saveFolder, curScale=self.curSession.scale)
```

以及：

```python
    def runButNotOutputWdaDebug(self, funcToRun):
        # disable then re-enable debug for 
        # avoid debug print too many binary image data of current screenshot
        # for internal session scale will trigger do screenshot
        needEnableLater = False
        if wda.DEBUG:
            wda.DEBUG = False
            needEnableLater = True


        retValue = funcToRun()


        if needEnableLater:
            wda.DEBUG = True


        return retValue
```
