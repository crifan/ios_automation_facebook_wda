# iOS弹框处理

背景：iOS的各种app，有各种类型的弹框，目前主要是通过 wda的query 和 获取源码后bs的find找到过滤，再去点击让弹框消失
其中此处分成了2部分：
* 首次启动后，可能会遇到的弹框：`process_popup_iOS_FirstLaunchBeforeMain`
* 其他正常运行期间，可能会遇到的弹框：`process_popup_iOS`

## process_popup_iOS_FirstLaunchBeforeMain

```python
def process_popup_iOS_FirstLaunchBeforeMain(self, soup=None):
    """
    处理iOS中app，在首次登录，进入主页之前，的弹框
    """
    foundAndProcessedPopup = False
    if not soup:
        curPageXml = self.get_page_source()
        soup = CommonUtils.xmlToSoup(curPageXml)

    # if not foundAndProcessedPopup:
    #     foundAndProcessedPopup = self.iOSProcessPopupUpperRightClose(soup)
    # if not foundAndProcessedPopup:
    #     foundAndProcessedPopup = self.iOSProcessPopupUpperRightAlertClose(soup)
    # Note: put common upper right close, for some app, eg 必要, before show main page, exist these popup
    if not foundAndProcessedPopup:
        # foundAndProcessedPopup = self.iOSProcessPopupUpperRightCommonClose(soup)
        foundAndProcessedPopup = self.iOSProcessPopupCommonNameContainClose(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessPopupPossibleCloseButton(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessPopupUnderCloseButton(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessPopupIKnow(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessCommonUserAgreementAgree(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessUserPrivacyProtocol(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessAlertUseWirelessData(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessCommonAlertAllow(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessCommonAlertOk(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessRightUpperJumpOver(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessPopupCommonSave(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessPopupCommonConfirmButton(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessPopupImmediatelyExperience(soup)

    return foundAndProcessedPopup
```

## process_popup_iOS

```python
def process_popup_iOS(self, curPageXml):
    foundAndProcessedPopup = False

    soup = CommonUtils.xmlToSoup(curPageXml)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.process_popup_iOS_FirstLaunchBeforeMain(soup)

    if self.isSpecialiOSApp_kags:
        if not foundAndProcessedPopup:
            foundAndProcessedPopup = self.iOSProcessKagsPopupStillNotLogin(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessPopupPhoneCall(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessSettingsAllowUseLocation(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessPopupCertificateError(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessPopupNetworkNotStableRetry(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessPopupNetworkNotStableRefresh(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessPopupSystemAbnormalRetryRefresh(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessPopupServerBusyLaterRetry(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessPopupWeixinLogin(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessPopupJumpThirdApp(soup)

    if not foundAndProcessedPopup:
        # foundAndProcessedPopup = self.iOSProcessPopupGetInfoFail(soup)
        foundAndProcessedPopup = self.iOSProcessPopupDoSomeFail(soup)

    if not foundAndProcessedPopup:
        # foundAndProcessedPopup = self.iOSProcessPopupMiniprogamAuthority(soup)
        foundAndProcessedPopup = self.iOSProcessPopupMiniprogamGetYour(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessPopupWeixinAuthorizeLocation(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessPopupMiniprogamWarning(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessPopupMiniprogamDisclaimer(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessWillOpenNonOfficialPage(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessAlertNoteConfrim(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessAlertCancel(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessAlertCommonGiveup(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessPopupPhotoCamera(soup)

    # 注：此逻辑经常误判，没有弹框误以为有弹框，所以放弃此逻辑
    # if not foundAndProcessedPopup:
    #     foundAndProcessedPopup = self.iOSProcessCommonPopupWindowUpperRightClose(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessPopupSovereignHasRead(soup)

    if not foundAndProcessedPopup:
        foundAndProcessedPopup = self.iOSProcessPopupCommonWindowCancel(soup)

    return foundAndProcessedPopup
```

具体实现：

## iOSProcessPopupImmediatelyExperience

```python
def iOSProcessPopupImmediatelyExperience(self, soup):
    """
        弹框 other->other-button name=立即体验
    """

    """
        米家 弹框 立即体验：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                        <XCUIElementTypeImage type="XCUIElementTypeImage" name="sort_guide_icon" enabled="true" visible="false" x="54" y="115" width="306" height="310"/>
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="设备排序全新升级" name="设备排序全新升级" label="设备排序全新升级" enabled="true" visible="true" x="111" y="426" width="192" height="34"/>
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="1.新增快速置顶，排序操作更方便 2.顶部排序固定，不会被随意打乱" name="1.新增快速置顶，排序操作更方便 2.顶部排序固定，不会被随意打乱" label="1.新增快速置顶，排序操作更方便 2.顶部排序固定，不会被随意打乱" enabled="true" visible="true" x="95" y="468" width="224" height="42"/>
                        <XCUIElementTypeButton type="XCUIElementTypeButton" name="立即体验" label="立即体验" enabled="true" visible="true" x="24" y="657" width="366" height="43"/>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>
    """
    immediatelyExperienceChainList = [
        {
            "tag": "XCUIElementTypeOther",
            "attrs": self.FullScreenAttrDict
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": self.FullScreenAttrDict
        },
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"enabled":"true", "visible":"true", "name":"立即体验"}
        },
    ]
    foundAndProcessedPopup = self.findAndClickCenterPosition(immediatelyExperienceChainList, soup)
    return foundAndProcessedPopup
```

## iOSProcessPopupCommonConfirmButton

```python
def iOSProcessPopupCommonConfirmButton(self, soup):
    """
        other下面other下的button name="确定"
    """
    foundAndProcessedPopup = False
    """
        米家 弹框 米家隐私政策 确定：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="41" y="243" width="332" height="250">
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="米家隐私政策" name="米家隐私政策" label="米家隐私政策" enabled="true" visible="true" x="49" y="263" width="316" height="16"/>
                    <XCUIElementTypeTextView type="XCUIElementTypeTextView" value="我们的隐私政策已于2018年4月28日更新，将会于2018年5月25日生效。我们对隐私政策进行了详细修订，从该日期开始，这一隐私政策能够提供有关我们如何管理你在使用所有米家产品和服务时透露的个人信息的隐私详情，特定的米家产品或服务提供独立的隐私政策除外。&#10;请花一些时间熟悉我们的隐私权惯例，如果你有任何问题，请告诉我们。&#10;&#10; 米家隐私政策 " enabled="true" visible="true" x="49" y="283" width="316" height="145"/>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="41" y="442" width="332" height="1"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="取消" label="取消" enabled="true" visible="true" x="41" y="443" width="166" height="50"/>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="206" y="443" width="2" height="50"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="确定" label="确定" enabled="true" visible="true" x="207" y="443" width="166" height="50"/>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>
    """
    confirmChainList = [
        {
            "tag": "XCUIElementTypeOther",
            "attrs": self.FullScreenAttrDict
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true"}
        },
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"enabled":"true", "visible":"true", "name":"确定"}
        },
    ]
    confirmSoup = CommonUtils.bsChainFind(soup, confirmChainList)
    if confirmSoup:
        self.clickElementCenterPosition(confirmSoup)
        foundAndProcessedPopup = True

    return foundAndProcessedPopup
```

## iOSProcessPopupCommonSave

```python
def iOSProcessPopupCommonSave(self, soup):
    """
        通用的弹框 other下的other下的button name=保存
    """
    foundAndProcessedPopup = False
    """
        米家 弹框 地区选择 保存：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">

                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="64" width="414" height="88">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="10" y="64" width="394" height="36">
                                <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="10" y="64" width="394" height="36"/>
                                <XCUIElementTypeSearchField type="XCUIElementTypeSearchField" name="搜索" label="搜索" enabled="true" visible="true" x="18" y="64" width="378" height="36"/>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="110" width="414" height="10"/>
                            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="选择你的使用国家或地区" name="选择你的使用国家或地区" label="选择你的使用国家或地区" enabled="true" visible="true" x="22" y="120" width="368" height="32"/>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="22" y="151" width="392" height="1"/>
                        </XCUIElementTypeOther>
                        <XCUIElementTypeTable type="XCUIElementTypeTable" enabled="true" visible="true" x="0" y="152" width="414" height="450">

                        </XCUIElementTypeTable>
                        <XCUIElementTypeButton type="XCUIElementTypeButton" value="1" name="checkbox unchecked" label="checkbox unchecked" enabled="true" visible="true" x="91" y="626" width="20" height="20"/>
                        <XCUIElementTypeButton type="XCUIElementTypeButton" name="我已阅读 用户协议和隐私政策" label="我已阅读 用户协议和隐私政策" enabled="true" visible="true" x="119" y="622" width="176" height="28"/>
                        <XCUIElementTypeButton type="XCUIElementTypeButton" name="保存" label="保存" enabled="true" visible="true" x="24" y="672" width="366" height="42"/>
                    </XCUIElementTypeOther>
    """
    commonSaveChainList = [
        {
            "tag": "XCUIElementTypeOther",
            "attrs": self.FullScreenAttrDict
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": self.FullScreenAttrDict
        },
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"enabled":"true", "visible":"true", "name":"保存"}
        },
    ]
    commonSaveSoup = CommonUtils.bsChainFind(soup, commonSaveChainList)
    if commonSaveSoup:
        self.clickElementCenterPosition(commonSaveSoup)
        foundAndProcessedPopup = True
    return foundAndProcessedPopup
```

## iOSProcessPopupCommonWindowCancel

```python
def iOSProcessPopupCommonWindowCancel(self, soup):
    """
        通用的弹框 window下的other下的button name=取消
    """
    foundAndProcessedPopup = False
    """
        斑马AI课 弹框 该音频为专属音频 取消：
            <XCUIElementTypeWindow type="XCUIElementTypeWindow" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="0" y="0" width="414" height="736"/>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="0" y="20" width="414" height="716"/>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="30" y="178" width="354" height="380">
                        <XCUIElementTypeImage type="XCUIElementTypeImage" name="ZBEFMAlertImage" enabled="true" visible="false" x="93" y="198" width="228" height="184"/>
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="该音频为专属音频，仅对英语S2系统课用户开放" name="该音频为专属音频，仅对英语S2系统课用户开放" label="该音频为专属音频，仅对英语S2系统课用户开放" enabled="true" visible="true" x="57" y="395" width="300" height="43"/>
                        <XCUIElementTypeButton type="XCUIElementTypeButton" name="查看课程详情" label="查看课程详情" enabled="true" visible="true" x="79" y="454" width="256" height="44"/>
                        <XCUIElementTypeButton type="XCUIElementTypeButton" name="取消" label="取消" enabled="true" visible="true" x="191" y="510" width="32" height="30"/>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeWindow>
    """
    commonCancelChainList = [
        {
            "tag": "XCUIElementTypeWindow",
            "attrs": self.FullScreenAttrDict
        },
        {
            "tag": "XCUIElementTypeOther",
            # "attrs": self.FullScreenAttrDict
            "attrs": {"enabled":"true", "visible":"true"}
        },
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"enabled":"true", "visible":"true", "name":"取消"}
        },
    ]
    commonCancelSoup = CommonUtils.bsChainFind(soup, commonCancelChainList)
    if commonCancelSoup:
        self.clickElementCenterPosition(commonCancelSoup)
        foundAndProcessedPopup = True
    return foundAndProcessedPopup
```

## iOSProcessPopupSovereignHasRead

```python
def iOSProcessPopupSovereignHasRead(self, soup):
    """
        必要 弹框 朕已阅
    """
    foundAndProcessedPopup = False
    """
        必要 弹框 朕已阅：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="true" x="44" y="156" width="326" height="424">
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="抢福利活动说明" name="抢福利活动说明" label="抢福利活动说明" enabled="true" visible="true" x="149" y="165" width="117" height="20"/>
                    <XCUIElementTypeTextView type="XCUIElementTypeTextView" value="抢福利活动全新上线啦！！！&#10;当您获得特权金、参团卡、全民拼卡、返现卡、金币等资产后，请您尽快使用，很可能会被好友抢走哦！&#10;本期重磅推出&lt;保护罩&gt;道具卡！您的资产可受到保护不被抢走,快去使用金币在每日签到页兑换吧！！&#10;新玩法需要用户更新APP，如果您更新APP后，仍然无法参与活动，有可能APP正在发版中，请您等待。" enabled="true" visible="true" x="66" y="204" width="282" height="292"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="朕已阅" label="朕已阅" enabled="true" visible="true" x="82" y="513" width="250" height="50"/>
                </XCUIElementTypeImage>
            </XCUIElementTypeOther>
    """
    hasReadChainList = [
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true", "x":"0", "y":"0", "width":"%s" % self.X, "height":"%s" % self.totalY}
        },
        {
            "tag": "XCUIElementTypeImage",
            "attrs": {"enabled":"true", "visible":"true"}
        },
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"enabled":"true", "visible":"true", "name": "朕已阅"}
        },
    ]
    hasReadCloseSoup = CommonUtils.bsChainFind(soup, hasReadChainList)
    if hasReadCloseSoup:
        self.clickElementCenterPosition(hasReadCloseSoup)
        foundAndProcessedPopup = True
    return foundAndProcessedPopup
```

## iOSProcessAlertCommonGiveup

```python
def iOSProcessAlertCommonGiveup(self, soup):
    """
        通用逻辑：对于alert弹框， 找里面的 放弃 button，并点击
    """
    foundAndProcessedPopup = False
    """
        京东金融 弹框 是否放弃注册？
            <XCUIElementTypeAlert type="XCUIElementTypeAlert" name="是否放弃注册？" label="是否放弃注册？" enabled="true" visible="true" x="72" y="316" width="270" height="105">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="316" width="270" height="105">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="316" width="270" height="105">
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="316" width="270" height="105">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="316" width="270" height="105"/>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="316" width="270" height="105">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="316" width="270" height="105"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="316" width="270" height="105"/>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="316" width="270" height="105">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="316" width="270" height="60">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="316" width="270" height="60">
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="是否放弃注册？" name="是否放弃注册？" label="是否放弃注册？" enabled="true" visible="true" x="88" y="335" width="238" height="21"/>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="376" width="270" height="1">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="376" width="270" height="1"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="376" width="270" height="1"/>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="376" width="270" height="45">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="376" width="270" height="45">
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="376" width="270" height="45">
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="376" width="135" height="45">
                                            <XCUIElementTypeButton type="XCUIElementTypeButton" name="放弃" label="放弃" enabled="true" visible="true" x="72" y="376" width="135" height="45"/>
                                        </XCUIElementTypeOther>
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="207" y="376" width="1" height="45">
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="207" y="376" width="1" height="45"/>
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="207" y="376" width="1" height="45"/>
                                        </XCUIElementTypeOther>
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="207" y="376" width="135" height="45">
                                            <XCUIElementTypeButton type="XCUIElementTypeButton" name="继续注册" label="继续注册" enabled="true" visible="true" x="207" y="376" width="135" height="45"/>
                                        </XCUIElementTypeOther>
                                    </XCUIElementTypeOther>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeAlert>
    """
    giveupChainList = [
        {
            "tag": "XCUIElementTypeAlert",
            "attrs": {"enabled":"true", "visible":"true"}
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true"}
        },
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"enabled":"true", "visible":"true", "name":"放弃"}
        },
    ]
    alertGiveupSoup = CommonUtils.bsChainFind(soup, giveupChainList)
    if alertGiveupSoup:
        self.clickElementCenterPosition(alertGiveupSoup)
        foundAndProcessedPopup = True
    return foundAndProcessedPopup
```

## iOSProcessPopupServerBusyLaterRetry

```python
def iOSProcessPopupServerBusyLaterRetry(self, soup):
    foundAndProcessedPopup = False
    """
        京东金融 异常页面 服务器繁忙，请稍后重试
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="64">
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="com icon backup u" label="com icon backup u" enabled="true" visible="true" x="0" y="20" width="44" height="44"/>
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" enabled="true" visible="false" x="80" y="64" width="254" height="24"/>
                </XCUIElementTypeOther>
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="64" width="414" height="672">
                    <XCUIElementTypeImage type="XCUIElementTypeImage" name="network_err_dog" enabled="true" visible="false" x="152" y="164" width="110" height="110"/>
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="服务器繁忙，请稍后重试" name="服务器繁忙，请稍后重试" label="服务器繁忙，请稍后重试" enabled="true" visible="true" x="0" y="304" width="414" height="17"/>
                </XCUIElementTypeOther>
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="0" y="686" width="414" height="50">
                    <XCUIElementTypeCollectionView type="XCUIElementTypeCollectionView" enabled="true" visible="false" x="0" y="686" width="414" height="50"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" enabled="true" visible="false" x="0" y="686" width="414" height="50"/>
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" enabled="true" visible="false" x="16" y="701" width="382" height="20"/>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>
    """
    busyRetryChainList = [
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true", "x":"0", "y":"0", "width":"%s" % self.X, "height":"%s" % self.totalY}
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true", "x":"0", "width":"%s" % self.X}
        },
        {
            "tag": "XCUIElementTypeStaticText",
            "attrs": {"enabled":"true", "visible":"true", "value": "服务器繁忙，请稍后重试"}
        },
    ]
    busyRetrySoup = CommonUtils.bsChainFind(soup, busyRetryChainList)
    if busyRetrySoup:
        # find back button
        parentOtherSoup = busyRetrySoup.parent
        parentParentOtherSoup = parentOtherSoup.parent
        if parentParentOtherSoup:
            backupP = re.compile("backup") # "com icon backup u"
            backupSoup = parentParentOtherSoup.find(
                "XCUIElementTypeButton",
                attrs={"visible":"true", "enabled":"true", "name": backupP}
            )
            if backupSoup:
                self.clickElementCenterPosition(backupSoup)
                foundAndProcessedPopup = True
    return foundAndProcessedPopup
```

