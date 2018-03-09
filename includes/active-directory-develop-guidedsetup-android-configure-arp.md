
## <a name="add-the-applications-registration-information-to-your-app"></a>Přidat informace o registraci aplikace do aplikace

V tomto kroku budete muset do projektu přidejte ID klienta.

1.  Otevřete `MainActivity` (v části `app`  >  `java`  >   *`{host}.{namespace}`* )
2.  Nahraďte řádek `final static String CLIENT_ID` se:
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. Otevřete: `app` > `manifests` > `AndroidManifest.xml`
4. Přidejte následující aktivitu pro `manifest\application` uzlu. Tato registrace `BrowserTabActivity` umožňující operačního systému a pokračovat v aplikaci po dokončení ověřování:

```xml
<!--Intent filter to capture System Browser calling back to our app after Sign In-->
<activity
    android:name="com.microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />

        <!--Add in your scheme/host from registered redirect URI-->
        <!--By default, the scheme should be similar to 'msal[appId]' -->
        <data android:scheme="msal[Enter the application Id here]"
            android:host="auth" />
    </intent-filter>
</activity>
```

