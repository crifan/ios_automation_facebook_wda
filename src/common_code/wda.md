# wda

此处整理和`facebook-wda`本身相关的，常用的代码。

## init_device_driver_iOS wda基本的初始化

```python
def init_device_driver_iOS(self):
    # init facebook-wda
    # wda.HTTP_TIMEOUT = self.config["WdaHttpTimeout"]
    wda.HTTP_TIMEOUT = self.config["wda"]["http"]["timeout"]
    # wda.DEBUG = self.config["WdaDebug"]
    wda.DEBUG = self.config["wda"]["debug"]

    # self.wdaClient = wda.Client('http://localhost:8100')
    # wdaServerUrl = 'http://%s:%s' % (self.config["WdaServerHost"], self.config["WdaServerPort"])
    wdaServerUrl = 'http://%s:%s' % (self.config["wda"]["server"]["host"], self.config["wda"]["server"]["port"])
    logging.info("wdaServerUrl=%s", wdaServerUrl) # 'http://localhost:8100'
    self.wdaClient = wda.Client(wdaServerUrl)
    logging.info("self.wdaClient=%s", self.wdaClient)

    self.curSession = self.wdaClient

    # init settings
    # newSettings = {
    #     "snapshotTimeout": self.config["WdaSnapshotTimeout"],
    #     "screenshotQuality": self.config["WdaScreenQuality"],
    #     "mjpecfgScalingFactor": self.config["WdaScalingFactor"],
    #     "includeNonModalElements": self.config["WdaIncludeNonModalElements"],
    #     "shouldUseCompactResponses": self.config["WdaShouldUseCompactResponses"],
    #     "elementResponseAttributes": self.config["WdaElementResponseAttributes"],
    # }
    newSettings = {
        "snapshotTimeout": self.config["wda"]["settings"]["SnapshotTimeout"],
        "screenshotQuality": self.config["wda"]["settings"]["ScreenQuality"],
        "mjpecfgScalingFactor": self.config["wda"]["settings"]["ScalingFactor"],
        "includeNonModalElements": self.config["wda"]["settings"]["IncludeNonModalElements"],
        "shouldUseCompactResponses": self.config["wda"]["settings"]["ShouldUseCompactResponses"],
        "elementResponseAttributes": self.config["wda"]["settings"]["ElementResponseAttributes"],
    }
    respNewSettings = self.curSession.set_settings(newSettings)
    logging.debug("respNewSettings=%s", respNewSettings)
```

## processWdaResponse 处理(post等返回的response）返回数据

```python
def processWdaResponse(self, wdaResponse):
    """Process common wda (http post) response

    Args:
    Returns:
        bool, ?/dict:
            true, response value
            false, error info dict
    Raises:
    """
    isRespOk = False
    respInfo = None
    logging.debug("wdaResponse=%s", wdaResponse)
    respStatus = wdaResponse.status
    respValue = wdaResponse.value
    respSessionId = wdaResponse.sessionId
    logging.debug("respStatus=%s, respValue=%s, respSessionId", respStatus, respValue, respSessionId)
    if respStatus == 0:
        isRespOk = True
        respInfo = respValue
    else:
        isRespOk = False
        errInfo = {
            "status": respStatus,
            "value": respValue,
            "sessionId": respSessionId,
        }
        respInfo = errInfo
    return isRespOk, respInfo
```

调用举例：

（1）

```python
curAppState = self.wdaClient.app_state(appBundleId)
isGetOk, respInfo = self.processWdaResponse(curAppState)
```

（2）

```python
terminateResp = self.wdaClient.app_terminate(appBundleId)
logging.debug("terminateResp=%s", terminateResp)
# respStatus = resp.status
# respValue = resp.value
# respSessionId = resp.sessionId
# logging.info("respStatus=%s, respValue=%s, respSessionId", respStatus, respValue, respSessionId)
# if respStatus == 0:
#     isTerminalOk = True
#     respInfo = None
# else:
#     errInfo = {
#         "status": respStatus,
#         "value": respValue,
#     }
#     respInfo = errInfo
isTerminalOk, respInfo = self.processWdaResponse(terminateResp)
```

（3）

```python
launchResp = self.wdaClient.app_launch(appBundleId)
isLaunchOk, respInfo = self.processWdaResponse(launchResp)
```