## iOSProcessPopupSystemAbnormalRetryRefresh

```python
def iOSProcessPopupSystemAbnormalRetryRefresh(self, soup):
    foundAndProcessedPopup = False
    """
        京东金融 异常页面 系统正在开小差，请稍后再试 再刷新下：
            <XCUIElementTypeOther type="XCUIElementTypeOther" name="系统正在开小差，请稍后再试 再刷新下" label="系统正在开小差，请稍后再试 再刷新下" enabled="true" visible="true" x="0" y="64" width="414" height="672">
                <XCUIElementTypeOther type="XCUIElementTypeOther" name="系统正在开小差，请稍后再试 再刷新下" label="系统正在开小差，请稍后再试 再刷新下" enabled="true" visible="true" x="0" y="64" width="414" height="672">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" name="系统正在开小差，请稍后再试" label="系统正在开小差，请稍后再试" enabled="true" visible="true" x="114" y="240" width="186" height="136">
                        <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="true" x="152" y="240" width="110" height="111"/>
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="系统正在开小差，请稍后再试" name="系统正在开小差，请稍后再试" label="系统正在开小差，请稍后再试" enabled="true" visible="true" x="114" y="360" width="186" height="16"/>
                    </XCUIElementTypeOther>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" name="再刷新下" label="再刷新下" enabled="true" visible="true" x="147" y="375" width="120" height="61">
                        <XCUIElementTypeOther type="XCUIElementTypeOther" name="再刷新下" label="再刷新下" enabled="true" visible="true" x="147" y="395" width="120" height="41"/>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>
    """
    retryRefreshChainList = [
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true", "x":"0", "width":"%s" % self.X}
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true", "x":"0", "width":"%s" % self.X}
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true", "name": "再刷新下"}
        },
    ]
    retryRefreshSoup = CommonUtils.bsChainFind(soup, retryRefreshChainList)
    if retryRefreshSoup:
        self.clickElementCenterPosition(retryRefreshSoup)
        foundAndProcessedPopup = True
    return foundAndProcessedPopup
```

## iOSProcessPopupUnderCloseButton

```python
def iOSProcessPopupUnderCloseButton(self, soup):
    """
        弹框 关闭按钮在弹框下面的 尤其是 name=""
    """
    foundAndProcessedPopup = False
    """
        途虎养车 弹框 关闭按钮在下面 新人618全品类消费券：
            <XCUIElementTypeWindow type="XCUIElementTypeWindow" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                            <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="72" y="190" width="270" height="356"/>
                            <XCUIElementTypeButton type="XCUIElementTypeButton" enabled="true" visible="true" x="72" y="190" width="270" height="356"/>
                            <XCUIElementTypeButton type="XCUIElementTypeButton" name="" label="" enabled="true" visible="true" x="187" y="589" width="40" height="41"/>
                        </XCUIElementTypeOther>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeWindow>
    """
    specialChar = ""
    underCloseChainList = [
        {
            "tag": "XCUIElementTypeWindow",
            "attrs": self.FullScreenAttrDict,
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": self.FullScreenAttrDict,
        },
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"enabled":"true", "visible":"true", "name": specialChar}
        },
    ]
    underCloseSoup = CommonUtils.bsChainFind(soup, underCloseChainList)
    if underCloseSoup:
        self.clickElementCenterPosition(underCloseSoup)
        foundAndProcessedPopup = True

    return foundAndProcessedPopup
```

## iOSProcessPopupPossibleCloseButton

```python
def isPopupWindowSize(self, curSize):
    """判断一个soup的宽高大小是否是弹框类窗口(Image,Other等）的大小"""
    # global FullScreenSize
    FullScreenSize = self.X * self.totalY
    curSizeRatio = curSize / FullScreenSize # 0.289
    PopupWindowSizeMinRatio = 0.25
    # PopupWindowSizeMaxRatio = 0.9
    PopupWindowSizeMaxRatio = 0.8
    # isSizeValid = curSizeRatio >= MinPopupWindowSizeRatio
    # is popup like window, size should large enough, but should not full screen
    isSizeValid = PopupWindowSizeMinRatio <= curSizeRatio <= PopupWindowSizeMaxRatio
    return isSizeValid

def isNormalButtonSize(self, curSize):
    """判断一个soup的宽高大小是否是普通的按钮大小"""
    NormalButtonSizeMin = 30*30
    NormalButtonSizeMax = 100*100
    isNormalSize = NormalButtonSizeMin <= curSize <= NormalButtonSizeMax
    return isNormalSize

def iOSProcessPopupPossibleCloseButton(self, soup):
    """
    必要 弹框 首单限时福利 右上角 关闭按钮
        特殊：但是内部无有效识别内容，比如name中包含close这类条件
        暂时只能特殊但通用处理：
            window -> other -> button
                且 button的 w和h基本一致，后者差距小于w和h最大值的10%
                以及 w和h都在一定（普通关闭按钮的大小）范围，比如 20-60

    必要 我的 蒙层弹框 首次使用引导页：
        基于上面逻辑：
            window->other->button
            button条件不变
        基础上，底层判断逻辑是：
            next的sibling后面，有且只有一个button，且有name
                且按钮宽高大小在合理范围，比如普通按钮的大小，算作为 30x30 < 按钮面积 < 100x100
                    意思是：引导页往往伴随着一个普通大小的按钮，供用户点击

    必要 弹框 推荐开通以下授权 关闭按钮在弹框下面：
        和前面逻辑略有不同
            Application -> window -> Other
        button条件不变
        其他条件类似于第一个，但是是 prev的sibling 是个Other元素，且面积是弹框类大小
    """
    foundAndProcessedPopup = False
    """
        必要 弹框 首单限时福利 右上角 关闭按钮：
            <XCUIElementTypeWindow type="XCUIElementTypeWindow" enabled="true" visible="true" x="0" y="0" width="414" height="736">
            。。。
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                    <XCUIElementTypeButton type="XCUIElementTypeButton" enabled="true" visible="true" x="339" y="122" width="31" height="32"/>
                    <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="true" x="44" y="174" width="326" height="388"/>
                </XCUIElementTypeOther>
            </XCUIElementTypeWindow>
        
        必要 我的 蒙层弹框 首次使用引导页：
            <XCUIElementTypeWindow type="XCUIElementTypeWindow" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                。。。
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="208"/>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="392" width="414" height="344"/>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="101" y="406" width="296" height="186">
                        <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="101" y="406" width="296" height="186"/>
                        <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="236" y="441" width="25" height="25"/>
                        <XCUIElementTypeButton type="XCUIElementTypeButton" enabled="true" visible="true" x="367" y="459" width="25" height="25"/>
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="发现新功能" name="发现新功能" label="发现新功能" enabled="true" visible="true" x="114" y="468" width="88" height="18"/>
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="必要地震预警通知功能（测试版）上线！ 曾提前61秒成功为成都预警四川长宁地震的成都高新减灾研究所与必要战略合作，为您的安全保驾护航！" name="必要地震预警通知功能（测试版）上线！ 曾提前61秒成功为成都预警四川长宁地震的成都高新减灾研究所与必要战略合作，为您的安全保驾护航！" label="必要地震预警通知功能（测试版）上线！ 曾提前61秒成功为成都预警四川长宁地震的成都高新减灾研究所与必要战略合作，为您的安全保驾护航！" enabled="true" visible="true" x="114" y="488" width="278" height="51"/>
                        <XCUIElementTypeButton type="XCUIElementTypeButton" name="开启预警" label="开启预警" enabled="true" visible="true" x="316" y="547" width="67" height="32"/>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
        
        必要 弹框 推荐开通以下授权 关闭按钮在弹框下面：：
            <XCUIElementTypeApplication type="XCUIElementTypeApplication" name="必要" label="必要" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeWindow type="XCUIElementTypeWindow" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                    。。。
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="44" y="233" width="326" height="270">
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="推荐开通以下授权" name="推荐开通以下授权" label="推荐开通以下授权" enabled="true" visible="true" x="53" y="257" width="308" height="24"/>
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="86" y="324" width="242" height="58">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="85" y="324" width="244" height="58">
                                <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="85" y="331" width="45" height="45"/>
                                <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="定位" name="定位" label="定位" enabled="true" visible="true" x="140" y="324" width="33" height="19"/>
                                <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="开启定位通知将在推荐、配送等方面为您提供更精准服务。" name="开启定位通知将在推荐、配送等方面为您提供更精准服务。" label="开启定位通知将在推荐、配送等方面为您提供更精准服务。" enabled="true" visible="true" x="140" y="353" width="189" height="29"/>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                        <XCUIElementTypeButton type="XCUIElementTypeButton" name="开启" label="开启" enabled="true" visible="true" x="69" y="429" width="276" height="50"/>
                    </XCUIElementTypeOther>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" enabled="true" visible="true" x="191" y="538" width="32" height="31"/>
    """
    # noNameP = re.compile("???")
    noNameP = False
    possibleCloseChainList = [
        {
            "tag": "XCUIElementTypeWindow",
            "attrs": {"enabled":"true", "visible":"true", "x":"0", "y":"0", "width":"%s" % self.X, "height":"%s" % self.totalY}
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true", "x":"0", "y":"0", "width":"%s" % self.X, "height":"%s" % self.totalY}
        },
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"enabled":"true", "visible":"true", "name": noNameP}
        },
    ]
    possibleCloseSoup = CommonUtils.bsChainFind(soup, possibleCloseChainList)

    isCloseUnderPopup = False
    if not possibleCloseSoup:
        # 尝试找 是否是 关闭按钮在弹框下面的 弹框
        closeUnderPopupChainList = [
            {
                "tag": "XCUIElementTypeApplication",
                "attrs": {"enabled":"true", "visible":"true", "x":"0", "y":"0", "width":"%s" % self.X, "height":"%s" % self.totalY}
            },
            {
                "tag": "XCUIElementTypeWindow",
                "attrs": {"enabled":"true", "visible":"true", "x":"0", "y":"0", "width":"%s" % self.X, "height":"%s" % self.totalY}
            },
            {
                "tag": "XCUIElementTypeButton",
                "attrs": {"enabled":"true", "visible":"true", "name": noNameP}
            },
        ]
        possibleCloseSoup = CommonUtils.bsChainFind(soup, closeUnderPopupChainList)
        if possibleCloseSoup:
            isCloseUnderPopup = True

    if possibleCloseSoup:
        # check is match close button rule or not
        isValidSize = False
        isAlmostSameWH = False
        # isNexSiblingImage = False
        # isImageSizeValid = False
        isPossibleClose = True

        if isPossibleClose:
            soupAttrDict = possibleCloseSoup.attrs # {'enabled': 'true', 'height': '32', 'type': 'XCUIElementTypeButton', 'visible': 'true', 'width': '31', 'x': '339', 'y': '122'}
            logging.debug("possibleCloseSoup soupAttrDict=%s", soupAttrDict)
            # x = int(soupAttrDict["x"])
            # y = int(soupAttrDict["y"])
            width = int(soupAttrDict["width"])
            height = int(soupAttrDict["height"])
            # ButtonMinWH = 20
            # ButtonMinWH = 30
            ButtonMinWH = 25
            ButtonMaxWH = 60
            isValidWidth = ButtonMinWH  <= width  <= ButtonMaxWH
            isValidHeight = ButtonMinWH <= height <= ButtonMaxWH
            isValidSize = isValidWidth and isValidHeight

            isPossibleClose = isValidSize

        if isPossibleClose:
            isAlmostSameWH = False
            isSameWH = width == height
            if isSameWH:
                isAlmostSameWH = isSameWH
            else:
                maxWH = max(width, height)
                diffWH = abs(width - height)
                MaxAllowDiffRatio = 0.1
                curDiffRatio = diffWH / maxWH
                isValidDiff = curDiffRatio <= MaxAllowDiffRatio
                isAlmostSameWH = isValidDiff

            isPossibleClose = isAlmostSameWH

        if isPossibleClose:
            # check next sibling is large Image
            # nextSiblingeSoup = possibleCloseSoup.next_sibling # '\n'
            # nextSiblingeSoupList = possibleCloseSoup.next_siblings
            nextSiblingeSoupGenerator = possibleCloseSoup.next_siblings
            nextSiblingeSoupList = list(nextSiblingeSoupGenerator)

            hasLargeImage = CommonUtils.isContainSpecificSoup(nextSiblingeSoupList, "XCUIElementTypeImage", self.isPopupWindowSize)
            isPossibleClose = hasLargeImage

            if not isPossibleClose:
                hasNormalButton = CommonUtils.isContainSpecificSoup(nextSiblingeSoupList, "XCUIElementTypeButton", self.isNormalButtonSize)
                isPossibleClose = hasNormalButton

            if not isPossibleClose:
                if isCloseUnderPopup:
                    # prevSiblingSoupGenerator = possibleCloseSoup.previous_siblings
                    # prevPrevSiblingSoupList = list(prevSiblingSoupGenerator)
                    # hasLargeOther = CommonUtils.isContainSpecificSoup(prevPrevSiblingSoupList, "XCUIElementTypeOther", self.isPopupWindowSize)
                    prevSiblingSoup = possibleCloseSoup.previous_sibling # '\n'
                    if prevSiblingSoup:
                        prevPrevSiblingSoup = prevSiblingSoup.previous_sibling # XCUIElementTypeOther
                        prevPrevSiblingSoupList = [ prevPrevSiblingSoup ]
                        hasLargeOther = CommonUtils.isContainSpecificSoup(prevPrevSiblingSoupList, "XCUIElementTypeOther", self.isPopupWindowSize)
                        isPossibleClose = hasLargeOther

        if isPossibleClose:
            foundAndProcessedPopup = self.findAndClickButtonElementBySoup(possibleCloseSoup)

    return foundAndProcessedPopup
```

## iOSProcessPopupCommonNameContainClose

```python
# def iOSProcessPopupUpperRightCommonClose(self, soup):
def iOSProcessPopupCommonNameContainClose(self, soup):
    foundAndProcessedPopup = False
    """
        京东金融 登录页 弹框 右上角 关闭按钮：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="64">
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" enabled="true" visible="false" x="65" y="20" width="284" height="44"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="com icon black close u" label="com icon black close u" enabled="true" visible="true" x="370" y="20" width="44" height="44"/>
                </XCUIElementTypeOther>

        京东金融 tab2财富 弹框 我是Max alertClose：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                。。。
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="jr fianncing alertClose@3x" label="jr fianncing alertClose@3x" enabled="true" visible="true" x="327" y="132" width="26" height="25"/>

        必要 弹框 右上角关闭按钮 name中有close：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="0" y="0" width="414" height="736"/>
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="44" y="150" width="327" height="436">
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="rights close white" label="rights close white" enabled="true" visible="true" x="338" y="150" width="32" height="32"/>
                    <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="true" x="44" y="199" width="326" height="387">
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="01 : 58 : 31 . 6 后过期" name="01 : 58 : 31 . 6 后过期" label="01 : 58 : 31 . 6 后过期" enabled="true" visible="true" x="137" y="476" width="140" height="18"/>
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="137" y="495" width="140" height="2"/>
                    </XCUIElementTypeImage>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>

        必要 验证码登录页 左上角 关闭 按钮 name中含close：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="64">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="64"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="login close" label="login close" enabled="true" visible="true" x="10" y="20" width="44" height="44"/>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="293" y="20" width="111" height="44">
                        <XCUIElementTypeButton type="XCUIElementTypeButton" name="密码登录" label="密码登录" enabled="true" visible="true" x="293" y="20" width="111" height="44"/>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
    """
    commonCloseP = re.compile("close", flags=re.I)
    # com icon black close u
    # jr fianncing alertClose@3x
    # rights close white
    # login close
    commonCloseChainList = [
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true", "x":"0", "y":"0", "width":"%s" % self.X, "height":"%s" % self.totalY}
        },
        {
            "tag": "XCUIElementTypeOther",
            # "attrs": {"enabled":"true", "visible":"true", "x":"0", "y":"0", "width":"%s" % self.X, "height":"%s" % self.totalY}
            # "attrs": {"enabled":"true", "visible":"true", "x":"0", "y":"0", "width":"%s" % self.X}
            "attrs": {"enabled":"true", "visible":"true"}
        },
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"enabled":"true", "visible":"true", "name": commonCloseP}
        },
    ]
    commonCloseSoup = CommonUtils.bsChainFind(soup, commonCloseChainList)
    if commonCloseSoup:
        # self.clickElementCenterPosition(commonCloseSoup) # sometime not work
        # foundAndProcessedPopup = True

        # so change to wda query element then click
        foundAndProcessedPopup = self.findAndClickButtonElementBySoup(commonCloseSoup)
    return foundAndProcessedPopup
```

