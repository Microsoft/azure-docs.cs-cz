
## <a name="add-the-applications-registration-information-to-your-app"></a>Přidat informace o registraci aplikace do aplikace

V tomto kroku budete muset do projektu přidejte ID aplikace:

1.  V `ViewController.swift`, nahraďte řádek '`let kClientID`' s:
```swift
let kClientID = "[Enter the application Id here]"
```
<!-- Workaround for Docs conversion bug -->
<ol start="2">
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
            <string>msal[Enter the application Id here]</string>
            <string>auth</string>
        </array>
    </dict>
</array>
```
