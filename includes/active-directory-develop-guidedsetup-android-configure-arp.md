## <a name="add-the-applications-registration-information-to-your-app"></a>Informace o registraci vaší aplikace přidejte do své aplikace

V tomto kroku potřebujete ID klienta přidat do projektu.

1.  Otevřít `MainActivity` (v části `app`  >  `java`  >  *`{host}.{namespace}`*)
2.  Nahraďte řádek začínající `final static String CLIENT_ID` pomocí:
```java
final static String CLIENT_ID = "[Enter the application Id here]";
```
3. Otevřít: `app` > `manifests` > `AndroidManifest.xml`
4. Přidejte následující aktivitu `manifest\application` uzlu. Tento registr `BrowserTabActivity` umožňující operačního systému a pokračovat po dokončení ověřování vaší aplikace:

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