## iOSProcessPopupUpperRightAlertClose

```python
# def iOSProcessPopupUpperRightAlertClose(self, soup):
#     foundAndProcessedPopup = False
#     """
#         京东金融 tab2财富 弹框 我是Max alertClose：
#             <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
#                 。。。
#                 <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
#                     <XCUIElementTypeButton type="XCUIElementTypeButton" name="jr fianncing alertClose@3x" label="jr fianncing alertClose@3x" enabled="true" visible="true" x="327" y="132" width="26" height="25"/>
#     """
#     alertCloseP = re.compile("alertClose")
#     alertCloseChainList = [
#         {
#             "tag": "XCUIElementTypeOther",
#             "attrs": {"enabled":"true", "visible":"true", "x":"0", "y":"0", "width":"%s" % self.X, "height":"%s" % self.totalY}
#         },
#         {
#             "tag": "XCUIElementTypeOther",
#             "attrs": {"enabled":"true", "visible":"true", "x":"0", "y":"0", "width":"%s" % self.X, "height":"%s" % self.totalY}
#         },
#         {
#             "tag": "XCUIElementTypeButton",
#             "attrs": {"enabled":"true", "visible":"true", "name": alertCloseP}
#         },
#     ]
#     alertCloseSoup = CommonUtils.bsChainFind(soup, alertCloseChainList)
#     if alertCloseSoup:
#         self.clickElementCenterPosition(alertCloseSoup)
#         foundAndProcessedPopup = True
#     return foundAndProcessedPopup

# def iOSProcessPopupUpperRightClose(self, soup):
#     foundAndProcessedPopup = False
#     """
#         京东金融 登录页 弹框 右上角 关闭按钮：
#                 <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
#                     <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="64">
#                         <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" enabled="true" visible="false" x="65" y="20" width="284" height="44"/>
#                         <XCUIElementTypeButton type="XCUIElementTypeButton" name="com icon black close u" label="com icon black close u" enabled="true" visible="true" x="370" y="20" width="44" height="44"/>
#                     </XCUIElementTypeOther>
#     """
#     containCloseP = re.compile("close") # com icon black close u
#     closeChainList = [
#         {
#             "tag": "XCUIElementTypeOther",
#             "attrs": {"enabled":"true", "visible":"true", "x":"0", "y":"0", "width":"%s" % self.X, "height":"%s" % self.totalY}
#         },
#         {
#             "tag": "XCUIElementTypeOther",
#             "attrs": {"enabled":"true", "visible":"true", "x":"0", "y":"0", "width":"%s" % self.X}
#         },
#         {
#             "tag": "XCUIElementTypeButton",
#             "attrs": {"enabled":"true", "visible":"true", "name": containCloseP}
#         },
#     ]
#     closeSoup = CommonUtils.bsChainFind(soup, closeChainList)
#     if closeSoup:
#         self.clickElementCenterPosition(closeSoup)
#         foundAndProcessedPopup = True
#     return foundAndProcessedPopup
```

## iOSProcessRightUpperJumpOver

```python
def iOSProcessRightUpperJumpOver(self, soup):
    foundAndProcessedPopup = False
    """
        京东金融 登录页 右上角 跳过：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="64">
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" enabled="true" visible="false" x="65" y="20" width="284" height="44"/>
                        <XCUIElementTypeButton type="XCUIElementTypeButton" name="跳过" label="跳过" enabled="true" visible="true" x="344" y="29" width="55" height="26"/>
                    </XCUIElementTypeOther>
    """
    jumpOverChainList = [
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true", "x":"0", "y":"0", "width":"%s" % self.X, "height":"%s" % self.totalY}
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true", "x":"0", "y":"0", "width":"%s" % self.X, "height":"%s" % self.totalY}
        },
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"enabled":"true", "visible":"true", "name":"跳过"}
        },
    ]
    jumpOverSoup = CommonUtils.bsChainFind(soup, jumpOverChainList)
    if jumpOverSoup:
        self.clickElementCenterPosition(jumpOverSoup)
        foundAndProcessedPopup = True
    return foundAndProcessedPopup
```

## iOSProcessAlertUseWirelessData

```python
def iOSProcessAlertUseWirelessData(self, soup):
    foundAndProcessedPopup = False
    """
        恒易贷 允许使用无线数据 无线局域网与蜂窝移动网络：
            <XCUIElementTypeAlert type="XCUIElementTypeAlert" name="允许“恒易贷”使用无线数据？" label="允许“恒易贷”使用无线数据？" enabled="true" visible="true" x="72" y="253" width="270" height="230">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="253" width="270" height="230">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="253" width="270" height="230">
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="253" width="270" height="230">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="253" width="270" height="230"/>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="253" width="270" height="230">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="253" width="270" height="230"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="253" width="270" height="230"/>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="253" width="270" height="230">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="253" width="270" height="97">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="253" width="270" height="97">
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="允许“恒易贷”使用无线数据？" name="允许“恒易贷”使用无线数据？" label="允许“恒易贷”使用无线数据？" enabled="true" visible="true" x="88" y="273" width="238" height="21"/>
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="关闭无线数据时，部分功能可能无法使用。" name="关闭无线数据时，部分功能可能无法使用。" label="关闭无线数据时，部分功能可能无法使用。" enabled="true" visible="true" x="88" y="297" width="238" height="32"/>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="349" width="270" height="1">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="349" width="270" height="1"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="349" width="270" height="1"/>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="350" width="270" height="133">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="350" width="270" height="133">
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="350" width="270" height="133">
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="350" width="270" height="44">
                                            <XCUIElementTypeButton type="XCUIElementTypeButton" name="无线局域网与蜂窝移动网络" label="无线局域网与蜂窝移动网络" enabled="true" visible="true" x="72" y="350" width="270" height="44"/>
                                        </XCUIElementTypeOther>
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="1">
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="394" width="270" height="1"/>
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="1"/>
                                        </XCUIElementTypeOther>
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="45">
                                            <XCUIElementTypeButton type="XCUIElementTypeButton" name="仅限无线局域网" label="仅限无线局域网" enabled="true" visible="true" x="72" y="394" width="270" height="45"/>
                                        </XCUIElementTypeOther>
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="438" width="270" height="1">
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="438" width="270" height="1"/>
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="438" width="270" height="1"/>
                                        </XCUIElementTypeOther>
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="438" width="270" height="45">
                                            <XCUIElementTypeButton type="XCUIElementTypeButton" name="不允许" label="不允许" enabled="true" visible="true" x="72" y="438" width="270" height="45"/>
                                        </XCUIElementTypeOther>
                                    </XCUIElementTypeOther>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeAlert>
    """
    wifiCellularChainList = [
        {
            "tag": "XCUIElementTypeAlert",
            "attrs": {"enabled":"true", "visible":"true"}
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true"}
        },
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"enabled":"true", "visible":"true", "name": "无线局域网与蜂窝移动网络"}
        },
    ]
    wifiCellularSoup = CommonUtils.bsChainFind(soup, wifiCellularChainList)
    if wifiCellularSoup:
        # found 无线局域网与蜂窝移动网络 but 
        # self.clickElementCenterPosition(wifiCellularSoup) # actually clicked 不允许 ！！！
        # foundAndProcessedPopup = True

        # # change to wda query element then click by element
        # curName = wifiCellularSoup.attrs["name"] # 好
        # wifiCellularButtonQuery = {"type":"XCUIElementTypeButton", "enabled":"true", "name": curName}
        # foundAndClicked = self.findAndClickElement(wifiCellularButtonQuery, isShowErrLog=False)
        # foundAndProcessedPopup = foundAndClicked
        foundAndProcessedPopup = self.findAndClickButtonElementBySoup(wifiCellularSoup)
    return foundAndProcessedPopup
```

## iOSProcessCommonAlertOk

```python
def iOSProcessCommonAlertOk(self, soup):
    """
        对于 alert弹框，点击 好
    """
    foundAndProcessedPopup = False
    """
        恒易贷 弹框 想访问您的通讯录 好：
            <XCUIElementTypeAlert type="XCUIElementTypeAlert" name="“恒易贷”想访问您的通讯录" label="“恒易贷”想访问您的通讯录" enabled="true" visible="true" x="72" y="298" width="270" height="141">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="298" width="270" height="141">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="298" width="270" height="141">
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="298" width="270" height="141">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="298" width="270" height="141"/>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="298" width="270" height="141">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="298" width="270" height="141"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="298" width="270" height="141"/>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="298" width="270" height="141">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="298" width="270" height="96">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="298" width="270" height="96">
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="“恒易贷”想访问您的通讯录" name="“恒易贷”想访问您的通讯录" label="“恒易贷”想访问您的通讯录" enabled="true" visible="true" x="88" y="317" width="238" height="21"/>
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="恒易贷需要获取您的通讯录信息，以提供更精准的服务" name="恒易贷需要获取您的通讯录信息，以提供更精准的服务" label="恒易贷需要获取您的通讯录信息，以提供更精准的服务" enabled="true" visible="true" x="88" y="341" width="238" height="33"/>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="1">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="394" width="270" height="1"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="1"/>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="45">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="45">
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="45">
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="135" height="45">
                                            <XCUIElementTypeButton type="XCUIElementTypeButton" name="不允许" label="不允许" enabled="true" visible="true" x="72" y="394" width="135" height="45"/>
                                        </XCUIElementTypeOther>
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="207" y="394" width="1" height="45">
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="207" y="394" width="1" height="45"/>
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="207" y="394" width="1" height="45"/>
                                        </XCUIElementTypeOther>
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="207" y="394" width="135" height="45">
                                            <XCUIElementTypeButton type="XCUIElementTypeButton" name="好" label="好" enabled="true" visible="true" x="207" y="394" width="135" height="45"/>
                                        </XCUIElementTypeOther>
                                    </XCUIElementTypeOther>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeAlert>
    """
    accessYourP = re.compile("想访问您的") # “恒易贷”想访问您的通讯录
    okChainList = [
        {
            "tag": "XCUIElementTypeAlert",
            "attrs": {"enabled":"true", "visible":"true", "name": accessYourP}
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true"}
        },
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"enabled":"true", "visible":"true", "name":"好"}
        },
    ]
    okSoup = CommonUtils.bsChainFind(soup, okChainList)
    if okSoup:
        # clickCenterPosition(curSession, okSoup.attrs)
        # foundAndProcessedPopup = True

        # # Note: seems actual click is No allow button? -> not work !
        # # change to wda query element then click for makesure work
        # curName = okSoup.attrs["name"] # 好
        # okButtonQuery = {"type":"XCUIElementTypeButton", "enabled":"true", "name": curName}
        # foundAndClicked = self.findAndClickElement(okButtonQuery, isShowErrLog=False)
        # foundAndProcessedPopup = foundAndClicked
        foundAndProcessedPopup = self.findAndClickButtonElementBySoup(okSoup)
    return foundAndProcessedPopup
```

## iOSProcessCommonAlertAllow

```python
def iOSProcessCommonAlertAllow(self, soup):
    foundAndProcessedPopup = False
    """
        京东金融 弹框 想给您发送信息 允许：
            <XCUIElementTypeAlert type="XCUIElementTypeAlert" name="“京东金融”想给您发送通知" label="“京东金融”想给您发送通知" enabled="true" visible="true" x="72" y="298" width="270" height="141">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="298" width="270" height="141">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="298" width="270" height="141">
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="298" width="270" height="141">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="298" width="270" height="141"/>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="298" width="270" height="141">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="298" width="270" height="141"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="298" width="270" height="141"/>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="298" width="270" height="141">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="298" width="270" height="96">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="298" width="270" height="96">
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="“京东金融”想给您发送通知" name="“京东金融”想给您发送通知" label="“京东金融”想给您发送通知" enabled="true" visible="true" x="88" y="317" width="238" height="21"/>
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="“通知”可能包括提醒、声音和图标标记。这些可在“设置”中配置。" name="“通知”可能包括提醒、声音和图标标记。这些可在“设置”中配置。" label="“通知”可能包括提醒、声音和图标标记。这些可在“设置”中配置。" enabled="true" visible="true" x="88" y="341" width="238" height="33"/>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="1">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="394" width="270" height="1"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="1"/>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="45">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="45">
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="45">
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="135" height="45">
                                            <XCUIElementTypeButton type="XCUIElementTypeButton" name="不允许" label="不允许" enabled="true" visible="true" x="72" y="394" width="135" height="45"/>
                                        </XCUIElementTypeOther>
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="207" y="394" width="1" height="45">
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="207" y="394" width="1" height="45"/>
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="207" y="394" width="1" height="45"/>
                                        </XCUIElementTypeOther>
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="207" y="394" width="135" height="45">
                                            <XCUIElementTypeButton type="XCUIElementTypeButton" name="允许" label="允许" enabled="true" visible="true" x="207" y="394" width="135" height="45"/>
                                        </XCUIElementTypeOther>
                                    </XCUIElementTypeOther>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeAlert>

        京东金融 弹框 允许“京东金融”在您使用该应用时访问您的位置吗？ 允许：
            <XCUIElementTypeAlert type="XCUIElementTypeAlert" name="允许“京东金融”在您使用该应用时访问您的位置吗？" label="允许“京东金融”在您使用该应用时访问您的位置吗？" enabled="true" visible="true" x="72" y="247" width="270" height="243">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="247" width="270" height="243">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="247" width="270" height="243">
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="247" width="270" height="243">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="247" width="270" height="243"/>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="247" width="270" height="243">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="247" width="270" height="243"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="247" width="270" height="243"/>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="247" width="270" height="243">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="247" width="270" height="198">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="247" width="270" height="198">
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="允许“京东金融”在您使用该应用时访问您的位置吗？" name="允许“京东金融”在您使用该应用时访问您的位置吗？" label="允许“京东金融”在您使用该应用时访问您的位置吗？" enabled="true" visible="true" x="88" y="266" width="238" height="43"/>
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="为了基于您当前所在位置更精准地为您推荐附近的优惠、提供周边商家搜索服务、提供限定销售区域的金融产品，请您允许京东金融使用位置权限。我们仅定位您当前所处的位置，不会追踪您的行踪轨迹。您可以在设置页面取消位置授权。" name="为了基于您当前所在位置更精准地为您推荐附近的优惠、提供周边商家搜索服务、提供限定销售区域的金融产品，请您允许京东金融使用位置权限。我们仅定位您当前所处的位置，不会追踪您的行踪轨迹。您可以在设置页面取消位置授权。" label="为了基于您当前所在位置更精准地为您推荐附近的优惠、提供周边商家搜索服务、提供限定销售区域的金融产品，请您允许京东金融使用位置权限。我们仅定位您当前所处的位置，不会追踪您的行踪轨迹。您可以在设置页面取消位置授权。" enabled="true" visible="true" x="88" y="312" width="238" height="113"/>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="445" width="270" height="1">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="445" width="270" height="1"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="445" width="270" height="1"/>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="445" width="270" height="45">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="445" width="270" height="45">
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="445" width="270" height="45">
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="445" width="135" height="45">
                                            <XCUIElementTypeButton type="XCUIElementTypeButton" name="不允许" label="不允许" enabled="true" visible="true" x="72" y="445" width="135" height="45"/>
                                        </XCUIElementTypeOther>
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="207" y="445" width="1" height="45">
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="207" y="445" width="1" height="45"/>
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="207" y="445" width="1" height="45"/>
                                        </XCUIElementTypeOther>
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="207" y="445" width="135" height="45">
                                            <XCUIElementTypeButton type="XCUIElementTypeButton" name="允许" label="允许" enabled="true" visible="true" x="207" y="445" width="135" height="45"/>
                                        </XCUIElementTypeOther>
                                    </XCUIElementTypeOther>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeAlert>

        京东金融 弹框 允许在您并未使用该应用时访问您的位置吗？仅在使用应用期间 始终允许：
            <XCUIElementTypeAlert type="XCUIElementTypeAlert" name="允许“京东金融”在您并未使用该应用时访问您的位置吗？" label="允许“京东金融”在您并未使用该应用时访问您的位置吗？" enabled="true" visible="true" x="72" y="224" width="270" height="288">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="224" width="270" height="288">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="224" width="270" height="288">
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="224" width="270" height="288">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="224" width="270" height="288"/>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="224" width="270" height="288">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="224" width="270" height="288"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="224" width="270" height="288"/>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="224" width="270" height="288">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="224" width="270" height="199">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="224" width="270" height="199">
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="允许“京东金融”在您并未使用该应用时访问您的位置吗？" name="允许“京东金融”在您并未使用该应用时访问您的位置吗？" label="允许“京东金融”在您并未使用该应用时访问您的位置吗？" enabled="true" visible="true" x="88" y="244" width="238" height="43"/>
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="为了基于您当前所在位置更精准地为您推荐附近的优惠、提供周边商家搜索服务、提供限定销售区域的金融产品，请您允许京东金融使用位置权限。我们仅定位您当前所处的位置，不会追踪您的行踪轨迹。您可以在设置页面取消位置授权。" name="为了基于您当前所在位置更精准地为您推荐附近的优惠、提供周边商家搜索服务、提供限定销售区域的金融产品，请您允许京东金融使用位置权限。我们仅定位您当前所处的位置，不会追踪您的行踪轨迹。您可以在设置页面取消位置授权。" label="为了基于您当前所在位置更精准地为您推荐附近的优惠、提供周边商家搜索服务、提供限定销售区域的金融产品，请您允许京东金融使用位置权限。我们仅定位您当前所处的位置，不会追踪您的行踪轨迹。您可以在设置页面取消位置授权。" enabled="true" visible="true" x="88" y="290" width="238" height="112"/>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="422" width="270" height="1">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="422" width="270" height="1"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="422" width="270" height="1"/>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="423" width="270" height="89">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="423" width="270" height="89">
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="423" width="270" height="89">
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="423" width="270" height="44">
                                            <XCUIElementTypeButton type="XCUIElementTypeButton" name="仅在使用应用期间" label="仅在使用应用期间" enabled="true" visible="true" x="72" y="423" width="270" height="44"/>
                                        </XCUIElementTypeOther>
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="467" width="270" height="1">
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="467" width="270" height="1"/>
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="467" width="270" height="1"/>
                                        </XCUIElementTypeOther>
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="467" width="270" height="45">
                                            <XCUIElementTypeButton type="XCUIElementTypeButton" name="始终允许" label="始终允许" enabled="true" visible="true" x="72" y="467" width="270" height="45"/>
                                        </XCUIElementTypeOther>
                                    </XCUIElementTypeOther>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeAlert>

        恒易贷 想给您发送通知 允许：
            <XCUIElementTypeAlert type="XCUIElementTypeAlert" name="“恒易贷”想给您发送通知" label="“恒易贷”想给您发送通知" enabled="true" visible="true" x="72" y="298" width="270" height="141">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="298" width="270" height="141">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="298" width="270" height="141">
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="298" width="270" height="141">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="298" width="270" height="141"/>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="298" width="270" height="141">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="298" width="270" height="141"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="298" width="270" height="141"/>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="298" width="270" height="141">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="298" width="270" height="96">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="298" width="270" height="96">
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="“恒易贷”想给您发送通知" name="“恒易贷”想给您发送通知" label="“恒易贷”想给您发送通知" enabled="true" visible="true" x="88" y="317" width="238" height="21"/>
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="“通知”可能包括提醒、声音和图标标记。这些可在“设置”中配置。" name="“通知”可能包括提醒、声音和图标标记。这些可在“设置”中配置。" label="“通知”可能包括提醒、声音和图标标记。这些可在“设置”中配置。" enabled="true" visible="true" x="88" y="341" width="238" height="33"/>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="1">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="394" width="270" height="1"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="1"/>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="45">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="45">
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="45">
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="135" height="45">
                                            <XCUIElementTypeButton type="XCUIElementTypeButton" name="不允许" label="不允许" enabled="true" visible="true" x="72" y="394" width="135" height="45"/>
                                        </XCUIElementTypeOther>
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="207" y="394" width="1" height="45">
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="207" y="394" width="1" height="45"/>
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="207" y="394" width="1" height="45"/>
                                        </XCUIElementTypeOther>
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="207" y="394" width="135" height="45">
                                            <XCUIElementTypeButton type="XCUIElementTypeButton" name="允许" label="允许" enabled="true" visible="true" x="207" y="394" width="135" height="45"/>
                                        </XCUIElementTypeOther>
                                    </XCUIElementTypeOther>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeAlert>
    """
    # allowP = re.compile("(始终)?允许") # will also match "不允许"
    allowP = re.compile("^(始终)?允许$") # only match "允许" "始终允许"
    allowChainList = [
        {
            "tag": "XCUIElementTypeAlert",
            "attrs": {"enabled":"true", "visible":"true"}
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true"}
        },
        {
            "tag": "XCUIElementTypeButton",
            # "attrs": {"enabled":"true", "visible":"true", "name":"允许"}
            "attrs": {"enabled":"true", "visible":"true", "name": allowP}
        },
    ]
    allowSoup = CommonUtils.bsChainFind(soup, allowChainList)
    if allowSoup:
        # clickCenterPosition(curSession, allowSoup.attrs)
        # foundAndProcessedPopup = True
        
        # # above click position not work for 允许 -> actually click 不允许 !!!
        # # change to use wda to find 允许 then click element
        # curName = allowSoup.attrs["name"] # 允许 / 始终允许
        # # allowButtonQuery = {"type":"XCUIElementTypeButton", "enabled":"true", "name": "允许"}
        # allowButtonQuery = {"type":"XCUIElementTypeButton", "enabled":"true", "name": curName}
        # foundAndClickeAllow = self.findAndClickElement(allowButtonQuery, isShowErrLog=False)
        # foundAndProcessedPopup = foundAndClickeAllow
        foundAndProcessedPopup = self.findAndClickButtonElementBySoup(allowSoup)
    return foundAndProcessedPopup
```

