---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 4ed4f7e15a21e1565031994bd377c15aebd535bc
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46466144"
---
## <a name="setting-up-your-ios-application"></a>Nastavení aplikace iOS

Tato část obsahuje podrobné pokyny pro vytvoření nového projektu k předvedení jak integrovat aplikace pro iOS (Swift) s *ve službě Microsoft* tak ji můžete dotazovat webové rozhraní API, která vyžaduje token.

> Stáhněte si tento ukázkový projekt XCode místo toho raději? [Stáhnete projekt](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip) a pokračujte [potřeba provádět krok konfigurace](#register-your-application) konfigurace ukázkového kódu před spuštěním.


## <a name="install-carthage-to-download-and-build-msal"></a>Nainstalujte Carthage ke stažení a sestavení MSAL
Správce balíčků Carthage se používá během období preview MSAL – integruje se s XCode a přitom možnost Microsoft provádět změny do knihovny.

- Stáhněte a nainstalujte nejnovější verzi Carthage [tady](https://github.com/Carthage/Carthage/releases "Carthage adresu URL pro stažení")

## <a name="creating-your-application"></a>Vytvoření vaší aplikace

1.  Xcode otevřete a vyberte `Create a new Xcode project`
2.  Vyberte `iOS`  >  `Single view Application` a klikněte na tlačítko *další*
3.  Zadejte název produktu a klikněte na *další*
4.  Vyberte složku pro vytvoření aplikace a klikněte na tlačítko *Create*

## <a name="build-the-msal-framework"></a>Sestavení rozhraní MSAL

Postupujte podle pokynů k vyžádání a začnete vytvářet nejnovější verzi knihovny MSAL díky Carthage:

1.  Otevřete prostředí bash terminál a přejděte do kořenové složky aplikace
2.  Kopírování níže a vložte do terminálu bash pro vytvoření souboru "Cartfile":

```bash
echo "github \"AzureAD/microsoft-authentication-library-for-objc\" \"master\"" > Cartfile
```
<!-- Workaround for Docs conversion bug -->
<ol start="3">
<li>
Zkopírujte a vložte níže. Tento příkaz načte závislosti do složky Carthage/rezervace, pak vytvoří knihovna MSAL:
</li>
</ol>

```bash
carthage update
```

> Výše uvedený postup slouží k stáhněte si a sestavte Microsoft Authentication Library (MSAL). Knihovna MSAL zpracovává načítání, ukládání do mezipaměti a aktualizaci tokeny uživatele pro přístup k rozhraní API chráněné službou Azure Active Directory v2.

## <a name="add-the-msal-framework-to-your-application"></a>Přidání rozhraní MSAL do aplikace
1.  V Xcode otevřete `General` kartu
2.  Přejděte `Linked Frameworks and Libraries` části a klikněte na tlačítko `+`
3.  Vyberte `Add other…`
4.  Vyberte: `Carthage`  >  `Build`  >  `iOS`  >  `MSAL.framework` a klikněte na tlačítko *otevřít*. Měli byste vidět `MSAL.framework` přidán do seznamu.
5.  Přejděte na `Build Phases` kartu a klikněte na tlačítko `+` ikonu, zvolte `New Run Script Phase`
6.  Přidejte následující obsah *skriptu oblasti*:

```text
/usr/local/bin/carthage copy-frameworks
```

<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
Přidejte následující text do <code>Input Files</code> kliknutím <code>+</code>:
</li>
</ol>

```text
$(SRCROOT)/Carthage/Build/iOS/MSAL.framework
```

## <a name="creating-your-applications-ui"></a>Vytvoření uživatelského rozhraní aplikace
Jako součást šablony projektu by měl automaticky vytvoří soubor Main.storyboard. Postupujte podle níže uvedených pokynů k vytvoření aplikace uživatelského rozhraní:

1.  CTRL + klikněte na tlačítko `Main.storyboard` vyvolali místní nabídku, a potom klikněte na: `Open As` > `Source Code`
2.  Nahradit `<scenes>` uzel s následujícím kódem:

```xml
 <scenes>
    <scene sceneID="tne-QT-ifu">
        <objects>
            <viewController id="BYZ-38-t0r" customClass="ViewController" customModule="MSALiOS" customModuleProvider="target" sceneMemberID="viewController">
                <layoutGuides>
                    <viewControllerLayoutGuide type="top" id="y3c-jy-aDJ"/>
                    <viewControllerLayoutGuide type="bottom" id="wfy-db-euE"/>
                </layoutGuides>
                <view key="view" contentMode="scaleToFill" id="8bC-Xf-vdC">
                    <rect key="frame" x="0.0" y="0.0" width="375" height="667"/>
                    <autoresizingMask key="autoresizingMask" widthSizable="YES" heightSizable="YES"/>
                    <subviews>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Microsoft Authentication Library" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="ifd-fu-zjm">
                            <rect key="frame" x="64" y="28" width="246" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <label opaque="NO" userInteractionEnabled="NO" contentMode="left" horizontalHuggingPriority="251" verticalHuggingPriority="251" fixedFrame="YES" text="Logging" textAlignment="natural" lineBreakMode="tailTruncation" baselineAdjustment="alignBaselines" adjustsFontSizeToFit="NO" translatesAutoresizingMaskIntoConstraints="NO" id="98g-dc-BPL">
                            <rect key="frame" x="16" y="277" width="62" height="21"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <fontDescription key="fontDescription" type="system" pointSize="17"/>
                            <nil key="textColor"/>
                            <nil key="highlightedColor"/>
                        </label>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="2rX-Vv-T1i">
                            <rect key="frame" x="87" y="100" width="200" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Call Microsoft Graph API"/>
                            <connections>
                                <action selector="callGraphButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="Kx0-JL-Bv9"/>
                            </connections>
                        </button>
                        <textView clipsSubviews="YES" multipleTouchEnabled="YES" contentMode="scaleToFill" fixedFrame="YES" editable="NO" textAlignment="natural" selectable="NO" translatesAutoresizingMaskIntoConstraints="NO" id="qXW-2z-J7K">
                            <rect key="frame" x="16" y="324" width="343" height="291"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <color key="backgroundColor" white="1" alpha="1" colorSpace="calibratedWhite"/>
                            <accessibility key="accessibilityConfiguration">
                                <accessibilityTraits key="traits" updatesFrequently="YES"/>
                            </accessibility>
                            <fontDescription key="fontDescription" type="system" pointSize="14"/>
                            <textInputTraits key="textInputTraits" autocapitalizationType="sentences"/>
                        </textView>
                        <button opaque="NO" contentMode="scaleToFill" fixedFrame="YES" contentHorizontalAlignment="center" contentVerticalAlignment="center" buttonType="roundedRect" lineBreakMode="middleTruncation" translatesAutoresizingMaskIntoConstraints="NO" id="9u4-b8-vmX">
                            <rect key="frame" x="137" y="138" width="100" height="30"/>
                            <autoresizingMask key="autoresizingMask" flexibleMaxX="YES" flexibleMaxY="YES"/>
                            <state key="normal" title="Sign Out"/>
                            <connections>
                                <action selector="signoutButton:" destination="BYZ-38-t0r" eventType="touchUpInside" id="kZT-P8-0Zy"/>
                            </connections>
                        </button>
                    </subviews>
                    <color key="backgroundColor" red="1" green="1" blue="1" alpha="1" colorSpace="custom" customColorSpace="sRGB"/>
                </view>
                <connections>
                    <outlet property="loggingText" destination="qXW-2z-J7K" id="uqO-Yw-AsK"/>
                    <outlet property="signoutButton" destination="9u4-b8-vmX" id="OCh-qk-ldv"/>
                </connections>
            </viewController>
            <placeholder placeholderIdentifier="IBFirstResponder" id="dkx-z0-nzr" sceneMemberID="firstResponder"/>
        </objects>
        <point key="canvasLocation" x="140" y="137.18140929535232"/>
    </scene>
</scenes>
```
