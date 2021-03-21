---
title: Použití zprostředkovatelů s Xamarin iOS & Androidem | Azure
titleSuffix: Microsoft identity platform
description: Naučte se, jak nastavit aplikace Xamarin iOS, které můžou používat Microsoft Authenticator a Microsoft Authentication Library pro .NET (MSAL.NET). Také se dozvíte, jak migrovat z knihovny ověřování Azure AD pro .NET (ADAL.NET) do knihovny Microsoft Authentication Library pro .NET (MSAL.NET).
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 226e94510709b37a7e6b1aae90a7e0ec5b4222b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103199581"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Použití Microsoft Authenticator nebo Portál společnosti Intune v aplikacích Xamarin

V zařízeních s Androidem a iOS poskytují zprostředkovatelé jako Microsoft Authenticator a Microsoft Intune Portál společnosti specifické pro Android:

- **Jednotné přihlašování (SSO)**: uživatelé se nemusí přihlašovat ke každé aplikaci.
- **Identifikace zařízení**: Zprostředkovatel přistupuje k certifikátu zařízení. Tento certifikát se vytvoří na zařízení, když je připojený k pracovišti.
- **Ověření identifikace aplikace**: když aplikace volá zprostředkovatele, PŘEDÁ adresu URL pro přesměrování. Zprostředkovatel ověří adresu URL.

Chcete-li povolit jednu z těchto funkcí, použijte `WithBroker()` parametr při volání `PublicClientApplicationBuilder.CreateApplication` metody. `.WithBroker()`Ve výchozím nastavení je parametr nastaven na hodnotu true.

Nastavení zprostředkovaných ověřování v knihovně Microsoft Authentication Library pro .NET (MSAL.NET) se liší podle platformy:

* [aplikace pro iOS](#brokered-authentication-for-ios)
* [Aplikace pro Android](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>Zprostředkované ověřování pro iOS

Pomocí následujících kroků můžete aplikaci Xamarin. iOS povolit komunikaci s aplikací [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) . Pokud cílíte na iOS 13, zvažte čtení informací o [změně rozhraní API společnosti Apple](./msal-net-xamarin-ios-considerations.md).

### <a name="step-1-enable-broker-support"></a>Krok 1: povolení podpory zprostředkovatele

Je nutné povolit podporu zprostředkovatele pro jednotlivé instance `PublicClientApplication` . Ve výchozím nastavení je podpora zakázaná. Při vytváření `PublicClientApplication` prostřednictvím `PublicClientApplicationBuilder` použijte `WithBroker()` parametr, jak ukazuje následující příklad. `WithBroker()`Ve výchozím nastavení je parametr nastaven na hodnotu true.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Krok 2: povolení přístupu k řetězci klíčů

Pokud chcete povolit přístup pomocí řetězce klíčů, musíte mít přístupovou skupinu pro řetězce klíčů pro vaši aplikaci. Pomocí `WithIosKeychainSecurityGroup()` rozhraní API můžete nastavit přístupovou skupinu pro řetězce klíčů při vytváření aplikace:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Další informace najdete v tématu [Povolení přístupu k řetězci klíčů](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Krok 3: aktualizace AppDelegate pro zpracování zpětného volání

Když MSAL.NET volá zprostředkovatele, zprostředkovatel volá zpět do vaší aplikace prostřednictvím `OpenUrl` metody `AppDelegate` třídy. Vzhledem k tomu, že MSAL čeká na odpověď zprostředkovatele, musí vaše aplikace spolupracovat a volat MSAL.NET zpátky. Pokud chcete tuto spolupráci povolit, aktualizujte soubor *AppDelegate. cs* a přepište následující metodu.

```csharp
public override bool OpenUrl(UIApplication app, NSUrl url,
                             string sourceApplication,
                             NSObject annotation)
{
    if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
    {
      AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
      return true;
    }

    else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
    {
         return false;
    }

    return true;
}
```

Tato metoda je vyvolána při každém spuštění aplikace. Používá se jako příležitost ke zpracování odpovědi od zprostředkovatele a dokončení procesu ověřování, který MSAL.NET zahájil.

### <a name="step-4-set-uiviewcontroller"></a>Krok 4: nastavte UIViewController ()

Stále v souboru *AppDelegate. cs* nastavte okno objektu. Obvykle není nutné nastavovat okno objektu pro Xamarin iOS, ale k posílání a přijímání odpovědí od zprostředkovatele budete potřebovat okno objektu.

Nastavení okna objektu:

1. V souboru *AppDelegate. cs* nastavte `App.RootViewController` na nový `UIViewController()` . Toto přiřazení zajišťuje, že volání do služby Broker zahrnuje `UIViewController` . Pokud je toto nastavení nesprávně přiřazeno, může se zobrazit tato chyba:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Při `AcquireTokenInteractive` volání použijte `.WithParentActivityOrWindow(App.RootViewController)` a pak předejte odkaz na okno objektu, které budete používat.

    V *App. cs*:

    ```csharp
       public static object RootViewController { get; set; }
    ```

    V *AppDelegate. cs*:

    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```

    V `AcquireToken` volání:

    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>Krok 5: registrace schématu adresy URL

MSAL.NET používá adresy URL k vyvolání zprostředkovatele a pak vrátí odpověď zprostředkovatele do vaší aplikace. Pokud chcete dokončit operaci round trip, zaregistrujte schéma URL vaší aplikace v souboru *info. plist* .

`CFBundleURLSchemes`Název musí obsahovat `msauth.` předponu. Použijte předponu s `CFBundleURLName` příponou.

V rámci schématu adresy URL `BundleId` aplikace jednoznačně identifikuje: `$"msauth.(BundleId)"` . Pokud ano `BundleId` `com.yourcompany.xforms` , pak je schéma URL `msauth.com.yourcompany.xforms` .

> [!NOTE]
> Toto schéma URL se označuje jako součást identifikátoru URI přesměrování, který jedinečně identifikuje vaši aplikaci, když obdrží odpověď od zprostředkovatele.

```XML
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Krok 6: Přidání identifikátoru zprostředkovatele do oddílu LSApplicationQueriesSchemes

MSAL používá `–canOpenURL:` ke kontrole, jestli je na zařízení nainstalovaný zprostředkovatel. V systému iOS 9 byla společnost Apple uzamčena schématy, pro které se aplikace může dotazovat.

Přidejte `msauthv2` do `LSApplicationQueriesSchemes` části souboru *info. plist* , jak je uvedeno v následujícím příkladu:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-add-a-redirect-uri-to-your-app-registration"></a>Krok 7: Přidání identifikátoru URI přesměrování k registraci vaší aplikace

Pokud použijete zprostředkovatele, váš identifikátor URI pro přesměrování má dodatečný požadavek. Identifikátor URI pro přesměrování _musí_ mít následující formát:

```csharp
$"msauth.{BundleId}://auth"
```

Tady je příklad:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth";
```

Všimněte si, že identifikátor URI přesměrování odpovídá `CFBundleURLSchemes` názvu, který jste zahrnuli do souboru *info. plist* .

Přidejte identifikátor URI přesměrování do registrace aplikace v [Azure Portal](https://portal.azure.com). Pokud chcete vygenerovat správně formátovaný identifikátor URI pro přesměrování, pomocí **Registrace aplikací** v Azure Portal VYGENERUJTE identifikátor URI zprostředkované přesměrování z ID sady prostředků.

**Vygenerujte identifikátor URI pro přesměrování:**

1. Přihlaste se na <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
1. Vyberte **Azure Active Directory**  >  **Registrace aplikací** > zaregistrovanou aplikaci.
1. Výběr **ověřování**  >  **Přidání platformy**  >  **iOS/MacOS**
1. Zadejte ID sady prostředků a pak vyberte **Konfigurovat**.

    Zkopírujte vygenerovaný identifikátor URI pro přesměrování, který se zobrazí v textovém poli **přesměrování URI** pro zahrnutí do kódu:

    :::image type="content" source="media/msal-net-use-brokers-with-xamarin-apps/portal-01-ios-platform-settings.png" alt-text="nastavení platformy iOS s generovaným identifikátorem URI přesměrování v Azure Portal":::
1. Pokud chcete dokončit generování identifikátoru URI přesměrování, vyberte **Hotovo** .

## <a name="brokered-authentication-for-android"></a>Zprostředkované ověřování pro Android

### <a name="step-1-enable-broker-support"></a>Krok 1: povolení podpory zprostředkovatele

Podpora zprostředkovatele je povolena po jednotlivých `PublicClientApplication` případech. Ve výchozím nastavení je zakázaný. `WithBroker()`Při vytváření prostřednictvím použijte parametr (ve výchozím nastavení nastaven na hodnotu true) `IPublicClientApplication` `PublicClientApplicationBuilder` .

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) // See step #4
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Krok 2: aktualizace AppDelegate pro zpracování zpětného volání

Když MSAL.NET volá zprostředkovatele, zprostředkovatel pak vrátí zpět do vaší aplikace pomocí `OnActivityResult()` metody. Vzhledem k tomu, že MSAL bude čekat na odpověď od služby Broker, musí vaše aplikace směrovat výsledek do MSAL.NET.

Přesměrujte výsledek do `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` metody přepsáním `OnActivityResult()` metody, jak je znázorněno zde:

```csharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Tato metoda je vyvolána pokaždé, když se spustí aplikace zprostředkovatele, a používá se jako příležitost ke zpracování odpovědi od služby Broker a dokončení procesu ověřování spuštěného nástrojem MSAL.NET.

### <a name="step-3-set-an-activity"></a>Krok 3: nastavení aktivity

Chcete-li povolit zprostředkované ověřování, nastavte aktivitu tak, aby MSAL mohl odeslat a přijmout odpověď do služby Broker. Provedete to tak, že zadáte aktivitu (obvykle `MainActivity` ) `WithParentActivityOrWindow(object parent)` nadřazenému objektu.

Například v volání metody `AcquireTokenInteractive()` :

```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-add-a-redirect-uri-to-your-app-registration"></a>Krok 4: Přidání identifikátoru URI přesměrování k registraci vaší aplikace

MSAL používá adresy URL pro vyvolání zprostředkovatele a pak se vrátí do vaší aplikace. K dokončení této operace odezvy Zaregistrujte **identifikátor URI pro přesměrování** vaší aplikace pomocí [Azure Portal](https://portal.azure.com).

Formát identifikátoru URI přesměrování vaší aplikace závisí na certifikátu použitém k podepsání APK. Například:

```
msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

Poslední část identifikátoru URI `hgbUYHVBYUTvuvT&Y6tr554365466=` je verze signatury s kódováním base64, pomocí které je APK podepsáno. Při vývoji aplikace v aplikaci Visual Studio, pokud ladíte kód bez podepisování APK pomocí konkrétního certifikátu, Visual Studio podepíše APK za vás pro účely ladění. Když Visual Studio podepíše APK za vás tímto způsobem, poskytne mu jedinečný podpis pro počítač, na kterém je postaven. To znamená, že při každém sestavení aplikace na jiném počítači budete muset aktualizovat identifikátor URI přesměrování v kódu aplikace a registraci aplikace v Azure Portal, aby se ověřilo pomocí MSAL.

Během ladění se může objevit výjimka MSAL (nebo zpráva protokolu) oznamující, že zadaný identifikátor URI přesměrování není správný. **Výjimka nebo zpráva protokolu také indikuje identifikátor URI přesměrování, který byste měli používat** s aktuálním počítačem, na kterém ladíte. K pokračování v vývoji aplikace můžete použít poskytnutý identifikátor URI pro přesměrování, pokud aktualizujete identifikátor URI přesměrování v kódu a přidáte poskytnutý identifikátor URI pro přesměrování do registrace aplikace v Azure Portal.

Až budete připraveni k finalizaci kódu, aktualizujte identifikátor URI přesměrování v kódu a registraci aplikace v Azure Portal pro použití signatury certifikátu, pomocí kterého podepisujete APK.

V praxi to znamená, že byste měli zvážit přidání identifikátoru URI přesměrování pro každého člena vývojového týmu a *také* identifikátor URI přesměrování pro produkční verzi APK.

Podpis můžete vypočítat sami, podobně jako MSAL.

```csharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

Máte také možnost získat podpis balíčku pomocí **nástroje nástroje** s následujícími příkazy:

* Windows:
    ```console
    keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android
    ````
* MacOS
    ```console
    keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64
    ````

### <a name="step-5-optional-fall-back-to-the-system-browser"></a>Krok 5 (volitelné): Vraťte se do prohlížeče systému

Pokud je MSAL nakonfigurovaný na použití zprostředkovatele, ale zprostředkovatel není nainstalovaný, MSAL se vrátí k používání webového zobrazení (prohlížeč). MSAL se pokusí ověřit pomocí výchozího systémového prohlížeče na zařízení, což selže, protože identifikátor URI přesměrování je nakonfigurovaný pro zprostředkovatele a systémový prohlížeč neví, jak ho použít k přechodu zpět na MSAL. Chcete-li se této chybě vyhnout, můžete nakonfigurovat *Filtr záměrů* pomocí identifikátoru URI přesměrování zprostředkovatele, který jste použili v kroku 4.

Upravte manifest vaší aplikace a přidejte tak filtr záměru:

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="Package Name"
                    android:path="/Package Signature"/>
```

Například pokud máte identifikátor URI přesměrování `msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=` , váš manifest by měl vypadat jako následující fragment kódu XML.

Hodnota lomítko ( `/` ) před podpisem v `android:path` hodnotě je **povinná**.

```xml
<!-- NOTE the SLASH (required) that prefixes the signature value in the path attribute.
     The signature value is the Base64-encoded signature discussed above. -->
<intent-filter>
      <data android:scheme="msauth"
                    android:host="com.microsoft.xforms.testApp"
                    android:path="/hgbUYHVBYUTvuvT&Y6tr554365466="/>
```

Další informace o konfiguraci aplikace pro prohlížeč systému a podporu pro Android 11 najdete v článku o [aktualizaci manifestu Android pro podporu prohlížeče systému](msal-net-xamarin-android-considerations.md#update-the-android-manifest-for-system-webview-support).

Alternativně můžete nakonfigurovat MSAL tak, aby se vrátil do vloženého prohlížeče, který nespoléhá na identifikátor URI přesměrování:

```csharp
.WithUseEmbeddedWebUi(true)
```

## <a name="troubleshooting-tips-for-android-brokered-authentication"></a>Tipy pro řešení potíží pro zprostředkované ověřování v Androidu

Tady je několik tipů pro předcházení problémům při implementaci zprostředkovaných ověřování na Androidu:

- **Identifikátor URI pro přesměrování** – přidejte identifikátor URI přesměrování k registraci vaší aplikace v [Azure Portal](https://portal.azure.com/). Chybějící nebo nesprávný identifikátor URI pro přesměrování je běžný problém zjištěný vývojáři.
- **Verze zprostředkovatele** – nainstalujte minimální požadovanou verzi zprostředkovatele aplikace. Jednu z těchto dvou aplikací lze použít pro zprostředkované ověřování v Androidu.
  - [Portál společnosti Intune](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal) (verze 5.0.4689.0 nebo novější)
  - [Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) (verze 6.2001.0140 nebo novější).
- **Priorita zprostředkovatele** – MSAL komunikuje s *prvním agentem nainstalovaným* na zařízení při instalaci více zprostředkovatelů.

    Příklad: Pokud poprvé nainstalujete Microsoft Authenticator a pak nainstalujete Portál společnosti Intune, provede se zprostředkované ověřování *jenom* na Microsoft Authenticator.
- **Protokoly** – Pokud narazíte na problém s zprostředkovaný ověřováním, může vám zobrazení protokolů zprostředkovatele pomáhat s diagnostikou příčiny.
  - Získat protokoly Microsoft Authenticator:

    1. Vyberte tlačítko nabídky v pravém horním rohu aplikace.
    1. Vyberte **Odeslat zpětnou vazbu**  >  **s problémy?**.
    1. V části **co se pokoušíte provést?** vyberte možnost a přidejte popis.
    1. Pokud chcete protokoly odeslat, vyberte šipku v pravém horním rohu aplikace.

    Po odeslání protokolů se v dialogovém okně zobrazí ID incidentu. Poznamenejte si ID incidentu a zahrňte ho po podání žádosti o pomoc.

  - Získat protokoly Portál společnosti Intune:

    1. V levém horním rohu aplikace vyberte tlačítko nabídky.
    1. Vyberte   >  **podporu e-mailu** pro pomoc.
    1. Pokud chcete protokoly odeslat, vyberte **jenom nahrávat protokoly**.

    Po odeslání protokolů se v dialogovém okně zobrazí ID incidentu. Poznamenejte si ID incidentu a zahrňte ho po podání žádosti o pomoc.

## <a name="next-steps"></a>Další kroky

Seznamte [se s důležitými informacemi o použití Univerzální platforma Windows s MSAL.NET](msal-net-uwp-considerations.md).
