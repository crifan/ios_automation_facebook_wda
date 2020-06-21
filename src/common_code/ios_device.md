# iOS设备

此处整理和iOS设备，比如iPhone相关的常用代码段。

## 设备是否解锁

```python
def is_device_unlock_iOS(self, device):
    # Note: before later real init dirver, init here for here can use wdaClient
    self.init_device_driver_iOS()
    isLocked = self.wdaClient.locked()
    isUnlocked = not isLocked
    return isUnlocked
```

注：其中`init_device_driver_iOS`定义详见：[wda基本的初始化](https://book.crifan.com/books/ios_automation_facebook_wda/website/common_code/)

## 获取电池状态

```python
def is_device_charged_iOS(self):
    batteryInfo = self.wdaClient.battery_info()
    logging.debug("batteryInfo=%s", batteryInfo)
    # batteryInfo={'level': 1, 'state': 2}
    # batteryInfo={'level': 0.49000000953674316, 'state': 2}
    batteryLevelFloat = batteryInfo["level"]
    batteryLevelPercentInt = int(batteryLevelFloat * 100) # 100, 49
    batteryStateInt = batteryInfo["state"]
    logging.debug("batteryStateInt=%s", batteryStateInt)

    curBattryState = BatteryState(batteryStateInt)
    logging.debug("curBattryState=%s", curBattryState) # curBattryState=BatteryState.Charging
    curBattryStateName = curBattryState.name
    logging.debug("curBattryStateName=%s", curBattryStateName) # curBattryStateName=Charging
    logging.info("Battery state: %s", curBattryStateName) # Battery state: Charging
    return batteryLevelPercentInt
```

相关定义：

```python
from wda import ScreenshotQuality, BatteryState, ApplicationState
```

其中是我自己定义的：

```python
################################################################################
# Definition
################################################################################

# https://developer.apple.com/documentation/uikit/uidevice/1620042-batterylevel?language=objc
class BatteryState(Enum):
    Unknown = 0
    Unplugged = 1
    Charging = 2
    Full = 3

# https://developer.apple.com/documentation/xctest/xctimagequality?language=objc
class ScreenshotQuality(Enum):
    Original = 0 # lossless PNG image
    Medium = 1 # high quality lossy JPEG image
    Low = 2 # highly compressed lossy JPEG image

# https://developer.apple.com/documentation/xctest/xcuiapplicationstate?language=objc
class ApplicationState(Enum):
    Unknown = 0
    NotRunning = 1
    RunningBackgroundSuspended = 2
    RunningBackground = 3
    RunningForeground = 4
```

## get_devices_iOS 当前连接（到Mac）的iOS设备

```python
def get_devices_iOS(self):
    deviceStrList = self.get_iOS_deviceStrList()
    iOSDevIdList = []
    for eachDevStr in deviceStrList:
        # Mo iPhone (11.4.1) [fc9e1f9f2e1339328b9580f826a30fded3bc69ff]
        # iPhone 11 Pro Max (13.3) + Apple Watch Series 5 - 44mm (6.1.1) [D86F0BD5-4D38-4537-9C8C-2F5C74E404CA] (Simulator)
        # iPhone 11 (13.3) [509BC7C7-9C0E-42FA-8AB2-F5220EBAA13B] (Simulator)
        foundDevId = re.search("\[(?P<iOSdevId>[\w\-]+)\](\s+\(Simulator\))?$", eachDevStr, re.I)
        if foundDevId:
            iOSdevId = foundDevId.group("iOSdevId")
            iOSDevIdList.append(iOSdevId)
    # ['fc9e1f9f2e1339328b9580f826a30fded3bc69ff', 'D86F0BD5-4D38-4537-9C8C-2F5C74E404CA', ...]
    return iOSDevIdList
```

相关函数：

```python
def get_iOS_deviceStrList(self):
    deviceStrList = []
    deviceListCmd = 'instruments -s devices'
    deviceListStr = CommonUtils.get_cmd_lines(deviceListCmd, text=True)
    if deviceListStr:
        deviceRawList = deviceListStr.split("\n")
        """
            Known Devices:
            limao的MacBook Pro [F9089371-1060-5CE3-99BB-81741693BE80]
            Mo iPhone (11.4.1) [fc9e1f9f2e1339328b9580f826a30fded3bc69ff]
            Apple TV (13.3) [6680F059-4DE1-430C-B696-228AC27CAA88] (Simulator)
            Apple TV 4K (13.3) [048E58E8-6A27-4D81-BDEB-8812C610B756] (Simulator)
            Apple TV 4K (at 1080p) (13.3) [384D5E60-B6B1-481E-BDC3-B7FF8F773412] (Simulator)
            Apple Watch Series 4 - 40mm (6.1.1) [1B98415B-3FDE-401B-A80C-A3551DB207D7] (Simulator)
            Apple Watch Series 4 - 44mm (6.1.1) [661838E9-B0BE-42B4-B55E-9A34263B1AEA] (Simulator)
            iPad (7th generation) (13.3) [7F8EDE89-74E0-4BAB-B3CA-09E2DAE1F095] (Simulator)
            iPad Air (3rd generation) (13.3) [BBC48526-3922-4C97-BA14-B1888385243A] (Simulator)
            iPad Pro (11-inch) (13.3) [04DD3B8A-5B78-48E8-8B22-56796A9CFB73] (Simulator)
            iPad Pro (12.9-inch) (3rd generation) (13.3) [D811684E-2F3E-4FC6-92EA-39301451F7E5] (Simulator)
            iPad Pro (9.7-inch) (13.3) [B11D5D40-FEA2-4114-B053-E4CFD29D127C] (Simulator)
            iPhone 11 (13.3) [509BC7C7-9C0E-42FA-8AB2-F5220EBAA13B] (Simulator)
            iPhone 11 Pro (13.3) [3E8E7E92-66F2-4AF3-A405-23B5FB231DE7] (Simulator)
            iPhone 11 Pro (13.3) + Apple Watch Series 5 - 40mm (6.1.1) [F76D77EF-0932-4164-94BB-9FC757420911] (Simulator)
            iPhone 11 Pro Max (13.3) [50C15135-1532-44C5-B82C-B327F88F2712] (Simulator)
            iPhone 11 Pro Max (13.3) + Apple Watch Series 5 - 44mm (6.1.1) [D86F0BD5-4D38-4537-9C8C-2F5C74E404CA] (Simulator)
            iPhone 8 (13.3) [54589698-0C9F-407D-B21A-83432CABB681] (Simulator)
            iPhone 8 Plus (13.3) [509B7103-97DB-4AB9-B829-001190ED4B7E] (Simulator)
        """
        # CoreData: annotation:  Failed to load optimized model at path '/Applications/Xcode.app/Contents/Applications/Instruments.app/Contents/Frameworks/InstrumentsPackaging.framework/Versions/A/Resources/XRPackageModel.momd/XRPackageModel 9.0.omo'
        # InvalidCoreData = "CoreData"
        # Known Devices:
        # InvalidKnownDevices = "Known Devices"
        for eachDeviceStr in deviceRawList:
            eachDeviceStr = eachDeviceStr.strip()
            # isNotCoreData = InvalidCoreData not in eachDeviceStr
            # isNotKnownDevices = InvalidKnownDevices not in eachDeviceStr
            # if isNotCoreData and isNotKnownDevices:
            # if isNotKnownDevices:
            # Note: current only support iPhone devices, both real and simulator
            #     with device name contain 'iphone'
            foundiPhone = re.search("iPhone", eachDeviceStr, re.I)
            if foundiPhone:
                deviceStrList.append(eachDeviceStr)
                """
                    Mo iPhone (11.4.1) [fc9e1f9f2e1339328b9580f826a30fded3bc69ff]
                    iPhone 11 (13.3) [509BC7C7-9C0E-42FA-8AB2-F5220EBAA13B] (Simulator)
                    iPhone 11 Pro (13.3) [3E8E7E92-66F2-4AF3-A405-23B5FB231DE7] (Simulator)
                    iPhone 11 Pro (13.3) + Apple Watch Series 5 - 40mm (6.1.1) [F76D77EF-0932-4164-94BB-9FC757420911] (Simulator)
                    iPhone 11 Pro Max (13.3) [50C15135-1532-44C5-B82C-B327F88F2712] (Simulator)
                    iPhone 11 Pro Max (13.3) + Apple Watch Series 5 - 44mm (6.1.1) [D86F0BD5-4D38-4537-9C8C-2F5C74E404CA] (Simulator)
                    iPhone 8 (13.3) [54589698-0C9F-407D-B21A-83432CABB681] (Simulator)
                    iPhone 8 Plus (13.3) [509B7103-97DB-4AB9-B829-001190ED4B7E] (Simulator)
                """
    return deviceStrList
```

调用举例：

```python
def get_phone_name_iOS(self):
    deviceName = ""
    deviceStrList = self.get_iOS_deviceStrList()
    # Mo iPhone (11.4.1) [fc9e1f9f2e1339328b9580f826a30fded3bc69ff]
    curDevP = "(?P<deviceName>[\w ]+)\s+\((?P<iOSVersion>[\d\.]+)\)\s+\[%s\]" % self.device
    for eachDeviceStr in deviceStrList:
        foundCurDev = re.search(curDevP, eachDeviceStr)
        if foundCurDev:
            deviceName = foundCurDev.group("deviceName")
            iOSVersion = foundCurDev.group("iOSVersion")
            break

    return deviceName
```