## iOSProcessUserPrivacyProtocol

```python
def iOSProcessUserPrivacyProtocol(self, soup):
    foundAndProcessedPopup = False
    """
        恒易贷 首次进入 用户隐私保护协议 点击 我接受
    """

    """
        恒易贷 用户意思保护协议 我接收：
            <XCUIElementTypeWindow type="XCUIElementTypeWindow" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="用户隐私保护协议" name="用户隐私保护协议" label="用户隐私保护协议" enabled="true" visible="true" x="93" y="86" width="228" height="34"/>
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="感谢您使用恒易贷APP！" name="感谢您使用恒易贷APP！" label="感谢您使用恒易贷APP！" enabled="true" visible="true" x="35" y="143" width="156" height="24"/>
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="恒易贷非常重视您的隐私保护，并将使用多项安全防护措施来保护您的个人信息。请您在使用我们的服务前认真阅读本隐私政策，我们将通过本政策向您说明我们会如何收集、使用及保护您的个人信息。" name="恒易贷非常重视您的隐私保护，并将使用多项安全防护措施来保护您的个人信息。请您在使用我们的服务前认真阅读本隐私政策，我们将通过本政策向您说明我们会如何收集、使用及保护您的个人信息。" label="恒易贷非常重视您的隐私保护，并将使用多项安全防护措施来保护您的个人信息。请您在使用我们的服务前认真阅读本隐私政策，我们将通过本政策向您说明我们会如何收集、使用及保护您的个人信息。" enabled="true" visible="true" x="35" y="178" width="344" height="86"/>
                        <XCUIElementTypeOther type="XCUIElementTypeOther" name="点击我接受即表示您已阅读并同意《用户注册协议》和《用户隐私保护协议》，请详细阅读相关条款，如有疑问可拨打95713。" label="点击我接受即表示您已阅读并同意《用户注册协议》和《用户隐私保护协议》，请详细阅读相关条款，如有疑问可拨打95713。" enabled="true" visible="true" x="35" y="275" width="344" height="59"/>
                        <XCUIElementTypeButton type="XCUIElementTypeButton" name="我接受" label="我接受" enabled="true" visible="true" x="67" y="393" width="280" height="44"/>
                        <XCUIElementTypeButton type="XCUIElementTypeButton" name="我拒绝" label="我拒绝" enabled="true" visible="true" x="182" y="444" width="50" height="33"/>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeWindow>
    """
    userPrivacyIAcceptChainList = [
        {
            "tag": "XCUIElementTypeWindow",
            "attrs": {"enabled":"true", "visible":"true", "x":"0", "y":"0", "width":"%s" % self.X, "height":"%s" % self.totalY}
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true", "x":"0", "y":"0", "width":"%s" % self.X, "height":"%s" % self.totalY}
        },
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"enabled":"true", "visible":"true", "name": "我接受"}
        },
    ]
    userPrivacyIAcceptSoup = CommonUtils.bsChainFind(soup, userPrivacyIAcceptChainList)
    if userPrivacyIAcceptSoup:
        self.clickElementCenterPosition(userPrivacyIAcceptSoup)
        foundAndProcessedPopup = True
    return foundAndProcessedPopup
```

## iOSProcessCommonUserAgreementAgree

```python
def iOSProcessCommonUserAgreementAgree(self, soup):
    """
        京东金融、必要 首次进入 授权协议提示 点击 同意
    """
    foundAndProcessedAgreement = False
    """
        京东金融 首次进入 协议提示 同意：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
            。。。
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="579" width="414" height="157">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" name="有关个人信息收集、使用更详细的约定请阅读《京东金融隐私政策》全文。我们承诺会不断完善安全技术和制度措施，确保您个人信息的安全。" label="有关个人信息收集、使用更详细的约定请阅读《京东金融隐私政策》全文。我们承诺会不断完善安全技术和制度措施，确保您个人信息的安全。" enabled="true" visible="true" x="16" y="595" width="382" height="59"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="不同意" label="不同意" enabled="true" visible="true" x="16" y="670" width="186" height="50"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="同意" label="同意" enabled="true" visible="true" x="212" y="670" width="186" height="50"/>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>

        必要 用户服务协议及必要隐私政策 同意：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="38" y="163" width="338" height="410">
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="用户服务协议及必要隐私政策" name="用户服务协议及必要隐私政策" label="用户服务协议及必要隐私政策" enabled="true" visible="true" x="38" y="163" width="338" height="53"/>
                    <XCUIElementTypeTextView type="XCUIElementTypeTextView" value="在您注册成为必要用户的过程中，您需要完成我们的注册流程并通过点击同意的形式在线签署以下协议，请您务必仔细阅读、充分理解协议中的条款内容后再点击同意（尤其是以粗体标识的条款，因为这些条款可能会明确您应履行的义务或对您的权利有所限制）：&#10;《用户服务协议》&#10;《必要隐私政策》&#10;【请您注意】如果您不同意上述协议或其中任何条款约定，请您停止注册。您停止注册后将仅可以浏览我们的商品信息但无法享受我们的产品或服务。如您按照注册流程提示填写信息、阅读并点击同意上述协议且完成全部注册流程后，即表示您已充分阅读、理解并接受协议的全部内容；并表明您也同意必要可以依据以上的隐私政策内容来处理您的个人信息。如您对以上协议内容有任何疑问，您可随时与必要客服联系。" enabled="true" visible="true" x="60" y="215" width="294" height="241"/>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="60" y="455" width="294" height="2"/>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="60" y="467" width="294" height="56"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="不同意" label="不同意" enabled="true" visible="true" x="38" y="518" width="169" height="55"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="同意" label="同意" enabled="true" visible="true" x="206" y="518" width="170" height="55"/>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>

        斑马AI课 弹框 个人信息保护政策 同意：
            <XCUIElementTypeWindow type="XCUIElementTypeWindow" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="0" y="0" width="414" height="736"/>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="0" y="0" width="414" height="736"/>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="47" y="148" width="320" height="440">
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="个人信息保护政策" name="个人信息保护政策" label="个人信息保护政策" enabled="true" visible="true" x="47" y="173" width="320" height="23"/>
                        <XCUIElementTypeTextView type="XCUIElementTypeTextView" value="感谢您信任并使用斑马AI课的产品和服务。我们根据最新的法律法规、监管政策要求，更新了《隐私政策》，特向您推送本提示。请您仔细阅读并充分理解相关条款。&#10;斑马AI课会通过《隐私政策》帮助您了解我们手机、使用、存储个人信息的情况，以及您享有的相关权利。&#10;点击【同意】，表示您已阅读并同意相关协议条款，斑马AI课将尽全力保障您的合法权益并继续为您提供优质的产品和服务。&#10;* 您可通过阅读完整版《用户注册协议》《用户隐私协议》《儿童隐私政策》了解详尽条款内容。" enabled="true" visible="true" x="67" y="210" width="280" height="301">
                            <XCUIElementTypeLink type="XCUIElementTypeLink" name="《用户注册协议》" label="《用户注册协议》" enabled="true" visible="true" x="210" y="470" width="115" height="25"/>
                            <XCUIElementTypeLink type="XCUIElementTypeLink" name="《用户隐私协议》" label="《用户隐私协议》" enabled="true" visible="true" x="72" y="493" width="115" height="25"/>
                            <XCUIElementTypeLink type="XCUIElementTypeLink" name="《儿童隐私政策》" label="《儿童隐私政策》" enabled="true" visible="true" x="186" y="493" width="115" height="25"/>
                        </XCUIElementTypeTextView>
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="47" y="525" width="320" height="2"/>
                        <XCUIElementTypeButton type="XCUIElementTypeButton" name="同意" label="同意" enabled="true" visible="true" x="77" y="540" width="260" height="34"/>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeWindow>
    """
    commonAgreeChainList = [
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true", "x":"0", "y":"0", "width":"%s" % self.X, "height":"%s" % self.totalY}
        },
        {
            "tag": "XCUIElementTypeOther",
            # "attrs": {"enabled":"true", "visible":"true", "x":"0", "width":"%s" % self.X}
            "attrs": {"enabled":"true", "visible":"true"}
        },
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"enabled":"true", "visible":"true", "name":"同意"}
        },
    ]
    commonAgreeSoup = CommonUtils.bsChainFind(soup, commonAgreeChainList)
    if commonAgreeSoup:
        self.clickElementCenterPosition(commonAgreeSoup)
        foundAndProcessedAgreement = True
    return foundAndProcessedAgreement
```

## iOSProcessPopupIKnow

