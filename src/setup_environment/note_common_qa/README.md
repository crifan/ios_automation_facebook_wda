# 搭建环境期间常见问题和心得

下面整理一些搭建环境期间的常见问题和心得总结：


## xcodebuild报错：xcode-select error tool xcodebuild requires Xcode

如果运行xcodebuld报错：

```bash
xcode-select: error: tool 'xcodebuild' requires Xcode, but active developer directory '/Library/Developer/CommandLineTools' is a command line tools instance
```

* **原因**：没有安装XCode 或 虽然已安装XCode，但是没启用XCode的命令行
* **解决办法**：去安装并开启XCode的命令行
* **步骤**：
  * 文字
    * `Xcode`->`设置`->`Locations`->`Command Line Tools`，默认是**空**，下拉选择`Xcode 11.3.1(11C504)`
  * 截图
    * ![xcode_locations_command_line_tools](../../assets/img/xcode_locations_command_line_tools.png)

安装后，即可查看版本信息：

```bash
~  xcodebuild -version
Xcode 11.3.1
Build version 11C504
```

## xcodebuild报错：xcodebuild error missing value for key

如果没有iOS设备（如iPhone）插入到Mac中，则运行：

```bash
xcodebuild -project WebDriverAgent.xcodeproj -scheme WebDriverAgentRunner -destination "id=`idevice_id -l | head -n1`" test
```

会报错：

```bash
 ~/dev/xxx/crawler/appAutoCrawler/AppCrawler/iOSAutomation/refer/WebDriverAgent   master ●  xcodebuild -project WebDriverAgent.xcodeproj -scheme WebDriverAgentRunner -destination "id=`idevice_id -l | head -n1`" test
xcodebuild: error: missing value for key 'id' of option 'Destination'
```

## 当前被测iOS设备详情

在启动`test manager`期间会输出当前被测设备的详细信息

举例：

(1) `iOS 12.4.5`的`iPhone6`

```bash
2020-05-07 09:20:31.198 xcodebuild[2440:2434041] [MT] IDETestOperationsObserverDebug: (B7957682-E70F-46C7-86C2-53AEE7C8993D) Beginning test session WebDriverAgentRunner-B7957682-E70F-46C7-86C2-53AEE7C8993D at 2020-05-07 09:20:31.194 with Xcode 11C504 on target 📱<DVTiOSDevice (0x7f8456759e30), Crifan iPhone6, iPhone, 12.4.5 (16G161), ed94089f3e34d5538065a695bfdf03dfbb3c5579> {
        deviceSerialNumber:         DNPND9S1G5MR
        identifier:                 ed94089f3e34d5538065a695bfdf03dfbb3c5579
        deviceClass:                iPhone
        deviceName:                 Crifan iPhone6
        deviceIdentifier:           ed94089f3e34d5538065a695bfdf03dfbb3c5579
        productVersion:             12.4.5
        buildVersion:               16G161
        deviceSoftwareVersion:      12.4.5 (16G161)
        deviceArchitecture:         arm64
        deviceTotalCapacity:        60058931200
        deviceAvailableCapacity:    38391648256
        deviceIsTransient:          NO
        ignored:                    NO
        deviceIsBusy:               NO
        deviceIsPaired:             YES
        deviceIsActivated:          YES
        deviceActivationState:      Activated
        isPasscodeLocked:           NO
        deviceType:                 <DVTDeviceType:0x7f845621d390 Xcode.DeviceType.iPhone>
        supportedDeviceFamilies:    (
    1
)
        applications:              (null)
        provisioningProfiles:      (null)
        hasInternalSupport:        NO
        hasWritableSystem:         NO
        isSupportedOS:             YES
        bootArgs:                  (null)
        nextBootArgs:              (null)
        connected:                 YES
        isWirelessEnabled:         NO
        connectionType:            direct
        hostname:                  (null)
        bonjourServiceName:        d4:f4:6f:0a:30:80@fe80::d6f4:6fff:fe0a:3080._apple-mobdev2._tcp.local.
        activeProxiedDevice:       (null)
        } (12.4.5 (16G161))
```
