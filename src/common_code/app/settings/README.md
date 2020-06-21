# 设置

此处整理iOS的内置app，bundle id是`com.apple.Preferences`的`设置`相关的通用代码段。

## 启动内置app：`设置`

```python
def iOSLaunchSettings(self, isTerminateFirst=True, isDebugState=True):
    """iOS terminal and re-launch Settings=Preferences app

    Args:
        isTerminateFirst (bool): force terminal Settings before launch
        isDebugState (bool): enable debug for app state
    Returns:
        bool
    Raises:
    """

    iOS_AppId_Settings = "com.apple.Preferences"

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

    # settingsSession = self.wdaClient.session(iOS_AppId_Settings)
    # logging.debug("settingsSession=%s" % settingsSession)
    # launchResult = self.wdaClient.app_launch(iOS_AppId_Settings)
    # logging.debug("launchResult=%s", launchResult)
    isLaunchOk, respInfo = self.iOSLaunchApp(iOS_AppId_Settings)
    logging.info("isLaunchOk=%s, respInfo=%s", isLaunchOk, respInfo)
    # logging.info("launchResult: value=%s, status=%s, sessionId=%s", launchResult.value, launchResult.status, launchResult.sessionId)
    # launchResult: value=None, status=0, sessionId=79A39B72-F5F9-4A01-8E58-DD380452350A
    # logging.info("launchResult=%s", str(launchResult))
    # launchResult=GenericDict(value=None, sessionId='79A39B72-F5F9-4A01-8E58-DD380452350A', status=0)
    if isDebugState:
        isGetOk, curState = self.iOSGetAppState(iOS_AppId_Settings)
        logging.info("after launch: curState=%s", curState)
    return isLaunchOk, respInfo
```

调用：

```python
isLaunchOk, respInfo = self.iOSLaunchSettings()
```