```python
def iOSProcessPopupIKnow(self, soup):
    """
        京东金融 首次打开 协议弹框 我知道了
    """
    foundAndProcessedPopup = False
    """
        京东金融 弹框 我知道了：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="67" y="189" width="280" height="358">
                    <XCUIElementTypeScrollView type="XCUIElementTypeScrollView" enabled="true" visible="true" x="67" y="217" width="280" height="280">
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="权限调用说明" name="权限调用说明" label="权限调用说明" enabled="true" visible="true" x="77" y="217" width="260" height="21"/>
                        <XCUIElementTypeOther type="XCUIElementTypeOther" name="在提供服务过程中，我们可能需要调用您的以下重要设备权限，我们将在首次调用时逐项询问您是否允许使用该权限。您可以在我们询问时开启相关权限，也可以在设备系统“设置”里管理相关权限：&#10;1、相机、相册权限：向您提供扫一扫、头像设置、客服、评论或分享、人脸等图像识别服务时，您可以通过开通相机权限和/或相册权限以便进行实时拍摄和图片/视频上传。&#10;2、位置权限：基于您当前位置为您自动定位商家网点（银行、加油站等）、推荐周边服务及优惠、提供限定销售区域的金融服务时，您可以通过开启位置权限以便查看或获取当前所在区域的服务。&#10;3、通讯录权限：向您提供手机充值、转账、还款服务时，您可以选择直接从通讯录中选择并导入选定的特定联系人信息。&#10;4、日历权限：向您提供早起打卡提醒服务时，您可以通过开启日历权限便捷管理您的自定义事项、设定重要事项提醒。&#10;5、麦克风权限：向您提供语音搜索或语音客服服务时，您可以开启麦克风权限点击语音按钮进行录音，以便我们收集您的语音内容并进行必要的处理。&#10;6、面容 ID权限：向您提供面容 ID支付、面容 ID解锁服务时，您可以开启面容 ID权限并在您的设备上完成面容 ID验证，以便我们接收使用验证结果。&#10;7、蓝牙权限：向您提供手环服务时，您可以开启蓝牙权限搜索周边设备，以便您的手环能够与京东金融APP建立正常连接。&#10;&#10;更多权限信息说明，请点击查看完整版《京东金融隐私政策》。&#10;&#10;" label="在提供服务过程中，我们可能需要调用您的以下重要设备权限，我们将在首次调用时逐项询问您是否允许使用该权限。您可以在我们询问时开启相关权限，也可以在设备系统“设置”里管理相关权限：&#10;1、相机、相册权限：向您提供扫一扫、头像设置、客服、评论或分享、人脸等图像识别服务时，您可以通过开通相机权限和/或相册权限以便进行实时拍摄和图片/视频上传。&#10;2、位置权限：基于您当前位置为您自动定位商家网点（银行、加油站等）、推荐周边服务及优惠、提供限定销售区域的金融服务时，您可以通过开启位置权限以便查看或获取当前所在区域的服务。&#10;3、通讯录权限：向您提供手机充值、转账、还款服务时，您可以选择直接从通讯录中选择并导入选定的特定联系人信息。&#10;4、日历权限：向您提供早起打卡提醒服务时，您可以通过开启日历权限便捷管理您的自定义事项、设定重要事项提醒。&#10;5、麦克风权限：向您提供语音搜索或语音客服服务时，您可以开启麦克风权限点击语音按钮进行录音，以便我们收集您的语音内容并进行必要的处理。&#10;6、面容 ID权限：向您提供面容 ID支付、面容 ID解锁服务时，您可以开启面容 ID权限并在您的设备上完成面容 ID验证，以便我们接收使用验证结果。&#10;7、蓝牙权限：向您提供手环服务时，您可以开启蓝牙权限搜索周边设备，以便您的手环能够与京东金融APP建立正常连接。&#10;&#10;更多权限信息说明，请点击查看完整版《京东金融隐私政策》。&#10;&#10;" enabled="true" visible="true" x="77" y="257" width="261" height="888"/>
                    </XCUIElementTypeScrollView>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="67" y="497" width="280" height="1"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="我知道了" label="我知道了" enabled="true" visible="true" x="67" y="497" width="280" height="50"/>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>

        恒易贷 权限获取说明 我知道了：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736"/>
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="37" y="151" width="340" height="434">
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="权限获取说明" name="权限获取说明" label="权限获取说明" enabled="true" visible="true" x="158" y="171" width="98" height="20"/>
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="为了给您提供优质的服务，在您借款过程中，恒易贷需要您授权开启以下权限：" name="为了给您提供优质的服务，在您借款过程中，恒易贷需要您授权开启以下权限：" label="为了给您提供优质的服务，在您借款过程中，恒易贷需要您授权开启以下权限：" enabled="true" visible="true" x="62" y="196" width="290" height="33"/>
                    <XCUIElementTypeTable type="XCUIElementTypeTable" enabled="true" visible="true" x="62" y="246" width="290" height="274">
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="62" y="246" width="290" height="1"/>
                        <XCUIElementTypeCell type="XCUIElementTypeCell" enabled="true" visible="true" x="62" y="246" width="290" height="55">
                            <XCUIElementTypeImage type="XCUIElementTypeImage" name="icon_notice" enabled="true" visible="false" x="62" y="246" width="17" height="19"/>
                            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="通知权限" name="通知权限" label="通知权限" enabled="true" visible="true" x="87" y="246" width="62" height="24"/>
                            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="用于为您提供恒易贷或合作方的产品或活动信息" name="用于为您提供恒易贷或合作方的产品或活动信息" label="用于为您提供恒易贷或合作方的产品或活动信息" enabled="true" visible="true" x="87" y="271" width="265" height="19"/>
                        </XCUIElementTypeCell>
                        <XCUIElementTypeCell type="XCUIElementTypeCell" enabled="true" visible="true" x="62" y="300" width="290" height="73">
                            <XCUIElementTypeImage type="XCUIElementTypeImage" name="icon_location" enabled="true" visible="false" x="62" y="300" width="17" height="19"/>
                            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="定位服务权限" name="定位服务权限" label="定位服务权限" enabled="true" visible="true" x="87" y="300" width="92" height="24"/>
                            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="用于获取您的地理位置信息，以为您提供匹配的风险信资评估服务" name="用于获取您的地理位置信息，以为您提供匹配的风险信资评估服务" label="用于获取您的地理位置信息，以为您提供匹配的风险信资评估服务" enabled="true" visible="true" x="87" y="325" width="265" height="33"/>
                        </XCUIElementTypeCell>
                        <XCUIElementTypeCell type="XCUIElementTypeCell" enabled="true" visible="true" x="62" y="372" width="290" height="73">
                            <XCUIElementTypeImage type="XCUIElementTypeImage" name="icon_mail" enabled="true" visible="false" x="62" y="372" width="17" height="19"/>
                            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="通讯录权限" name="通讯录权限" label="通讯录权限" enabled="true" visible="true" x="87" y="372" width="77" height="24"/>
                            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="用于调用设备通讯录以方便您选择联系人电话号码，以辅助填写信息" name="用于调用设备通讯录以方便您选择联系人电话号码，以辅助填写信息" label="用于调用设备通讯录以方便您选择联系人电话号码，以辅助填写信息" enabled="true" visible="true" x="87" y="397" width="265" height="33"/>
                        </XCUIElementTypeCell>
                        <XCUIElementTypeCell type="XCUIElementTypeCell" enabled="true" visible="true" x="62" y="444" width="290" height="73">
                            <XCUIElementTypeImage type="XCUIElementTypeImage" name="icon_camera" enabled="true" visible="false" x="62" y="444" width="17" height="19"/>
                            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="相机权限" name="相机权限" label="相机权限" enabled="true" visible="true" x="87" y="444" width="62" height="24"/>
                            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="用于获取您的身份证信息以及脸部图像信息，以核实您身份的真实性" name="用于获取您的身份证信息以及脸部图像信息，以核实您身份的真实性" label="用于获取您的身份证信息以及脸部图像信息，以核实您身份的真实性" enabled="true" visible="true" x="87" y="469" width="265" height="33"/>
                        </XCUIElementTypeCell>
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="62" y="516" width="290" height="1"/>
                    </XCUIElementTypeTable>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="我知道了" label="我知道了" enabled="true" visible="true" x="92" y="520" width="230" height="38"/>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>
    """
    iKnowChainList = [
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true", "x":"0", "y":"0", "width":"%s" % self.X, "height":"%s" % self.totalY}
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true"}
        },
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"enabled":"true", "visible":"true", "name":"我知道了"}
        },
    ]
    iKnowSoup = CommonUtils.bsChainFind(soup, iKnowChainList)
    if iKnowSoup:
        self.clickElementCenterPosition(iKnowSoup)
        foundAndProcessedPopup = True
    return foundAndProcessedPopup
```

## iOSProcessPopupPhoneCall

```python
def iOSProcessPopupPhoneCall(self, soup):
    """iOS 通用弹框 呼叫 -> 点击 取消"""
    foundAndProcessedPopup = False
    """
        iOS 通用弹框 呼叫 打电话：
            <XCUIElementTypeAlert type="XCUIElementTypeAlert" name="‭400 186 6078‬" label="‭400 186 6078‬" enabled="true" visible="true" x="72" y="316" width="270" height="105">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="316" width="270" height="105">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="316" width="270" height="105">
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="316" width="270" height="105">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="316" width="270" height="105"/>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="316" width="270" height="105">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="316" width="270" height="105"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="316" width="270" height="105"/>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="316" width="270" height="105">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="316" width="270" height="60">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="316" width="270" height="60">
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="‭400 186 6078‬" name="‭400 186 6078‬" label="‭400 186 6078‬" enabled="true" visible="true" x="88" y="335" width="238" height="21"/>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="376" width="270" height="1">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="376" width="270" height="1"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="376" width="270" height="1"/>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="376" width="270" height="45">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="376" width="270" height="45">
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="376" width="270" height="45">
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="376" width="135" height="45">
                                            <XCUIElementTypeButton type="XCUIElementTypeButton" name="取消" label="取消" enabled="true" visible="true" x="72" y="376" width="135" height="45"/>
                                        </XCUIElementTypeOther>
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="207" y="376" width="1" height="45">
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="207" y="376" width="1" height="45"/>
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="207" y="376" width="1" height="45"/>
                                        </XCUIElementTypeOther>
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="207" y="376" width="135" height="45">
                                            <XCUIElementTypeButton type="XCUIElementTypeButton" name="呼叫" label="呼叫" enabled="true" visible="true" x="207" y="376" width="135" height="45"/>
                                        </XCUIElementTypeOther>
                                    </XCUIElementTypeOther>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeAlert>
    """
    callChainList = [
        {
            "tag": "XCUIElementTypeAlert",
            "attrs": {"enabled":"true", "visible":"true"}
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true"}
        },
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"enabled":"true", "visible":"true", "name":"呼叫"}
        },
    ]
    callSoup = CommonUtils.bsChainFind(soup, callChainList)
    if callSoup:
        # parentOtherSoup = callSoup.parent
        # if parentOtherSoup:
        #     parentParentOtherSoup = parentOtherSoup.parent
        #     if parentParentOtherSoup:
        #         cancelSoup = parentParentOtherSoup.find(
        #             "XCUIElementTypeButton",
        #             attrs={"enabled":"true", "visible":"true", "name": "取消"}
        #         )
        #         if cancelSoup:
        #             clickCenterPosition(curSession, cancelSoup.attrs)
        #             foundAndProcessedPopup = True

        # # above click position not work for 取消 !!!
        # # change to find 取消 then click element
        # cancelButtonQuery = {"type":"XCUIElementTypeButton", "enabled":"true", "visible":"true", "name": "取消"}
        # foundAndClickeCancel = self.findAndClickElement(cancelButtonQuery, isShowErrLog=False)
        # foundAndProcessedPopup = foundAndClickeCancel
        foundAndProcessedPopup = self.findAndClickButtonElementBySoup(curButtonName="取消")
    return foundAndProcessedPopup
```

## iOSProcessCommonPopupWindowUpperRightClose

```python
# 注：此逻辑经常误判，没有弹框误以为有弹框，所以放弃此逻辑
def iOSProcessCommonPopupWindowUpperRightClose(self, soup):
    """iOS 常见 通用弹框： 中间有多层 XCUIElementTypeOther 且 是全屏大小
        enabled="true" visible="true" x="0" y="0" width="414" height="736"
            至少3层，其下 再去找 就是 弹框本身最外层元素了
                然后尝试点击右上角关闭按钮
    """
    foundAndProcessedPopup = False
    """
    弹框中找 外层Other内部的 非x=0 y=0的元素，则为弹框区域 计算出其右上角的区域，点击 尝试关闭 弹框
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" name="姜老师 姜老师已经帮助超过800多名患者发起了筹款，经验丰富。关于筹款的问题您都可以找她咨询。 gongyiwuyouchou 一键复制老师微信 添加老师微信，专享1对1细致服务，立即开始咨询吧！" label="姜老师 姜老师已经帮助超过800多名患者发起了筹款，经验丰富。关于筹款的问题您都可以找她咨询。 gongyiwuyouchou 一键复制老师微信 添加老师微信，专享1对1细致服务，立即开始咨询吧！" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                        <XCUIElementTypeOther type="XCUIElementTypeOther" name="姜老师 姜老师已经帮助超过800多名患者发起了筹款，经验丰富。关于筹款的问题您都可以找她咨询。 gongyiwuyouchou 一键复制老师微信 添加老师微信，专享1对1细致服务，立即开始咨询吧！" label="姜老师 姜老师已经帮助超过800多名患者发起了筹款，经验丰富。关于筹款的问题您都可以找她咨询。 gongyiwuyouchou 一键复制老师微信 添加老师微信，专享1对1细致服务，立即开始咨询吧！" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" name="姜老师 姜老师已经帮助超过800多名患者发起了筹款，经验丰富。关于筹款的问题您都可以找她咨询。 gongyiwuyouchou 一键复制老师微信 添加老师微信，专享1对1细致服务，立即开始咨询吧！" label="姜老师 姜老师已经帮助超过800多名患者发起了筹款，经验丰富。关于筹款的问题您都可以找她咨询。 gongyiwuyouchou 一键复制老师微信 添加老师微信，专享1对1细致服务，立即开始咨询吧！" enabled="true" visible="true" x="20" y="200" width="374" height="263">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="20" y="200" width="374" height="36"/>
            。。。
    """
    allTopOtherSoupList = soup.find_all(
        "XCUIElementTypeOther",
        attrs={"enabled":"true", "visible":"true", "x":"0", "y":"0", "width":"%s" % self.X, "height":"%s" % self.totalY}
    )
    for eachTopOtherSoup in allTopOtherSoupList:
        popupTopOtherSoup = self.findPopupTopFrameElement(eachTopOtherSoup)
        if popupTopOtherSoup:
            curAttrsDict = popupTopOtherSoup.attrs
            curX = int(curAttrsDict["x"])
            curY = int(curAttrsDict["y"])
            curWidth = int(curAttrsDict["width"])
            curHeight = int(curAttrsDict["height"])
            curX1 = curX + curWidth
            curY1 = curY + curHeight
            PossibleCloseButtonWidth = 40
            PossibleCloseButtonHeight = 40
            possibleCloseButtonX0 = curX1 - PossibleCloseButtonWidth
            # possibleCloseButtonY0 = curY + PossibleCloseButtonHeight
            possibleCloseButtonY0 = curY
            possibleCloseButtonX1 = curX1
            possibleCloseButtonY1 = curY + PossibleCloseButtonHeight
            # possibleCloseButtonCenterX = possibleCloseButtonX0 + int(PossibleCloseButtonWidth / 2)
            # possibleCloseButtonCenterY = possibleCloseButtonY0 + int(PossibleCloseButtonHeight / 2)
            possibleCloseButtonCenterX = int( (possibleCloseButtonX0 + possibleCloseButtonX1) / 2)
            possibleCloseButtonCenterY = int( (possibleCloseButtonY0 + possibleCloseButtonY1) / 2)
            possibleCloseButtonCenterPositon = (possibleCloseButtonCenterX, possibleCloseButtonCenterY)
            self.tap(possibleCloseButtonCenterPositon)
            foundAndProcessedPopup = True
            break

    return foundAndProcessedPopup
```

## findPopupTopFrameElement

```python
def findPopupTopFrameElement(self, topOtherSoup):
    """
    寻找符合条件的子节点，即当前节点向下找，符合一直是
        type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736"
        且层数 >= 3，然后再找其下一个 非x=0 y=0的节点
            很可能就是 弹框的主体元素
    """
    popupTopFrameElement = None

    # MaxFullScreenSizeLevel = 3
    # FullScreenSizeMinLevel = 3
    # FullScreenSizeMaxLevel = 6 # see many invalid case is level >= 10, so here restrict to 6, not too much level
    FullScreenSizLevel = 3 # special: 无忧筹 点击 专属老师 后的弹框

    FullScreenAttr = {"type": "XCUIElementTypeOther", "enabled":"true", "visible":"true", "x":"0", "y":"0", "width":"%s" % self.X, "height":"%s" % self.totalY}

    curFullScreenOtherLevel = 0
    curSoup = topOtherSoup
    while True:
        subOtherSoupList = curSoup.find_all(
            "XCUIElementTypeOther",
            attrs=FullScreenAttr,
            recursive=False,
        )

        if not subOtherSoupList:
            break

        # only have one child
        childOtherSoupNum = len(subOtherSoupList)
        if childOtherSoupNum != 1:
            break

        firstOnlyOtherSoup = subOtherSoupList[0]
        if not firstOnlyOtherSoup:
            break

        if not hasattr(firstOnlyOtherSoup, "attrs"):
            break

        curAttrDict = firstOnlyOtherSoup.attrs

        allAttrSame = True
        for eachToCompareKey in FullScreenAttr.keys():
            eachToCompareValue = FullScreenAttr[eachToCompareKey]
            curValue = curAttrDict.get(eachToCompareKey)
            if curValue != eachToCompareValue:
                allAttrSame = False
                break
        
        if not allAttrSame:
            break

        curSoup = firstOnlyOtherSoup

        curFullScreenOtherLevel += 1

    # if curFullScreenOtherLevel >= MaxFullScreenSizeLevel:
    # isPossiblePopup = FullScreenSizeMinLevel <= curFullScreenOtherLevel < FullScreenSizeMaxLevel
    isPossiblePopup = curFullScreenOtherLevel == FullScreenSizLevel
    if isPossiblePopup:
        curChildOtherList = curSoup.find_all(
            "XCUIElementTypeOther",
            attrs={"type": "XCUIElementTypeOther", "enabled":"true", "visible":"true"},
            recursive=False,
        )
        if curChildOtherList:
            curChildOtherNum = len(curChildOtherList)
            if curChildOtherNum == 1:
                popupTopFrameElement = curChildOtherList[0]

    return popupTopFrameElement
```

## iOSProcessPopupPhotoCamera

