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
ms.custom: aaddev
ms.openlocfilehash: 1a57173311278c5e3e0304aeb12d4d6999379eb5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "84692321"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Použití Microsoft Authenticator nebo Portál společnosti Intune v aplikacích Xamarin

V zařízeních s Androidem a iOS poskytují zprostředkovatelé jako Microsoft Authenticator a Microsoft Intune Portál společnosti specifické pro Android:

- **Jednotné přihlašování (SSO)**: uživatelé se nemusí přihlašovat ke každé aplikaci.
- **Identifikace zařízení**: Zprostředkovatel přistupuje k certifikátu zařízení. Tento certifikát se vytvoří na zařízení, když je připojený k pracovišti.
- **Ověření identifikace aplikace**: když aplikace volá zprostředkovatele, PŘEDÁ adresu URL pro přesměrování. Zprostředkovatel ověří adresu URL.

Chcete-li povolit jednu z těchto funkcí, použijte `WithBroker()` parametr při volání `PublicClientApplicationBuilder.CreateApplication` metody. `.WithBroker()`Ve výchozím nastavení je parametr nastaven na hodnotu true. 

Pomocí pokynů v následujících částech můžete nastavit zprostředkované ověřování pro aplikace [iOS](#brokered-authentication-for-ios) nebo aplikace pro [Android](#brokered-authentication-for-android) .

## <a name="brokered-authentication-for-ios"></a>Zprostředkované ověřování pro iOS

Pomocí následujících kroků můžete aplikaci Xamarin. iOS povolit komunikaci s aplikací [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

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
Když Microsoft Authentication Library pro .NET (MSAL.NET) volá zprostředkovatele, zprostředkovatel volá zpět do vaší aplikace prostřednictvím `OpenUrl` metody `AppDelegate` třídy. Vzhledem k tomu, že MSAL čeká na odpověď zprostředkovatele, musí vaše aplikace spolupracovat a volat MSAL.NET zpátky. Pokud chcete tuto spolupráci povolit, aktualizujte `AppDelegate.cs` soubor, aby se přepsala následující metoda.

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
Stále v `AppDelegate.cs` souboru je nutné nastavit okno objektu. Pro Xamarin iOS obvykle není nutné nastavovat okno objektu. K posílání a přijímání odpovědí od zprostředkovatele ale potřebujete okno objektu. 

Nastavení okna objektu: 
1. V `AppDelegate.cs` souboru nastavte `App.RootViewController` na nový `UIViewController()` . Toto přiřazení zajišťuje, že volání do služby Broker zahrnuje `UIViewController` . Pokud je toto nastavení nesprávně přiřazeno, může se zobrazit tato chyba:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Při `AcquireTokenInteractive` volání použijte `.WithParentActivityOrWindow(App.RootViewController)` a pak předejte odkaz na okno objektu, které budete používat.

    V `App.cs`:

    ```csharp
       public static object RootViewController { get; set; }
    ```

    V `AppDelegate.cs`:

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
MSAL.NET používá adresy URL k vyvolání zprostředkovatele a pak vrátí odpověď zprostředkovatele do vaší aplikace. Pokud chcete dokončit operaci round trip, zaregistrujte do souboru schéma URL pro vaši aplikaci `Info.plist` .

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

Přidejte `msauthv2` do `LSApplicationQueriesSchemes` části `Info.plist` souboru, jak je uvedeno v následujícím příkladu:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Krok 7: registrace identifikátoru URI přesměrování na portálu aplikací

Pokud použijete zprostředkovatele, váš identifikátor URI pro přesměrování má dodatečný požadavek. Identifikátor URI pro přesměrování _musí_ mít následující formát:

```csharp
$"msauth.{BundleId}://auth"
```

Tady je příklad:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```

Všimněte si, že identifikátor URI přesměrování odpovídá `CFBundleURLSchemes` názvu, který jste zahrnuli do `Info.plist` souboru.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Krok 8: Ujistěte se, že je identifikátor URI přesměrování zaregistrován u vaší aplikace.

Identifikátor URI pro přesměrování musí být zaregistrován na [portálu pro registraci aplikací](https://portal.azure.com) jako platný identifikátor URI přesměrování pro vaši aplikaci. 

Portál pro registraci aplikací nabízí nové prostředí, které vám pomůžou vypočítat identifikátor URI zprostředkované odpovědi z ID sady prostředků. 

Výpočet identifikátoru URI přesměrování:

1. Na portálu pro registraci aplikací vyberte **ověřování**  >  **vyzkoušet nové prostředí**.

   ![Vyzkoušejte si nové prostředí pro registraci aplikací](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Vyberte **Přidat platformu**.

   ![Přidat platformu](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Až se seznam platforem podporuje, vyberte **iOS**.

   ![Konfigurace iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Zadejte ID sady prostředků podle požadavků a pak vyberte **Konfigurovat**.

   ![Zadejte ID sady prostředků.](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Až budete s postupem hotovi, vypočítává se identifikátor URI pro přesměrování.

![Kopírovat identifikátor URI pro přesměrování](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Zprostředkované ověřování pro Android

### <a name="step-1-enable-broker-support"></a>Krok 1: povolení podpory zprostředkovatele

Podpora zprostředkovatele je povolená na základě PublicClientApplication. Ve výchozím nastavení je zakázaný. `WithBroker()`Při vytváření prostřednictvím použijte parametr (ve výchozím nastavení nastaven na hodnotu true) `IPublicClientApplication` `PublicClientApplicationBuilder` .

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Krok 2: aktualizace AppDelegate pro zpracování zpětného volání

Když MSAL.NET volá zprostředkovatele, zprostředkovatel pak vrátí zpět do vaší aplikace pomocí metody OnActivityResult (). Vzhledem k tomu, že MSAL bude čekat na odpověď od služby Broker, musí vaše aplikace směrovat výsledek do MSAL.NET.
Toho lze dosáhnout směrováním výsledku do rozhraní `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` přepsáním metody OnActivityResult (), jak je uvedeno níže.

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Tato metoda je vyvolána pokaždé, když se spustí aplikace zprostředkovatele a používá se jako příležitost ke zpracování odpovědi od zprostředkovatele a dokončení procesu ověřování spuštěného nástrojem MSAL.NET.

### <a name="step-3-set-an-activity"></a>Krok 3: nastavení aktivity

Aby mohl zprostředkované ověřování fungovat, musíte nastavit aktivitu tak, aby MSAL mohla Odeslat a přijmout odpověď od zprostředkovatele.

K tomu je nutné zadat aktivitu (obvykle MainActivity) pro `WithParentActivityOrWindow(object parent)` nadřazený objekt. 

**Příklad:**

V volání metody získání tokenu:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>Krok 4: registrace RedirectUri na portálu aplikací

MSAL používá adresy URL k vyvolání zprostředkovatele a pak se vrátí zpět do vaší aplikace. K dokončení této zpáteční cesty musíte pro svoji aplikaci zaregistrovat schéma adresy URL. Tento identifikátor URI pro přesměrování musí být registrovaný na portálu pro registraci aplikací Azure AD jako platný identifikátor URI přesměrování pro vaši aplikaci.


Identifikátor URI přesměrování, který je potřeba pro vaši aplikaci, závisí na certifikátu použitém k podepsání APK.

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

Poslední část identifikátoru URI `hgbUYHVBYUTvuvT&Y6tr554365466=` je podpis, pomocí kterého je APK podepsán, kódování Base64.
Nicméně během vývojové fáze aplikace pomocí sady Visual Studio, pokud ladíte kód bez podepisování APK pomocí konkrétního certifikátu, Visual Studio podepíše APK za vás za účelem ladění, takže APK jedinečný podpis pro počítač, na kterém je postaven. To znamená, že při každém sestavení aplikace na jiném počítači budete muset aktualizovat identifikátor URI přesměrování v kódu aplikace a registraci aplikace v Azure Portal, aby se ověřilo pomocí MSAL. 

Během ladění se může objevit výjimka MSAL (nebo zpráva protokolu) oznamující, že zadaný identifikátor URI přesměrování není správný. **Tato výjimka vám taky poskytne identifikátor URI pro přesměrování, který byste měli používat** s aktuálním počítačem, na který ladíte. Tento identifikátor URI pro přesměrování můžete použít k pokračování vývoje pro daný čas.

Až budete připraveni k finalizaci kódu, nezapomeňte aktualizovat identifikátor URI přesměrování v kódu a v registraci aplikace v Azure Portal pro použití signatury certifikátu, pomocí kterého budete APK podepisovat.

V praxi to znamená, že je nutné zaregistrovat identifikátor URI přesměrování pro každého člena týmu a také identifikátor URI přesměrování pro verzi APK podepsanou v produkčním prostředí.

Tento podpis můžete také vypočítat sami, podobně jako MSAL: 

```CSharp
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

Máte také možnost získat podpis balíčku pomocí nástroje, a to pomocí následujících příkazů:

Pro Windows:`keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

Pro Mac:`keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>Další kroky

Seznamte [se s důležitými informacemi o použití Univerzální platforma Windows s MSAL.NET](msal-net-uwp-considerations.md).
