## <a name="setting-up-your-ios-application"></a>Nastavení aplikace iOS

Tato část obsahuje podrobné pokyny pro vytvoření nového projektu do ukazují, jak integrovat aplikace pro iOS (Swift) s *přihlášení se společností Microsoft* , může se dotázat webovým rozhraním API, které vyžadují token.

> Stáhněte si tento ukázkový projekt XCode místo dávají přednost? [Stažení projektu](https://github.com/Azure-Samples/active-directory-ios-swift-native-v2/archive/master.zip) a pokračujte [krok konfigurace](#register-your-application) před provedením konfigurace ukázka kódu.


## <a name="install-carthage-to-download-and-build-msal"></a>Nainstalujte Carthage ke stažení a sestavení MSAL
Správce balíčků Carthage se používá během období preview MSAL – integruje se s XCode při zachování schopnost Microsoft provést změny do knihovny.

- Stáhněte a nainstalujte nejnovější verzi Carthage [sem](https://github.com/Carthage/Carthage/releases "Carthage adresa URL pro stahování")

## <a name="creating-your-application"></a>Vytvoření vaší aplikace

1.  Otevřete Xcode a vyberte `Create a new Xcode project`
2.  Vyberte `iOS`  >  `Single view Application` a klikněte na tlačítko *další*
3.  Zadejte název produktu a klikněte na *další*
4.  Vyberte složku pro vytvoření aplikace a klikněte na tlačítko *vytvořit*

## <a name="build-the-msal-framework"></a>Sestavení rozhraní MSAL

Postupujte podle pokynů pro vyžádání obsahu a následně vytvořit nejnovější verze knihoven MSAL pomocí Carthage:

1.  Otevřete terminál bash a přejděte do kořenové složky aplikace
2.  Kopírování níže a vkládání v terminálu bash k vytvoření souboru 'Cartfile':

```bash
echo "github \"AzureAD/microsoft-authentication-library-for-objc\" \"master\"" > Cartfile
```
<!-- Workaround for Docs conversion bug -->
<ol start="3">
<li>
Zkopírujte a vložte níže. Tento příkaz načte závislosti do složky Carthage/rezervace, a poté vytvoří MSAL knihovny:
</li>
</ol>

```bash
carthage update
```

> Výše uvedené procesu se používá ke stažení a sestavení knihovny ověřování společnosti Microsoft (MSAL). MSAL zpracovává získávání, ukládání do mezipaměti a aktualizaci tokeny uživatel používá pro přístup k rozhraní API, které jsou chráněné službou Azure Active Directory v2.

## <a name="add-the-msal-framework-to-your-application"></a>Přidání rozhraní MSAL do aplikace
1.  V Xcode otevřete `General` karta
2.  Přejděte na `Linked Frameworks and Libraries` a klikněte na `+`
3.  Vyberte `Add other…`
4.  Vyberte: `Carthage`  >  `Build`  >  `iOS`  >  `MSAL.framework` a klikněte na tlačítko *otevřete*. Měli byste vidět `MSAL.framework` přidán do seznamu.
5.  Přejděte na `Build Phases` a klikněte na `+` ikonu, zvolte `New Run Script Phase`
6.  Přidejte následující obsah *skript oblasti*:

```text
/usr/local/bin/carthage copy-frameworks
```

<!-- Workaround for Docs conversion bug -->
<ol start="7">
<li>
Přidejte následující <code>Input Files</code> kliknutím <code>+</code>:
</li>
</ol>

```text
$(SRCROOT)/Carthage/Build/iOS/MSAL.framework
```

## <a name="creating-your-applications-ui"></a>Vytvoření uživatelského rozhraní aplikace
Soubor Main.storyboard by měl automaticky vytvoří jako součást vaše šablona projektu. Postupujte podle pokynů k vytvoření aplikace uživatelského rozhraní:

1.  CTRL + klikněte na tlačítko `Main.storyboard` zprovoznit v kontextové nabídce a potom klikněte na: `Open As` > `Source Code`
2.  Nahraďte `<scenes>` uzel s následující kód:

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
                            <state key="normal" title="Sign-Out"/>
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