```python
def iOSProcessPopupPhotoCamera(self, soup):
    """iOS 系统弹框： 拍照或录像 照片图库 浏览 取消"""
    foundAndProcessedPopup = False
    """
        系统弹框 拍照或录像：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="8" y="530" width="398" height="133">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="8" y="530" width="398" height="133">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="8" y="530" width="398" height="133">
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="0" y="0" width="398" height="133">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="398" height="133">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="398" height="133">
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="398" height="133">
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="398" height="133">
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="398" height="133">
                                                <XCUIElementTypeTable type="XCUIElementTypeTable" enabled="true" visible="true" x="0" y="0" width="398" height="133">
                                                    <XCUIElementTypeCell type="XCUIElementTypeCell" enabled="true" visible="true" x="0" y="0" width="398" height="45">
                                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="43" width="398" height="2"/>
                                                        <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="13" y="6" width="1" height="32"/>
                                                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="拍照或录像" name="拍照或录像" label="拍照或录像" enabled="true" visible="true" x="15" y="11" width="87" height="22"/>
                                                        <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="351" y="6" width="32" height="32"/>
                                                    </XCUIElementTypeCell>
                                                    <XCUIElementTypeCell type="XCUIElementTypeCell" enabled="true" visible="true" x="0" y="44" width="398" height="45">
                                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="88" width="398" height="1"/>
                                                        <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="13" y="50" width="1" height="33"/>
                                                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="照片图库" name="照片图库" label="照片图库" enabled="true" visible="true" x="15" y="56" width="70" height="21"/>
                                                        <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="351" y="50" width="32" height="33"/>
                                                    </XCUIElementTypeCell>
                                                    <XCUIElementTypeCell type="XCUIElementTypeCell" enabled="true" visible="true" x="0" y="88" width="398" height="45">
                                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="0" y="132" width="398" height="1"/>
                                                        <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="13" y="94" width="1" height="33"/>
                                                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="浏览" name="浏览" label="浏览" enabled="true" visible="true" x="14" y="100" width="36" height="21"/>
                                                        <XCUIElementTypeImage type="XCUIElementTypeImage" name="UIDocumentPicker-more" enabled="true" visible="false" x="351" y="94" width="32" height="33"/>
                                                    </XCUIElementTypeCell>
                                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="132" width="398" height="1"/>
                                                </XCUIElementTypeTable>
                                            </XCUIElementTypeOther>
                                        </XCUIElementTypeOther>
                                    </XCUIElementTypeOther>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="8" y="671" width="398" height="57">
                <XCUIElementTypeButton type="XCUIElementTypeButton" name="取消" label="取消" enabled="true" visible="true" x="8" y="671" width="398" height="57"/>
            </XCUIElementTypeOther>
    """
    photoCameraChainList = [
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true"}
        },
        {
            "tag": "XCUIElementTypeTable",
            "attrs": {"enabled":"true", "visible":"true", "x":"0", "y":"0"}
        },
        {
            "tag": "XCUIElementTypeStaticText",
            "attrs": {"enabled":"true", "visible":"true", "value":"拍照或录像"}
        },
    ]
    photoCameraSoup = CommonUtils.bsChainFind(soup, photoCameraChainList)
    if photoCameraSoup:
        topParentOtherSoup = None
        # find top most parent
        parentLevelNum = 11
        curParentSoup = photoCameraSoup
        for curLevelIdx in range(parentLevelNum):
            curParentSoup = curParentSoup.parent
            if not curParentSoup:
                break
        if curParentSoup:
            topParentOtherSoup = curParentSoup
        
        if topParentOtherSoup:
            nextSiblingList = topParentOtherSoup.next_siblings
            if nextSiblingList:
                nextOtherSoup = None
                for eachNextSibling in nextSiblingList:
                    if hasattr(eachNextSibling, "attrs"):
                        curType = eachNextSibling.attrs["type"]
                        if curType == "XCUIElementTypeOther":
                            # next sibling's first XCUIElementTypeOther
                            nextOtherSoup = eachNextSibling
                            break

            if nextOtherSoup:
                cancelSoup = nextOtherSoup.find(
                    "XCUIElementTypeButton",
                    attrs={"visible":"true", "enabled":"true", "name": "取消"}
                )
                if cancelSoup:
                    self.clickElementCenterPosition(cancelSoup)
                    foundAndProcessedPopup = True

    return foundAndProcessedPopup
```

## iOSProcessAlertNoteConfrim

```python
def iOSProcessAlertNoteConfrim(self, soup):
    """只要符合XCUIElementTypeAlert 且其中有 确定 按钮，则点击"""
    foundAndProcessedPopup = False
    """
        康爱公社 弹框 提醒 您的身份信息不完整 确定：
            <XCUIElementTypeAlert type="XCUIElementTypeAlert" name="提醒" label="提醒" enabled="true" visible="true" x="72" y="298" width="270" height="141">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="298" width="270" height="141">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="298" width="270" height="141">
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="298" width="270" height="141">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="298" width="270" height="141"/>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="298" width="270" height="141">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="298" width="270" height="141"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="298" width="270" height="141"/>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="298" width="270" height="141">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="298" width="270" height="96">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="298" width="270" height="96">
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="提醒" name="提醒" label="提醒" enabled="true" visible="true" x="88" y="317" width="238" height="21"/>
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="您的身份信息不完整，请检查身份证信息及手机号是否完善" name="您的身份信息不完整，请检查身份证信息及手机号是否完善" label="您的身份信息不完整，请检查身份证信息及手机号是否完善" enabled="true" visible="true" x="88" y="341" width="238" height="33"/>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="1">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="394" width="270" height="1"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="1"/>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="45">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="45">
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="45">
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="394" width="270" height="45">
                                            <XCUIElementTypeButton type="XCUIElementTypeButton" name="确定" label="确定" enabled="true" visible="true" x="72" y="394" width="270" height="45"/>
                                        </XCUIElementTypeOther>
                                    </XCUIElementTypeOther>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeAlert>

        康爱公社 弹框 提示 请登录后再进行操作：
            <XCUIElementTypeAlert type="XCUIElementTypeAlert" name="提示" label="提示" enabled="true" visible="true" x="72" y="306" width="270" height="125">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="306" width="270" height="125">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="306" width="270" height="125">
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="306" width="270" height="125">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="306" width="270" height="125"/>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="306" width="270" height="125">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="306" width="270" height="125"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="306" width="270" height="125"/>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="306" width="270" height="125">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="306" width="270" height="80">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="306" width="270" height="80">
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="提示" name="提示" label="提示" enabled="true" visible="true" x="88" y="325" width="238" height="21"/>
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="请登录后再进行操作" name="请登录后再进行操作" label="请登录后再进行操作" enabled="true" visible="true" x="88" y="349" width="238" height="17"/>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="386" width="270" height="1">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="386" width="270" height="1"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="386" width="270" height="1"/>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="386" width="270" height="45">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="386" width="270" height="45">
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="386" width="270" height="45">
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="386" width="270" height="45">
                                            <XCUIElementTypeButton type="XCUIElementTypeButton" name="确定" label="确定" enabled="true" visible="true" x="72" y="386" width="270" height="45"/>
                                        </XCUIElementTypeOther>
                                    </XCUIElementTypeOther>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeAlert>
    """
    noteTipP = re.compile("((提醒)|(提示))")
    alertConfirmChainList = [
        {
            "tag": "XCUIElementTypeAlert",
            # "attrs": {"enabled":"true", "visible":"true"}
            "attrs": {"enabled":"true", "visible":"true", "name": noteTipP}
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true"}
        },
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"enabled":"true", "visible":"true", "name":"确定"}
        },
    ]
    alertConfirmSoup = CommonUtils.bsChainFind(soup, alertConfirmChainList)
    if alertConfirmSoup:
        self.clickElementCenterPosition(alertConfirmSoup)
        foundAndProcessedPopup = True
    return foundAndProcessedPopup
```

## iOSProcessAlertCancel

```python
def iOSProcessAlertCancel(self, soup):
    """对于常见的XCUIElementTypeAlert，内有 取消，则点击取消"""
    foundAndProcessedPopup = False
    """
        益路通行 弹框 您是否要退出登录：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736"/>
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="0" y="0" width="414" height="736"/>
                <XCUIElementTypeAlert type="XCUIElementTypeAlert" name="您是否要退出登录？" label="您是否要退出登录？" enabled="true" visible="true" x="72" y="316" width="270" height="105">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="316" width="270" height="105">
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="316" width="270" height="105">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="316" width="270" height="105">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="316" width="270" height="105"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="316" width="270" height="105">
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="316" width="270" height="105"/>
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="316" width="270" height="105"/>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="316" width="270" height="105">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="316" width="270" height="60">
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="316" width="270" height="60">
                                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="您是否要退出登录？" name="您是否要退出登录？" label="您是否要退出登录？" enabled="true" visible="true" x="88" y="335" width="238" height="21"/>
                                    </XCUIElementTypeOther>
                                </XCUIElementTypeOther>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="376" width="270" height="1">
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="72" y="376" width="270" height="1"/>
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="376" width="270" height="1"/>
                                </XCUIElementTypeOther>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="376" width="270" height="45">
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="376" width="270" height="45">
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="376" width="270" height="45">
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="72" y="376" width="135" height="45">
                                                <XCUIElementTypeButton type="XCUIElementTypeButton" name="取消" label="取消" enabled="true" visible="true" x="72" y="376" width="135" height="45"/>
                                            </XCUIElementTypeOther>
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="207" y="376" width="1" height="45">
                                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="207" y="376" width="1" height="45"/>
                                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="207" y="376" width="1" height="45"/>
                                            </XCUIElementTypeOther>
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="207" y="376" width="135" height="45">
                                                <XCUIElementTypeButton type="XCUIElementTypeButton" name="退出" label="退出" enabled="true" visible="true" x="207" y="376" width="135" height="45"/>
                                            </XCUIElementTypeOther>
                                        </XCUIElementTypeOther>
                                    </XCUIElementTypeOther>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                    </XCUIElementTypeOther>
                </XCUIElementTypeAlert>
    """
    alertCancelChainList = [
        {
            "tag": "XCUIElementTypeAlert",
            "attrs": {"enabled":"true", "visible":"true"}
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true"}
        },
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"enabled":"true", "visible":"true", "name":"取消"}
        },
    ]
    alertCancelSoup = CommonUtils.bsChainFind(soup, alertCancelChainList)
    if alertCancelSoup:
        self.clickElementCenterPosition(alertCancelSoup)
        foundAndProcessedPopup = True
    return foundAndProcessedPopup
```

## iOSProcessKagsPopupStillNotLogin

```python
def iOSProcessKagsPopupStillNotLogin(self, soup):
    foundAndProcessedPopup = False
    """
        康爱公社 弹框 您还未登录：
            <XCUIElementTypeOther type="XCUIElementTypeOther" name="pages/home1/home1[2]" label="pages/home1/home1[2]" enabled="true" visible="false" x="0" y="0" width="414" height="2707">
            。。。
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="135" y="373" width="144" height="30">
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="您还未登录" name="您还未登录" label="您还未登录" enabled="true" visible="true" x="146" y="373" width="122" height="30"/>
                </XCUIElementTypeOther>
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="135" y="400" width="144" height="44">
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="请先登录后再进行操作" name="请先登录后再进行操作" label="请先登录后再进行操作" enabled="true" visible="true" x="135" y="420" width="144" height="19"/>
                </XCUIElementTypeOther>
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="112" y="473" width="70" height="22">
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="暂不登录" name="暂不登录" label="暂不登录" enabled="true" visible="true" x="112" y="473" width="70" height="22"/>
                </XCUIElementTypeOther>
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="232" y="473" width="70" height="22">
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="立即登录" name="立即登录" label="立即登录" enabled="true" visible="true" x="232" y="473" width="70" height="22"/>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>
    """
    stillNotLoginChainList = [
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "x":"0", "width": "%s" % self.X}
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"visible":"true", "enabled":"true"}
        },
        {
            "tag": "XCUIElementTypeStaticText",
            "attrs": {"visible":"true", "enabled":"true", "value":"您还未登录"}
        },
    ]
    stillNotLoginSoup = CommonUtils.bsChainFind(soup, stillNotLoginChainList)
    if stillNotLoginSoup:
        parentOtherSoup = stillNotLoginSoup.parent
        parentParentOtherSoup = parentOtherSoup.parent
        if parentParentOtherSoup:
            tempNotLoginSoup = parentParentOtherSoup.find(
                "XCUIElementTypeStaticText",
                attrs={"visible":"true", "enabled":"true", "value": "暂不登录"}
            )
            if tempNotLoginSoup:
                self.clickElementCenterPosition(tempNotLoginSoup)
                foundAndProcessedPopup = True
    return foundAndProcessedPopup
```

## iOSProcessWillOpenNonOfficialPage

```python
def iOSProcessWillOpenNonOfficialPage(self, soup):
    foundAndProcessed = False

    """
        将要访问 非有赞官方网页，请确认是否继续访问
        <XCUIElementTypeWebView type="XCUIElementTypeWebView" enabled="true" visible="true" x="0" y="64" width="375" height="603">
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="64" width="375" height="603">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="0" y="0" width="0" height="0">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="0" y="0" width="0" height="0">
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="64" width="375" height="603">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="64" width="375" height="603">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="150" y="234" width="75" height="24">
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="将要访问" name="将要访问" label="将要访问" enabled="true" visible="true" x="150" y="234" width="75" height="23"/>
                                </XCUIElementTypeOther>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="30" y="268" width="315" height="44">
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="http://weixintx.haoduoke.cn/mobile/new_shop?appId=wx63d48daf039fe35c" name="http://weixintx.haoduoke.cn/mobile/new_shop?appId=wx63d48daf039fe35c" label="http://weixintx.haoduoke.cn/mobile/new_shop?appId=wx63d48daf039fe35c" enabled="true" visible="true" x="37" y="270" width="301" height="40"/>
                                </XCUIElementTypeOther>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="58" y="322" width="258" height="22">
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="非有赞官方网页，请确认是否继续访问。" name="非有赞官方网页，请确认是否继续访问。" label="非有赞官方网页，请确认是否继续访问。" enabled="true" visible="true" x="58" y="324" width="259" height="18"/>
                                </XCUIElementTypeOther>
                                <XCUIElementTypeButton type="XCUIElementTypeButton" name="继续访问" label="继续访问" enabled="true" visible="true" x="20" y="384" width="335" height="50"/>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="160" y="632" width="74" height="15">
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="有赞安全中心" name="有赞安全中心" label="有赞安全中心" enabled="true" visible="true" x="160" y="632" width="74" height="15"/>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>
        </XCUIElementTypeWebView>
    """
    willOpenChainList = [
        {
            "tag": "XCUIElementTypeWebView",
            "attrs": {"visible":"true", "enabled":"true", "x":"0", "width": "%s" % self.X}
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"visible":"true", "enabled":"true", "x":"0", "width": "%s" % self.X}
        },
        {
            "tag": "XCUIElementTypeStaticText",
            "attrs": {"visible":"true", "enabled":"true", "value":"将要访问"}
        },
    ]
    willOpenSoup = CommonUtils.bsChainFind(soup, willOpenChainList)
    if willOpenSoup:
        parentOtherSoup = willOpenSoup.parent
        parentParentOtherSoup = parentOtherSoup.parent
        if parentParentOtherSoup:
            continueOpenSoup = parentParentOtherSoup.find(
                "XCUIElementTypeButton",
                attrs={"visible":"true", "enabled":"true", "name": "继续访问"}
            )
            if continueOpenSoup:
                self.clickElementCenterPosition(continueOpenSoup)
                foundAndProcessed = True

    return foundAndProcessed
```

## iOSProcessSettingsAllowUseLocation

```python
def iOSProcessSettingsAllowUseLocation(self, soup):
    """处理 设置中的 使用我的地理位置，并返回前一页
        注：往往是出现 弹框-是否授权当前位置，点击 允许后，跳转到此处设置页面
    """

    """
        设置 使用我的地理位置：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="375" height="667">
                <XCUIElementTypeNavigationBar type="XCUIElementTypeNavigationBar" name="设置" enabled="true" visible="true" x="0" y="20" width="375" height="44">
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="返回" label="返回" enabled="true" visible="true" x="10" y="20" width="30" height="44"/>
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="设置" name="设置" label="设置" enabled="true" visible="true" x="187" y="24" width="1" height="36"/>
                </XCUIElementTypeNavigationBar>
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="375" height="667">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="375" height="667">
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="375" height="667">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="0" y="0" width="375" height="64"/>
                            <XCUIElementTypeTable type="XCUIElementTypeTable" enabled="true" visible="true" x="0" y="0" width="375" height="667">
                                <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="允许&quot;贝德玛会员中心&quot;" name="允许&quot;贝德玛会员中心&quot;" label="允许&quot;贝德玛会员中心&quot;" enabled="true" visible="true" x="15" y="88" width="345" height="17"/>
                                <XCUIElementTypeCell type="XCUIElementTypeCell" value="0" enabled="true" visible="true" x="0" y="115" width="375" height="44">
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="115" width="375" height="1"/>
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="158" width="375" height="1"/>
                                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="使用我的地理位置" name="使用我的地理位置" label="使用我的地理位置" enabled="true" visible="true" x="15" y="126" width="139" height="22"/>
                                    <XCUIElementTypeSwitch type="XCUIElementTypeSwitch" value="0" name="使用我的地理位置" label="使用我的地理位置" enabled="true" visible="true" x="309" y="122" width="51" height="31"/>
                                </XCUIElementTypeCell>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="159" width="375" height="1219"/>
                            </XCUIElementTypeTable>
                        </XCUIElementTypeOther>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>
    """
    foundAndProcessedPopup = False
    useLocationSwitchQuery = {"type": "XCUIElementTypeSwitch", "name": "使用我的地理位置", "enabled":"true"}
    parentCellClassChain = "/XCUIElementTypeCell[`enabled = 1 AND rect.width = %d`]" % self.X
    useLocationSwitchQuery["parent_class_chains"] = [ parentCellClassChain ]
    foundAndClickeUseLocation = self.findAndClickElement(useLocationSwitchQuery, isShowErrLog=False)
    logging.debug("foundAndClickeUseLocation=%s", foundAndClickeUseLocation)
    if foundAndClickeUseLocation:
        parentNaviBarClassChain = '/XCUIElementTypeNavigationBar[`enabled = 1 AND name="设置"`]'
        backButtonQuery = {"type": "XCUIElementTypeButton", "name": "返回", "enabled":"true"}
        backButtonQuery["parent_class_chains"] = parentNaviBarClassChain
        foundAndClickeBack = self.findAndClickElement(backButtonQuery, isShowErrLog=False)
        logging.debug("foundAndClickeBack=%s", foundAndClickeBack)
        foundAndProcessedPopup = foundAndClickeBack

    return foundAndProcessedPopup
```

