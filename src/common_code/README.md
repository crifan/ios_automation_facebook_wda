# 常用代码段

在折腾`facebook-wda`期间，把各种常用的功能封装成了函数，现整理如下供参考：

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

注：最新版本代码以及具体解释，详见：

[通用逻辑 · Python常用代码段](https://book.crifan.com/books/python_common_code_snippet/website/common_code/common_logic.html)

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
