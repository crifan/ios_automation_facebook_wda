# app管理

说明：下面app管理期间用到的iOS的内置的app，其bundle id可参考：[iOS内置app的bundle id](https://book.crifan.com/books/ios_automation_facebook_wda/website/appendix/ios_app_bundle_id.html)

## get_PackageActivity_iOS 当前正在运行的app的信息

```python
def get_PackageActivity_iOS(self):
    """
        {
            "running" : true,
            "state" : 4,
            "generation" : 0,
            "processArguments" : {
                "env" : {},
                "args" : []
            },
            "title" : "",
            "bundleId" : "com.tencent.xin",
            "label" : "微信",
            "path" : "",
            "name" : "",
            "pid" : 1357
        }
    """
    curAppInfo = self.wdaClient.app_current()
    logging.debug("curAppInfo=%s", curAppInfo)
    return curAppInfo
```

调用：

```python
# 获取获取当前活跃app及activity方法
curAppInfo = self.get_PackageActivity_iOS()
curAppStateInt = curAppInfo["state"]
curStateEnum = ApplicationState(curAppStateInt)
logging.debug("curStateEnum=%s", curStateEnum)
bundleId = curAppInfo["bundleId"]
logging.debug("bundleId=%s", bundleId)
curAppName = curAppInfo["label"]
logging.debug("curAppName=%s", curAppName)

package = bundleId
```

## 获取app状态

```python
def iOSGetAppState(self, appBundleId):
    """get iOS app state

    Args:
        appBundleId (str): iOS app bundle id
    Returns:
        bool, enum/dict:
            true, app status enum
            false, error info dict
    Raises:
    """
    curAppState = self.wdaClient.app_state(appBundleId)
    logging.debug("curAppState=%s", curAppState)
    """
    {
        "value" : 4,
        "sessionId" : "5BBD460B-F420-461D-A5E3-244A74CDF5CE"
    }
    """
    # # <GenericDict, len() = 3>
    # # curAppStateValue = curAppState[0]
    # # curAppStatus = curAppState.status
    # # curAppSessionId = curAppState.sessionId
    # # logging.debug("curAppStatus=%s, curAppSessionId=%s", curAppStatus, curAppSessionId)
    # curAppStateValue = curAppState.value
    # logging.debug("curAppStateValue=%s", curAppStateValue)
    # curStateEnum = ApplicationState(curAppStateValue)
    # logging.debug("curStateEnum=%s", curStateEnum)
    # return curStateEnum
    isGetOk, respInfo = self.processWdaResponse(curAppState)
    if isGetOk:
        respValue = respInfo
        curStateEnum = ApplicationState(respValue)
        logging.debug("curStateEnum=%s", curStateEnum)
        respInfo = curStateEnum
    return isGetOk, respInfo
```

## 启动app

```python
def iOSLaunchApp(self, appBundleId):
    """Launch iOS app

    Args:
        appBundleId (str): iOS app bundle id
    Returns:
        bool, None/str:
            true, None
            false, str: error message
    Raises:
    """
    launchResp = self.wdaClient.app_launch(appBundleId)
    logging.debug("launchResp=%s", launchResp)
    isLaunchOk, respInfo = self.processWdaResponse(launchResp)
    return isLaunchOk, respInfo
```

## 停止app

```python
def iOSTerminateApp(self, appBundleId):
    """Terminate iOS app

    Args:
        appBundleId (str): iOS app bundle id
    Returns:
        bool, bool/str:
            true, bool
                True: terminal Ok
                False: terminal fail
                    eg: current not running 设置, if terminal, return False
            false, str: error message
    Raises:
    """
    # isTerminalOk = False
    # respInfo = None
    # self.wdaClient.session().app_terminate(appBundleId)
    # self.wdaClient().app_terminate(appBundleId)
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
    return isTerminalOk, respInfo
```

调用：

```python
if isTerminateFirst:
    if isDebugState:
        isGetOk, curState = self.iOSGetAppState(iOS_AppId_Settings)
        logging.info("before terminal: curState=%s", curState)
    # stop before start to avoid current page is not homepage of 设置
    isTerminalOk, respInfo = self.iOSTerminateApp(iOS_AppId_Settings)
    logging.info("%s: isTerminalOk=%s, respInfo=%s", iOS_AppId_Settings, isTerminalOk, respInfo)
    if isDebugState:
        isGetOk, curState = self.iOSGetAppState(iOS_AppId_Settings)
        logging.info("after terminal: curState=%s", curState)
```