## iOSProcessPopupNetworkNotStableRetry

```python
def iOSProcessPopupNetworkNotStableRetry(self, soup):
    """Process iOS popup 京东金融 网络不稳定,请点击重试"""
    foundAndProcessed = False
    """
        京东金融 页面 网络不稳定 请点击重试：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="64">
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="com icon black backup u" label="com icon black backup u" enabled="true" visible="true" x="0" y="20" width="44" height="44"/>
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" enabled="true" visible="false" x="65" y="20" width="284" height="44"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="comunity top report@3x" label="comunity top report@3x" enabled="true" visible="true" x="326" y="20" width="44" height="44"/>
                </XCUIElementTypeOther>
                <XCUIElementTypeTable type="XCUIElementTypeTable" enabled="true" visible="true" x="0" y="64" width="414" height="673">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="64" width="414" height="1"/>
                    <XCUIElementTypeCell type="XCUIElementTypeCell" enabled="true" visible="true" x="0" y="64" width="414" height="629">
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="网络不稳定,请点击重试" name="网络不稳定,请点击重试" label="网络不稳定,请点击重试" enabled="true" visible="true" x="16" y="322" width="382" height="21"/>
                        <XCUIElementTypeImage type="XCUIElementTypeImage" name="icon_network-instability" enabled="true" visible="false" x="132" y="152" width="150" height="151"/>
                    </XCUIElementTypeCell>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="692" width="414" height="1"/>
                </XCUIElementTypeTable>
            </XCUIElementTypeOther>
    """
    networkNotStableChainList = [
        {
            "tag": "XCUIElementTypeTable",
            "attrs": {"enabled":"true", "visible":"true", "x":"0", "width":"%s" % self.X}
        },
        {
            "tag": "XCUIElementTypeCell",
            "attrs": {"enabled":"true", "visible":"true", "x":"0", "width":"%s" % self.X}
        },
        {
            "tag": "XCUIElementTypeStaticText",
            "attrs": {"enabled":"true", "visible":"true", "value": "网络不稳定,请点击重试"}
        },
    ]
    networkNotStableSoup = CommonUtils.bsChainFind(soup, networkNotStableChainList)
    if networkNotStableSoup:
        self.clickElementCenterPosition(networkNotStableSoup)
        foundAndProcessed = True
    return foundAndProcessed
```

## iOSProcessPopupNetworkNotStableRefresh

```python
def iOSProcessPopupNetworkNotStableRefresh(self, soup):
    """Process iOS popup 京东金融 页面 网络不稳定 刷新试试"""
    foundAndProcessedPopup = False
    """
        京东金融 页面 网络不稳定 刷新试试：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="64" width="414" height="623">
                <XCUIElementTypeTable type="XCUIElementTypeTable" name="空列表" label="空列表" enabled="true" visible="false" x="0" y="64" width="414" height="623"/>
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="64" width="414" height="623">
                    <XCUIElementTypeImage type="XCUIElementTypeImage" name="com_network_err" enabled="true" visible="false" x="152" y="164" width="110" height="110"/>
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="网络不稳定" name="网络不稳定" label="网络不稳定" enabled="true" visible="true" x="0" y="287" width="414" height="17"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="刷新试试" label="刷新试试" enabled="true" visible="true" x="147" y="323" width="120" height="41"/>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>
    """
    tryRefreshChainList = [
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true", "x":"0", "width":"%s" % self.X}
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"enabled":"true", "visible":"true", "x":"0", "width":"%s" % self.X}
        },
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"enabled":"true", "visible":"true", "name": "刷新试试"}
        },
    ]
    tryRefreshSoup = CommonUtils.bsChainFind(soup, tryRefreshChainList)
    if tryRefreshSoup:
        self.clickElementCenterPosition(tryRefreshSoup)
        foundAndProcessedPopup = True

        # # Special: above click by position not work
        # # so change to wda query element then click
        # tryRefreshButtonQuery = {"type":"XCUIElementTypeButton", "enabled":"true", "name": "刷新试试"}
        # foundAndClicked = findAndClickElement(curSession, tryRefreshButtonQuery)
        # foundAndProcessedPopup = foundAndClicked

    return foundAndProcessedPopup
```

## iOSProcessPopupCertificateError

```python
def iOSProcessPopupCertificateError(self, soup):
    """Process iOS popup 安全证书存在问题 网络出错，轻触屏幕重新加载"""
    foundAndProcessed = False

    """
        <XCUIElementTypeButton type="XCUIElementTypeButton" name="网络出错，轻触屏幕重新加载:-1202" label="网络出错，轻触屏幕重新加载:-1202" enabled="true" visible="true" x="0" y="64" width="414" height="672">
            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="网络出错，轻触屏幕重新加载:-1202" name="网络出错，轻触屏幕重新加载:-1202" label="网络出错，轻触屏幕重新加载:-1202" enabled="true" visible="true" x="0" y="264" width="414" height="40"/>
        </XCUIElementTypeButton>
    """
    netErrTouchReloadP = re.compile("网络出错，轻触屏幕重新加载")
    foundTouchReload = soup.find(
        "XCUIElementTypeButton",
        attrs={"visible":"true", "name": netErrTouchReloadP, "enabled":"true"}
    )
    if foundTouchReload:
        self.clickElementCenterPosition(foundTouchReload)
        foundAndProcessed = True

    if not foundAndProcessed:
        """
            <XCUIElementTypeButton type="XCUIElementTypeButton" name="网络出错，轻触屏幕重新加载:-1202" label="网络出错，轻触屏幕重新加载:-1202" enabled="true" visible="false" x="0" y="64" width="414" height="672">
                <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="网络出错，轻触屏幕重新加载:-1202" name="网络出错，轻触屏幕重新加载:-1202" label="网络出错，轻触屏幕重新加载:-1202" enabled="true" visible="false" x="0" y="264" width="414" height="40"/>
            </XCUIElementTypeButton>

            <XCUIElementTypeWindow type="XCUIElementTypeWindow" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                    <XCUIElementTypeButton type="XCUIElementTypeButton" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="安全警告" name="安全警告" label="安全警告" enabled="true" visible="true" x="71" y="286" width="272" height="19"/>
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="mp.weixin.qq.com 该网站的安全证书存在问题，可选择“继续”在浏览器中访问。" name="mp.weixin.qq.com 该网站的安全证书存在问题，可选择“继续”在浏览器中访问。" label="mp.weixin.qq.com 该网站的安全证书存在问题，可选择“继续”在浏览器中访问。" enabled="true" visible="true" x="71" y="320" width="272" height="74"/>
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="取消" name="取消" label="取消" enabled="true" visible="true" x="109" y="443" width="36" height="22"/>
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="继续" name="继续" label="继续" enabled="true" visible="true" x="269" y="443" width="36" height="22"/>
                    </XCUIElementTypeButton>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeWindow>
        """
        securityWarningChainList = self.generateCommonPopupItemChainList(thirdLevelValue="安全警告")
        securityWarningSoup = CommonUtils.bsChainFind(soup, securityWarningChainList)
        if securityWarningSoup:
            parentButtonSoup = securityWarningSoup.parent
            if parentButtonSoup:
                certificateProblemP = re.compile("该网站的安全证书存在问题")
                foundCertProblem = parentButtonSoup.find(
                    "XCUIElementTypeStaticText",
                    attrs={"visible":"true", "enabled":"true", "value":certificateProblemP}
                )
                if foundCertProblem:
                    foundCancel = parentButtonSoup.find(
                        "XCUIElementTypeStaticText",
                        attrs={"visible":"true", "enabled":"true", "value":"取消"}
                    )
                    if foundCancel:
                        self.clickElementCenterPosition(foundCancel)
                        foundAndProcessed = True

    return foundAndProcessed
```

## iOSProcessPopupWeixinLogin

```python
def iOSProcessPopupWeixinLogin(self, soup):
    """Process iOS popup 微信登录"""
    foundAndProcessedPopup = False

    """
        <XCUIElementTypeWindow type="XCUIElementTypeWindow" enabled="true" visible="true" x="0" y="0" width="414" height="736">
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="52" y="236" width="310" height="263">
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="微信登录" name="微信登录" label="微信登录" enabled="true" visible="true" x="83" y="251" width="248" height="18"/>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="52" y="285" width="310" height="1"/>
                    <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="189" y="306" width="36" height="35"/>
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="微店消息助手申请获得以下权限:" name="微店消息助手申请获得以下权限:" label="微店消息助手申请获得以下权限:" enabled="true" visible="true" x="82" y="356" width="231" height="19"/>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="82" y="390" width="250" height="1"/>
                    <XCUIElementTypeTable type="XCUIElementTypeTable" enabled="true" visible="true" x="82" y="391" width="250" height="62">
                        <XCUIElementTypeCell type="XCUIElementTypeCell" enabled="true" visible="true" x="82" y="391" width="250" height="47">
                        <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="82" y="406" width="15" height="14"/>
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="获得你的公开信息（昵称、头像、地区及性别）" name="获得你的公开信息（昵称、头像、地区及性别）" label="获得你的公开信息（昵称、头像、地区及性别）" enabled="true" visible="true" x="101" y="405" width="230" height="34"/>
                        </XCUIElementTypeCell>
                    </XCUIElementTypeTable>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="52" y="454" width="310" height="1"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="拒绝" label="拒绝" enabled="true" visible="true" x="52" y="454" width="155" height="45">
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="拒绝" name="拒绝" label="拒绝" enabled="true" visible="true" x="112" y="466" width="35" height="21"/>
                    </XCUIElementTypeButton>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="207" y="454" width="1" height="45"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="允许" label="允许" enabled="true" visible="true" x="207" y="454" width="155" height="45">
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="允许" name="允许" label="允许" enabled="true" visible="true" x="267" y="466" width="35" height="21"/>
                    </XCUIElementTypeButton>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>
            </XCUIElementTypeOther>
        </XCUIElementTypeWindow>
    """
    weixinLoginChainList = self.generateCommonPopupItemChainList(
        secondLevelTag="XCUIElementTypeOther",
        thirdLevelValue="微信登录"
    )
    foundWeixinLogin = CommonUtils.bsChainFind(soup, weixinLoginChainList)

    tableChainList = self.generateCommonPopupItemChainList(
        secondLevelTag="XCUIElementTypeOther",
        thirdLevelTag="XCUIElementTypeTable",
    )
    foundTable = CommonUtils.bsChainFind(soup, tableChainList)

    if foundWeixinLogin and foundTable:
        parentOtherSoup = foundWeixinLogin.parent
        if parentOtherSoup:
            foundAllowButton = parentOtherSoup.find(
                "XCUIElementTypeButton",
                attrs={"visible":"true", "enabled":"true", "label":"允许"}
            )
            if foundAllowButton:
                self.clickElementCenterPosition(foundAllowButton)
                foundAndProcessedPopup = True

    return foundAndProcessedPopup
```

## iOSProcessPopupJumpThirdApp

```python
def iOSProcessPopupJumpThirdApp(self, soup):
    """Process iOS popup 可能离开微信，打开第三方应用"""
    foundAndProcessedPopup = False

    """
        <XCUIElementTypeWindow type="XCUIElementTypeWindow" enabled="true" visible="true" x="0" y="0" width="414" height="736">
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                        <XCUIElementTypeButton type="XCUIElementTypeButton" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" enabled="true" visible="false" x="47" y="288" width="0" height="0"/>
                            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="可能离开微信，打开第三方应用" name="可能离开微信，打开第三方应用" label="可能离开微信，打开第三方应用" enabled="true" visible="true" x="71" y="331" width="272" height="18"/>
                            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="取消" name="取消" label="取消" enabled="true" visible="true" x="109" y="409" width="36" height="22"/>
                            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="继续" name="继续" label="继续" enabled="true" visible="true" x="269" y="409" width="36" height="22"/>
                        </XCUIElementTypeButton>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>
        </XCUIElementTypeWindow>
    """
    leaveWeixinChainList = self.generateCommonPopupItemChainList(thirdLevelValue="可能离开微信，打开第三方应用")
    foundLeaveWeixin = CommonUtils.bsChainFind(soup, leaveWeixinChainList)
    if foundLeaveWeixin:
        parentButtonSoup = foundLeaveWeixin.parent
        if parentButtonSoup:
            foundCancel = parentButtonSoup.find(
                "XCUIElementTypeStaticText",
                attrs={"visible":"true", "enabled":"true", "value":"取消"}
            )
            if foundCancel:
                self.clickElementCenterPosition(foundCancel)
                foundAndProcessedPopup = True

    return foundAndProcessedPopup
```

## iOSProcessPopupDoSomeFail

```python
# def iOSProcessPopupGetInfoFail(self, soup):
def iOSProcessPopupDoSomeFail(self, soup):
    """Process iOS popup 获取信息失败 微信登录失败 等"""
    foundAndProcessedPopup = False
    """
        微信：
            获取信息失败：
            <XCUIElementTypeWindow type="XCUIElementTypeWindow" enabled="true" visible="true" x="0" y="0" width="414" height="736">
            。。。
                <XCUIElementTypeButton type="XCUIElementTypeButton" enabled="true" visible="true" x="0" y="0" width="414" height="736">
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="获取信息失败" name="获取信息失败" label="获取信息失败" enabled="true" visible="true" x="71" y="309" width="272" height="21"/>
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="取消" name="取消" label="取消" enabled="true" visible="true" x="109" y="419" width="36" height="22"/>
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="确定" name="确定" label="确定" enabled="true" visible="true" x="269" y="419" width="36" height="22"/>
                </XCUIElementTypeButton>
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="320" y="24" width="87" height="32">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="320" y="24" width="88" height="32">
                        <XCUIElementTypeButton type="XCUIElementTypeButton" name="更多" label="更多" enabled="true" visible="true" x="320" y="24" width="44" height="32"/>
                        <XCUIElementTypeButton type="XCUIElementTypeButton" name="关闭" label="关闭" enabled="true" visible="true" x="363" y="24" width="45" height="32"/>
                    </XCUIElementTypeOther>
                </XCUIElementTypeOther>
            。。。
            </XCUIElementTypeWindow>

            微信登录失败：
            <XCUIElementTypeWindow type="XCUIElementTypeWindow" enabled="true" visible="true" x="0" y="0" width="375" height="667">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="375" height="667">
                    <XCUIElementTypeButton type="XCUIElementTypeButton" enabled="true" visible="true" x="0" y="0" width="375" height="667">
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="32" y="247" width="311" height="173">
                            <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="32" y="247" width="311" height="173"/>
                            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="微信登录失败" name="微信登录失败" label="微信登录失败" enabled="true" visible="true" x="52" y="272" width="271" height="22"/>
                            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="此公众号并没有这些scope的权限，错误码:10005" name="此公众号并没有这些scope的权限，错误码:10005" label="此公众号并没有这些scope的权限，错误码:10005" enabled="true" visible="true" x="59" y="309" width="257" height="40"/>
                            <XCUIElementTypeButton type="XCUIElementTypeButton" name="确定" label="确定" enabled="true" visible="true" x="32" y="370" width="311" height="50"/>
                        </XCUIElementTypeOther>
                    </XCUIElementTypeButton>
                </XCUIElementTypeOther>
            </XCUIElementTypeWindow>

        小程序：
            获取信息失败：
            <XCUIElementTypeButton type="XCUIElementTypeButton" enabled="true" visible="true" x="0" y="0" width="375" height="667">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="32" y="251" width="311" height="165">
                    <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="32" y="251" width="311" height="165"/>
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="获取信息失败" name="获取信息失败" label="获取信息失败" enabled="true" visible="true" x="52" y="276" width="271" height="21"/>
                    <XCUIElementTypeTextView type="XCUIElementTypeTextView" value="是否重新加载页面" enabled="true" visible="true" x="121" y="311" width="133" height="35"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="取消" label="取消" enabled="true" visible="true" x="32" y="365" width="156" height="51"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="确定" label="确定" enabled="true" visible="true" x="187" y="365" width="156" height="51"/>
                </XCUIElementTypeOther>
            </XCUIElementTypeButton>
    """
    # getInfoFailChainList = self.generateCommonPopupItemChainList(thirdLevelValue="获取信息失败")
    # getInfoFailSoup = CommonUtils.bsChainFind(soup, getInfoFailChainList)
    # if getInfoFailSoup:
    #     parentButtonOrOtherSoup = getInfoFailSoup.parent
    doSomeFailP = re.compile("\S+失败$") # 获取信息失败, 微信登录失败
    doSomeFailChainList = self.generateCommonPopupItemChainList(thirdLevelValue=doSomeFailP)
    doSomeFailSoup = CommonUtils.bsChainFind(soup, doSomeFailChainList)
    if doSomeFailSoup:
        parentButtonOrOtherSoup = doSomeFailSoup.parent
        if parentButtonOrOtherSoup:
            # 微信 弹框：获取信息失败
            foundConfirm = parentButtonOrOtherSoup.find(
                "XCUIElementTypeStaticText",
                attrs={"visible":"true", "enabled":"true", "value": "确定"}
            )

            if not foundConfirm:
                # 小程序 弹框：获取信息失败
                # 微信 弹框：微信登录失败
                foundConfirm = parentButtonOrOtherSoup.find(
                    "XCUIElementTypeButton",
                    attrs={"visible":"true", "enabled":"true", "name": "确定"}
                )

            if foundConfirm:
                self.clickElementCenterPosition(foundConfirm)
                foundAndProcessedPopup = True

    return foundAndProcessedPopup
```

