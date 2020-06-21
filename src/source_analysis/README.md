# 源码分析

下面给出`facebook-wda`的部分源码的内部实现逻辑分析：

## wda内部用到了Appium

文件：`refer/WebDriverAgent/WebDriverAgentLib/Commands/FBSessionCommands.m`

```cpp
+ (NSArray *)routes
{
  return
  @[
    [[FBRoute POST:@"/url"] respondWithTarget:self action:@selector(handleOpenURL:)],
    [[FBRoute POST:@"/session"].withoutSession respondWithTarget:self action:@selector(handleCreateSession:)],
    [[FBRoute POST:@"/wda/apps/launch"] respondWithTarget:self action:@selector(handleSessionAppLaunch:)],
    [[FBRoute POST:@"/wda/apps/activate"] respondWithTarget:self action:@selector(handleSessionAppActivate:)],
    [[FBRoute POST:@"/wda/apps/terminate"] respondWithTarget:self action:@selector(handleSessionAppTerminate:)],
    [[FBRoute POST:@"/wda/apps/state"] respondWithTarget:self action:@selector(handleSessionAppState:)],
    [[FBRoute GET:@"/wda/apps/list"] respondWithTarget:self action:@selector(handleGetActiveAppsList:)],
    [[FBRoute GET:@""] respondWithTarget:self action:@selector(handleGetActiveSession:)],
    [[FBRoute DELETE:@""] respondWithTarget:self action:@selector(handleDeleteSession:)],
    [[FBRoute GET:@"/status"].withoutSession respondWithTarget:self action:@selector(handleGetStatus:)],

    // Health check might modify simulator state so it should only be called in-between testing sessions
    [[FBRoute GET:@"/wda/healthcheck"].withoutSession respondWithTarget:self action:@selector(handleGetHealthCheck:)],

    // Settings endpoints
    [[FBRoute GET:@"/appium/settings"] respondWithTarget:self action:@selector(handleGetSettings:)],
    [[FBRoute POST:@"/appium/settings"] respondWithTarget:self action:@selector(handleSetSettings:)],
  ];
}
```

中的 `/appium/settings` 就是Appium的。

对应着支持外部的python的wda去调用：

文件：`/Users/limao/.pyenv/versions/3.8.0/Python.framework/Versions/3.8/lib/python3.8/site-packages/wda/__init__.py`

```python
def get_settings(self):
    resp = self.http.get("/appium/settings")
    respSettings = resp.value
    if DEBUG:
        # print("resp=%s" % resp) #  TypeError not all arguments converted during string formatting
        print("respSettings=%s" % respSettings)
    return respSettings


def set_settings(self, newSettings):
    postResp = self.http.post("/appium/settings", {
        "settings": newSettings,
    })
    respNewSettings = postResp.value
    if DEBUG:
        print("respNewSettings=%s" % respNewSettings)
    return respNewSettings
```

所以settings等参数，也要参考对应文档：

* GitHub中的
    * [appium/settings.md at master · appium/appium](https://github.com/appium/appium/blob/master/docs/en/advanced-concepts/settings.md)
* readthedocs中的
  * [Settings - appium](https://appium.readthedocs.io/en/stable/en/advanced-concepts/settings/)
  * [The Settings API - Appium](http://appium.io/docs/en/advanced-concepts/settings/)

其中的screenshotQuality的值，是来自苹果的XCTest中的定义：

* [XCTImageQuality - XCTest | Apple Developer Documentation](https://developer.apple.com/documentation/xctest/xctimagequality?language=objc)

以及另外相关的Capabilities

* [appium/caps.md at master · appium/appium](https://github.com/appium/appium/blob/master/docs/en/writing-running-appium/caps.md)
* [Update Settings - Appium](http://appium.io/docs/en/commands/session/settings/update-settings/)

而wda中其他还有地方也是用到Appium的，比如：

[Attribute - Appium](http://appium.io/docs/en/commands/element/attributes/attribute/)

```bash
HTTP API Specifications
Endpoint
GET /session/:session_id/elements/:element_id/attribute/:name
```

对应着能看到python的wda中就是：

文件：`/Users/limao/.pyenv/versions/3.8.0/Python.framework/Versions/3.8/lib/python3.8/site-packages/wda/__init__.py`

```python
def _wdasearch(self, using, value):
    """
    Returns:
        element_ids (list(string)): example ['id1', 'id2']
    
    HTTP example response:
    [
        {"ELEMENT": "E2FF5B2A-DBDF-4E67-9179-91609480D80A"},
        {"ELEMENT": "597B1A1E-70B9-4CBE-ACAD-40943B0A6034"}
    ]
    """
    element_ids = []
    for v in self.http.post('/elements', {
            'using': using,
            'value': value
    }).value:
        element_ids.append(v['ELEMENT'])
    return element_ids
```

和：

文件：`refer/WebDriverAgent/WebDriverAgentLib/Commands/FBFindElementCommands.m`

```cpp
+ (NSArray *)routes
{
  return
  @[
    [[FBRoute POST:@"/element"] respondWithTarget:self action:@selector(handleFindElement:)],
    [[FBRoute POST:@"/elements"] respondWithTarget:self action:@selector(handleFindElements:)],
    [[FBRoute POST:@"/element/:uuid/element"] respondWithTarget:self action:@selector(handleFindSubElement:)],
    [[FBRoute POST:@"/element/:uuid/elements"] respondWithTarget:self action:@selector(handleFindSubElements:)],
...
```

调试时开启debug后可以看到：

```bash
HAIN: **/XCUIElementTypeAny[`name == '通讯录'`]
Shell: curl -X POST -d '{"using": "class chain", "value": "**/XCUIElementTypeAny[`name == '\u901a\u8baf\u5f55'`]"}' 'http://192.168.31.43:8100/session/C69F63F6-80EA-47DD-BA04-0A912945CE39/elements'
Return (984ms): {
  "value" : [
    {
      "ELEMENT" : "15000000-0000-0000-0502-000000000000",
      "element-6066-11e4-a52e-4f735466cecf" : "15000000-0000-0000-0502-000000000000"
    }
  ],
  "sessionId" : "C69F63F6-80EA-47DD-BA04-0A912945CE39"
}
...
Shell: curl -X GET -d '' 'http://192.168.31.43:8100/session/C69F63F6-80EA-47DD-BA04-0A912945CE39/element/15000000-0000-0000-0502-000000000000/rect'
Return (688ms): {
  "value" : {
    "y" : 619,
    "x" : 96,
    "width" : 90,
    "height" : 48
  },
  "sessionId" : "C69F63F6-80EA-47DD-BA04-0A912945CE39"
}
```

去获取element的属性值的。
