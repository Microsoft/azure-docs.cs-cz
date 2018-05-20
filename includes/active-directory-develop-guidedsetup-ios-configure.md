## <a name="register-your-application"></a>Registrace vaší aplikace
Aplikace můžete zaregistrovat v některém ze dvou způsobů, jak je popsáno v následujících dvou částech.

### <a name="option-1-express-mode"></a>Možnost 1: Expresní režim
Nyní je nutné zaregistrovat aplikaci v *portálu pro registraci aplikace Microsoft*:
1. Registrace vaší aplikace pomocí [portálu pro registraci aplikace Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure)
2.  Zadejte název vaší aplikace a e-mailu
3.  Ujistěte se, že je zaškrtnuté políčko pro instalaci na základě
4.  Postupujte podle pokynů a získat ID aplikace a vložte jej do vašeho kódu

### <a name="option-2-advanced-mode"></a>Možnost 2: Pokročilého režimu

1.  Přejděte na [portálu pro registraci aplikace Microsoft](https://apps.dev.microsoft.com/portal/register-app)
2.  Zadejte název vaší aplikace a e-mailu
3.  Ujistěte se, že není zaškrtnuto políčko pro instalaci na základě
4.  Klikněte na tlačítko `Add Platform`, zvolte položku `Native Application` a klikněte na tlačítko `Save`
5.  Přejděte zpět na Xcode. V `ViewController.swift`, nahraďte řádek "`let kClientID`" s ID aplikace, který jste právě zaregistrovali:

```swift
let kClientID = "Your_Application_Id_Here"
```

<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
CTRL + klikněte na tlačítko <code>Info.plist</code> zprovoznit v kontextové nabídce a potom klikněte na: <code>Open As</code> > <code>Source Code</code>
</li>
<li>
V části <code>dict</code> kořenový uzel, přidejte následující:
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Your_Application_Id_Here]</string>
            <string>auth</string>
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
Nahraďte <i> <code>[Your_Application_Id_Here]</code> </i> s Id aplikace, který jste právě zaregistrovali
</li>
</ol>