## iOSProcessPopupWeixinAuthorizeLocation

```python
def iOSProcessPopupWeixinAuthorizeLocation(self, soup):
    """Process iOS popup 是否授权当前位置"""
    foundAndProcessedPopup = False
    """
        微信 弹框 是否授权当前位置：
            <XCUIElementTypeButton type="XCUIElementTypeButton" enabled="true" visible="true" x="0" y="0" width="375" height="667">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="32" y="240" width="311" height="187">
                    <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="32" y="240" width="311" height="187"/>
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="是否授权当前位置" name="是否授权当前位置" label="是否授权当前位置" enabled="true" visible="true" x="52" y="265" width="271" height="21"/>
                    <XCUIElementTypeTextView type="XCUIElementTypeTextView" value="需要获取您的地理位置，请确认授权，否则无法获取专柜导航" enabled="true" visible="true" x="67" y="300" width="241" height="57"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="取消" label="取消" enabled="true" visible="true" x="32" y="376" width="156" height="51"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="确定" label="确定" enabled="true" visible="true" x="187" y="376" width="156" height="51"/>
                </XCUIElementTypeOther>
            </XCUIElementTypeButton>
    """
    authorizeLocationChainList = [
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"visible":"true", "enabled":"true", "width": "%s" % self.X, "height": "%s" % self.totalY}
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"visible":"true", "enabled":"true"}
        },
        {
            "tag": "XCUIElementTypeStaticText",
            "attrs": {"visible":"true", "enabled":"true", "value":"是否授权当前位置"}
        },
    ]
    authorizeLocationSoup = CommonUtils.bsChainFind(soup, authorizeLocationChainList)
    if authorizeLocationSoup:
        parentOtherSoup = authorizeLocationSoup.parent
        confirmSoup = parentOtherSoup.find(
            "XCUIElementTypeButton",
            attrs={"visible":"true", "enabled":"true", "name": "确定"}
        )
        if confirmSoup:
            self.clickElementCenterPosition(confirmSoup)
            foundAndProcessedPopup = True

    return foundAndProcessedPopup
```

## iOSProcessPopupMiniprogamWarning

```python
def iOSProcessPopupMiniprogamWarning(self, soup):
    """
        微信-小程序 弹框 警告 尚未进行授权，请点击确定跳转到授权页面进行授权
    """
    foundAndProcessedPopup = False
    """
        微信-小程序 弹框 警告 尚未进行授权：
            <XCUIElementTypeButton type="XCUIElementTypeButton" enabled="true" visible="true" x="0" y="0" width="375" height="667">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="32" y="240" width="311" height="187">
                    <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="32" y="240" width="311" height="187"/>
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="警告" name="警告" label="警告" enabled="true" visible="true" x="52" y="265" width="271" height="21"/>
                    <XCUIElementTypeTextView type="XCUIElementTypeTextView" value="尚未进行授权，请点击确定跳转到授权页面进行授权。" enabled="true" visible="true" x="60" y="300" width="255" height="57"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="取消" label="取消" enabled="true" visible="true" x="32" y="376" width="156" height="51"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="确定" label="确定" enabled="true" visible="true" x="187" y="376" width="156" height="51"/>
                </XCUIElementTypeOther>
            </XCUIElementTypeButton>
    """
    warningChainList = [
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"visible":"true", "enabled":"true", "width": "%s" % self.X, "height": "%s" % self.totalY}
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"visible":"true", "enabled":"true"}
        },
        {
            "tag": "XCUIElementTypeStaticText",
            "attrs": {"visible":"true", "enabled":"true", "value":"警告"}
        },
    ]
    warningSoup = CommonUtils.bsChainFind(soup, warningChainList)
    if warningSoup:
        parentOtherSoup = warningSoup.parent
        confirmSoup = parentOtherSoup.find(
            "XCUIElementTypeButton",
            attrs={"visible":"true", "enabled":"true", "name": "确定"}
        )
        if confirmSoup:
            self.clickElementCenterPosition(confirmSoup)
            foundAndProcessedPopup = True

    return foundAndProcessedPopup
```

## iOSProcessPopupMiniprogamGetYour

```python
# def iOSProcessPopupMiniprogamAuthority(self, soup):
def iOSProcessPopupMiniprogamGetYour(self, soup):
    """Process iOS popup 获取你的昵称、头像、地区及性别 / 获取你的通讯地址 / 需要获取你的地理位置"""
    foundAndProcessedPopup = False
    """
        弹框 获取你的昵称、头像、地区及性别：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="375" height="667">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="375" height="667"/>
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="315" width="375" height="352">
                    <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="20" y="335" width="24" height="25"/>
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="优理氏UNES官方旗舰店" name="优理氏UNES官方旗舰店" label="优理氏UNES官方旗舰店" enabled="true" visible="true" x="49" y="338" width="163" height="19"/>
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="申请" name="申请" label="申请" enabled="true" visible="true" x="217" y="338" width="31" height="19"/>
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="获取你的昵称、头像、地区及性别" name="获取你的昵称、头像、地区及性别" label="获取你的昵称、头像、地区及性别" enabled="true" visible="true" x="20" y="383" width="260" height="25"/>
                    <XCUIElementTypeTable type="XCUIElementTypeTable" enabled="true" visible="true" x="20" y="423" width="335" height="65">
                        <XCUIElementTypeCell type="XCUIElementTypeCell" enabled="true" visible="true" x="20" y="423" width="335" height="65">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="20" y="423" width="335" height="65"/>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="20" y="423" width="335" height="1"/>
                            <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="20" y="435" width="40" height="41"/>
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="20" y="486" width="335" height="1"/>
                            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="斐波测试2" name="斐波测试2" label="斐波测试2" enabled="true" visible="false" x="72" y="435" width="291" height="22"/>
                            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="微信个人信息" name="微信个人信息" label="微信个人信息" enabled="true" visible="false" x="72" y="460" width="291" height="18"/>
                            <XCUIElementTypeImage type="XCUIElementTypeImage" name="icon_selected" enabled="true" visible="false" x="335" y="445" width="20" height="21"/>
                        </XCUIElementTypeCell>
                    </XCUIElementTypeTable>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="使用其他头像和昵称" label="使用其他头像和昵称" enabled="true" visible="true" x="20" y="503" width="138" height="18"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="取消" label="取消" enabled="true" visible="true" x="59" y="561" width="121" height="40"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="允许" label="允许" enabled="true" visible="true" x="195" y="561" width="121" height="40"/>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>

        弹框 获取你的通讯地址：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="375" height="667">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="375" height="667"/>
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="429" width="375" height="238">
                    <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="20" y="449" width="24" height="25"/>
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="优理氏UNES官方旗舰店" name="优理氏UNES官方旗舰店" label="优理氏UNES官方旗舰店" enabled="true" visible="true" x="49" y="452" width="163" height="19"/>
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="申请" name="申请" label="申请" enabled="true" visible="true" x="217" y="452" width="31" height="19"/>
                    <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="获取你的通讯地址" name="获取你的通讯地址" label="获取你的通讯地址" enabled="true" visible="true" x="20" y="497" width="139" height="25"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="拒绝" label="拒绝" enabled="true" visible="true" x="59" y="561" width="121" height="40"/>
                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="允许" label="允许" enabled="true" visible="true" x="195" y="561" width="121" height="40"/>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>

        小程序 弹框 需要获取你的地理位置：
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="375" height="667">
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="0" y="0" width="375" height="667"/>
                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="375" height="667">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="375" height="667"/>
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="0" y="0" width="375" height="667"/>
                    <XCUIElementTypeAlert type="XCUIElementTypeAlert" name="&quot;贝德玛会员中心&quot; 需要获取你的地理位置" label="&quot;贝德玛会员中心&quot; 需要获取你的地理位置" enabled="true" visible="true" x="52" y="270" width="271" height="127">
                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="52" y="270" width="271" height="127">
                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="52" y="270" width="271" height="127">
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="52" y="270" width="271" height="127">
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="52" y="270" width="271" height="127"/>
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="52" y="270" width="271" height="127">
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="52" y="270" width="271" height="127"/>
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="52" y="270" width="271" height="127"/>
                                    </XCUIElementTypeOther>
                                </XCUIElementTypeOther>
                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="52" y="270" width="271" height="127">
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="52" y="270" width="271" height="83">
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="52" y="270" width="271" height="83">
                                            <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="&quot;贝德玛会员中心&quot; 需要获取你的地理位置" name="&quot;贝德玛会员中心&quot; 需要获取你的地理位置" label="&quot;贝德玛会员中心&quot; 需要获取你的地理位置" enabled="true" visible="true" x="68" y="290" width="239" height="43"/>
                                        </XCUIElementTypeOther>
                                    </XCUIElementTypeOther>
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="52" y="352" width="271" height="1">
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="52" y="352" width="271" height="1"/>
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="52" y="352" width="271" height="1"/>
                                    </XCUIElementTypeOther>
                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="52" y="353" width="271" height="44">
                                        <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="52" y="353" width="271" height="44">
                                            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="52" y="353" width="271" height="44">
                                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="52" y="353" width="136" height="44">
                                                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="不允许" label="不允许" enabled="true" visible="true" x="52" y="353" width="136" height="44"/>
                                                </XCUIElementTypeOther>
                                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="187" y="353" width="1" height="44">
                                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="false" x="187" y="353" width="1" height="44"/>
                                                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="187" y="353" width="1" height="44"/>
                                                </XCUIElementTypeOther>
                                                <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="188" y="353" width="135" height="44">
                                                    <XCUIElementTypeButton type="XCUIElementTypeButton" name="允许" label="允许" enabled="true" visible="true" x="188" y="353" width="135" height="44"/>
                                                </XCUIElementTypeOther>
                                            </XCUIElementTypeOther>
                                        </XCUIElementTypeOther>
                                    </XCUIElementTypeOther>
                                </XCUIElementTypeOther>
                            </XCUIElementTypeOther>
                        </XCUIElementTypeOther>
                    </XCUIElementTypeAlert>
                </XCUIElementTypeOther>
            </XCUIElementTypeOther>
    """
    # getYourP = re.compile("获取你的\S+") # 获取你的通讯地址， 获取你的昵称、头像、地区及性别
    getYourP = re.compile("(需要)?获取你的\S+") # 获取你的通讯地址， 获取你的昵称、头像、地区及性别，需要获取你的地理位置
    getYourChainList = [
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"visible":"true", "enabled":"true", "x":"0", "width": self.X}
        },
        {
            "tag": "XCUIElementTypeOther",
            "attrs": {"visible":"true", "enabled":"true", "x":"0", "width": self.X}
        },
        {
            "tag": "XCUIElementTypeStaticText",
            "attrs": {"visible":"true", "enabled":"true", "value": getYourP}
        },
    ]
    getYourSoup = CommonUtils.bsChainFind(soup, getYourChainList)
    if getYourSoup:
        parentOther = getYourSoup.parent
        parentParentOther = None
        parentParentParentOther = None

        allowTag = "XCUIElementTypeButton"
        allowAttrs = {"visible":"true", "enabled":"true", "name": "允许"}

        # 获取你的通讯地址, 获取你的昵称、头像、地区及性别
        foundAllow = parentOther.find(allowTag, attrs=allowAttrs)

        if not foundAllow:
            # for support future possible case
            parentParentOther = parentOther.parent
            foundAllow = parentParentOther.find(allowTag, attrs=allowAttrs)

        if not foundAllow:
            # "贝德玛会员中心" 需要获取你的地理位置
            parentParentParentOther = parentParentOther.parent
            foundAllow = parentParentParentOther.find(allowTag, attrs=allowAttrs)

        if foundAllow:
            self.clickElementCenterPosition(foundAllow)
            foundAndProcessedPopup = True

    return foundAndProcessedPopup
```

## iOSProcessPopupMiniprogamDisclaimer

```python
def iOSProcessPopupMiniprogamDisclaimer(self, soup):
    """Process iOS popup 免责声明"""
    foundAndProcessedPopup = False
    """
        <XCUIElementTypeWindow type="XCUIElementTypeWindow" enabled="true" visible="true" x="0" y="0" width="375" height="667">
            <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="0" y="0" width="375" height="667">
                <XCUIElementTypeButton type="XCUIElementTypeButton" enabled="true" visible="true" x="0" y="0" width="375" height="667">
                    <XCUIElementTypeOther type="XCUIElementTypeOther" enabled="true" visible="true" x="27" y="208" width="321" height="251">
                        <XCUIElementTypeImage type="XCUIElementTypeImage" enabled="true" visible="false" x="27" y="208" width="321" height="251"/>
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="免责声明" name="免责声明" label="免责声明" enabled="true" visible="true" x="51" y="240" width="273" height="21"/>
                        <XCUIElementTypeStaticText type="XCUIElementTypeStaticText" value="本服务由第三方提供，获取你的微信收货地址信息。相关服务和责任由该第三方承担，如有问题请咨询该公司客服。" name="本服务由第三方提供，获取你的微信收货地址信息。相关服务和责任由该第三方承担，如有问题请咨询该公司客服。" label="本服务由第三方提供，获取你的微信收货地址信息。相关服务和责任由该第三方承担，如有问题请咨询该公司客服。" enabled="true" visible="true" x="51" y="277" width="273" height="94"/>
                        <XCUIElementTypeButton type="XCUIElementTypeButton" name="知道了" label="知道了" enabled="true" visible="true" x="27" y="403" width="321" height="56"/>
                    </XCUIElementTypeOther>
                </XCUIElementTypeButton>
            </XCUIElementTypeOther>
        </XCUIElementTypeWindow>
    """
    disclaimerChainList = [
        {
            "tag": "XCUIElementTypeWindow",
            "attrs": {"visible":"true", "enabled":"true", "width": self.X, "height": self.totalY}
        },
        {
            "tag": "XCUIElementTypeButton",
            "attrs": {"visible":"true", "enabled":"true", "width": self.X, "height": self.totalY}
        },
        {
            "tag": "XCUIElementTypeStaticText",
            "attrs": {"visible":"true", "enabled":"true", "value": "免责声明"}
        },
    ]
    disclaimerSoup = CommonUtils.bsChainFind(soup, disclaimerChainList)
    if disclaimerSoup:
        parentOtherSoup = disclaimerSoup.parent
        if parentOtherSoup:
            foundIKnow = parentOtherSoup.find(
                "XCUIElementTypeButton",
                attrs={"visible":"true", "enabled":"true", "name":"知道了"}
            )
            if foundIKnow:
                self.clickElementCenterPosition(foundIKnow)
                foundAndProcessedPopup = True

    return foundAndProcessedPopup
```

期间部分相关函数：

```python
def generateCommonPopupItemChainList(self,
        firstLevelTag="XCUIElementTypeWindow",
        secondLevelTag="XCUIElementTypeButton",
        thirdLevelTag="XCUIElementTypeStaticText",
        thirdLevelValue=None,
        thirdLevelName=None,
    ):
    CommonAttrs_VisibleEnabledFullWidthFullHeight = {"visible":"true", "enabled":"true", "width": "%s" % self.X, "height": "%s" % self.totalY}
    commonItemChainList = [
        {
            "tag": firstLevelTag,
            "attrs": CommonAttrs_VisibleEnabledFullWidthFullHeight
        },
        {
            "tag": secondLevelTag,
            "attrs": CommonAttrs_VisibleEnabledFullWidthFullHeight
        },
    ]
    thirdItemAttrs = {"visible":"true", "enabled":"true"}
    if thirdLevelValue:
        thirdItemAttrs["value"] = thirdLevelValue
    if thirdLevelName:
        thirdItemAttrs["name"] = thirdLevelName
    thirdItemDict =  {
        "tag": thirdLevelTag,
        "attrs": thirdItemAttrs
    }
    commonItemChainList.append(thirdItemDict)
    return commonItemChainList
```
