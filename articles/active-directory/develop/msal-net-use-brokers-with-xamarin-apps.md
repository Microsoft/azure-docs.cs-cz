---
title: Použijte makléři s Xamarin iOS & Android | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak nastavit aplikace Xamarin pro iOS, které můžou používat Microsoft Authenticator a Microsoft Authentication Library pro .NET (MSAL.NET). Přečtěte si také, jak migrovat z knihovny ověřování Azure AD pro .NET (ADAL.NET) do knihovny microsoft authentication library pro .NET (MSAL.NET).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262786"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Použití microsoft authenticatoru nebo portálu společnosti Intune v aplikacích Xamarin

V systémech Android a iOS umožňují zprostředkovatelé, jako je Microsoft Authenticator a portál Microsoft Intune Company Portal pro Android,:

- **Jednotné přihlašování :** Uživatelé se nemusí přihlašovat ke každé aplikaci.
- **Identifikace zařízení**: Zprostředkovatel přistupuje k certifikátu zařízení. Tento certifikát se vytvoří na zařízení, když je připojen k pracovišti.
- **Ověření identifikace aplikace**: Když aplikace zavolá zprostředkovatele, předá adresu URL přesměrování. Zprostředkovatel ověří adresu URL.

Chcete-li povolit jednu `WithBroker()` z těchto `PublicClientApplicationBuilder.CreateApplication` funkcí, použijte parametr při volání metody. Parametr `.WithBroker()` je ve výchozím nastavení nastaven na hodnotu true. 

Pomocí pokynů v následujících částech můžete také nastavit zprostředkované ověřování pro aplikace [pro iOS](#brokered-authentication-for-ios) nebo [Android.](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>Zprostředkované ověřování pro iOS

Pomocí následujících kroků povolte aplikaci Xamarin.iOS mluvit s aplikací [Microsoft Authenticator.](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)

### <a name="step-1-enable-broker-support"></a>Krok 1: Povolit podporu zprostředkovatele
Je nutné povolit podporu zprostředkovatele pro jednotlivé instance `PublicClientApplication`. Podpora je ve výchozím nastavení zakázána. Při vytváření `PublicClientApplication` `PublicClientApplicationBuilder`prostřednictvím `WithBroker()` použijte parametr jako následující příklad ukazuje. Parametr `WithBroker()` je ve výchozím nastavení nastaven na hodnotu true.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Krok 2: Povolení přístupu ke klíčence

Chcete-li povolit přístup k řetězci klíčů, musíte mít pro vaši aplikaci přístupovou skupinu řetězce klíčů. `WithIosKeychainSecurityGroup()` Rozhraní API můžete použít k nastavení skupiny přístupu k řetězci klíčů při vytváření aplikace:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Další informace naleznete v [tématu Enable keychain access](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Krok 3: Aktualizace appdelegate pro zpracování zpětného volání
Když Microsoft Authentication Library pro .NET (MSAL.NET) volá zprostředkovatele, `OpenUrl` zprostředkovatel volá `AppDelegate` zpět do vaší aplikace prostřednictvím metody třídy. Vzhledem k tomu, že MSAL čeká na odpověď zprostředkovatele, vaše aplikace musí spolupracovat volat MSAL.NET zpět. Chcete-li tuto spolupráci povolit, aktualizujte `AppDelegate.cs` soubor tak, aby přepsal následující metodu.

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

Tato metoda je vyvolána při každém spuštění aplikace. Používá se jako příležitost ke zpracování odpovědi od zprostředkovatele a dokončení procesu ověřování, který MSAL.NET spuštěn.

### <a name="step-4-set-uiviewcontroller"></a>Krok 4: Nastavení UIViewController()
Stále v `AppDelegate.cs` souboru je třeba nastavit okno objektu. Normálně pro Xamarin iOS není nutné nastavit okno objektu. Ale potřebujete okno objektu pro odesílání a přijímání odpovědí od zprostředkovatele. 

Nastavení okna objektu: 
1. V `AppDelegate.cs` souboru `App.RootViewController` nastavte na `UIViewController()`nový . Toto přiřazení zajišťuje, že volání `UIViewController`zprostředkovatele zahrnuje . Pokud je toto nastavení přiřazeno nesprávně, může se stát, že se zobrazí tato chyba:

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

    Ve `AcquireToken` výzvě:

    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>Krok 5: Registrace schématu adres URL
MSAL.NET používá adresy URL k vyvolání makléře a pak vrátit odpověď zprostředkovatele do vaší aplikace. Chcete-li dokončit zpáteční cestu, zaregistrujte schéma `Info.plist` adres URL pro aplikaci v souboru.

Název `CFBundleURLSchemes` musí `msauth.` obsahovat jako předponu. Postupujte podle `CFBundleURLName`předpony s . 

V schématu `BundleId` adres URL jednoznačně identifikuje `$"msauth.(BundleId)"`aplikaci: . Pokud `BundleId` tedy `com.yourcompany.xforms`ano , je `msauth.com.yourcompany.xforms`schéma adresy URL .

> [!NOTE]
> Toto schéma adres URL se stane součástí identifikátoru URI přesměrování, který jednoznačně identifikuje vaši aplikaci, když obdrží odpověď od zprostředkovatele.

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

MSAL `–canOpenURL:` používá ke kontrole, zda je v zařízení nainstalován zprostředkovatel. V iOS 9 Apple uzamkl schémata, na která se aplikace může dotazovat. 

Přidejte `msauthv2` `LSApplicationQueriesSchemes` do části `Info.plist` souboru, jako v následujícím příkladu:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Krok 7: Registrace identifikátoru URI přesměrování na portálu aplikace

Při použití zprostředkovatele, vaše přesměrování URI má další požadavek. Identifikátor URI přesměrování _musí_ mít následující formát:

```csharp
$"msauth.{BundleId}://auth"
```

Tady je příklad:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```

Všimněte si, že `CFBundleURLSchemes` identifikátor URI přesměrování `Info.plist` odpovídá názvu, který jste zahrnuli do souboru.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Krok 8: Ujistěte se, že identifikátor URI přesměrování je registrovaný ve vaší aplikaci.

Identifikátor URI přesměrování musí být zaregistrován na [portálu pro registraci aplikace](https://portal.azure.com) jako platný identifikátor URI přesměrování pro vaši aplikaci. 

Portál registrace aplikací poskytuje nové prostředí, které vám pomůže vypočítat zprostředkované odpovědi URI z ID balíčku. 

Výpočet identifikátoru URI přesměrování:

1. Na portálu pro registraci aplikací zvolte **Ověřování** > **Vyzkoušejte nové prostředí**.

   ![Vyzkoušejte si nové prostředí pro registraci aplikací](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Vyberte **Přidat platformu**.

   ![Přidání platformy](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Když je seznam platforem podporován, vyberte **iOS**.

   ![Konfigurace iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Podle potřeby zadejte ID sady a pak vyberte **Konfigurovat**.

   ![Zadejte ID sady.](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Po dokončení kroků se vypočítá identifikátor URI přesměrování za vás.

![Kopírovat identifikátor URI přesměrování](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Zprostředkované ověřování pro Android

### <a name="step-1-enable-broker-support"></a>Krok 1: Povolit podporu zprostředkovatele

Podpora brokera je povolena na základě aplikace pro veřejné klienty. Ve výchozím nastavení je zakázán. Při `WithBroker()` vytváření `IPublicClientApplication` rozhraní použijte parametr (ve výchozím `PublicClientApplicationBuilder`nastavení na hodnotu true)

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Krok 2: Aktualizace appdelegate pro zpracování zpětného volání

Když MSAL.NET volá zprostředkovatele, zprostředkovatel bude zase volat zpět do vaší aplikace s OnActivityResult() metoda. Vzhledem k tomu, že MSAL bude čekat na odpověď od zprostředkovatele, vaše aplikace musí směrovat výsledek MSAL.NET.
Toho lze dosáhnout směrováním výsledku `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` do metody přepsáním metody OnActivityResult(), jak je znázorněno níže

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Tato metoda je vyvolána při každém spuštění aplikace zprostředkovatele a používá se jako příležitost ke zpracování odpovědi od zprostředkovatele a dokončení procesu ověřování zahájeného MSAL.NET.

### <a name="step-3-set-an-activity"></a>Krok 3: Nastavení aktivity

Aby zprostředkované ověřování fungovalo, budete muset nastavit aktivitu tak, aby msal můžete odeslat a přijmout odpověď od brokera.

Chcete-li to provést, budete muset zadat aktivitu (obvykle `WithParentActivityOrWindow(object parent)` MainActivity) jako nadřazený objekt. 

**Například:**

Ve volání získat token:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>Krok 4: Registrace redirectUri na aplikačním portálu

MSAL používá adresy URL k vyvolání zprostředkovatele a pak se vrátit zpět do aplikace. Chcete-li dokončit tuto zpáteční cestu, musíte pro svou aplikaci zaregistrovat schéma adres URL. Tento identifikátor URI přesměrování musí být registrován na registračním portálu aplikace Azure AD jako platný identifikátor URI přesměrování pro vaši aplikaci.


Identifikátor URI přesměrování potřebný pro vaši aplikaci závisí na certifikátu použitém k podepsání sady APK.

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

Poslední část identifikátoru `hgbUYHVBYUTvuvT&Y6tr554365466=`URI , je podpis, který je podepsán pomocí souboru APK, base64 kódován.
Však během fáze vývoje aplikace pomocí sady Visual Studio, pokud ladíte kód bez podpisu APK s konkrétním certifikátem, Visual Studio podepíše APK pro účely ladění, dává APK jedinečný podpis pro stroj, na který je postaven. Proto pokaždé, když vytvoříte aplikaci na jiném počítači, budete muset aktualizovat identifikátor URI přesměrování v kódu aplikace a registraci aplikace na portálu Azure, aby bylo možné ověřit pomocí MSAL. 

Při ladění může dojít k výjimce MSAL (nebo zprávy protokolu) oznamující, že zadaný identifikátor URI přesměrování je nesprávný. **Tato výjimka vám také poskytne identifikátor URI přesměrování, který byste měli používat** s aktuálním počítačem, na který ladíte. Tento identifikátor URI přesměrování můžete použít k tomu, abyste se prozatím dále vyvíjeli.

Jakmile budete připraveni dokončit svůj kód, nezapomeňte aktualizovat identifikátor URI přesměrování v kódu a na registraci aplikace na webu Azure Portal, abyste použili podpis certifikátu, se kterým budete podepisovat soubor APK.

V praxi to znamená, že je třeba zaregistrovat identifikátor URI přesměrování pro každého člena týmu a identifikátor URI přesměrování pro produkční podepsanou verzi souboru APK.

Tento podpis můžete také vypočítat sami, podobně jako to dělá msal: 

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

Máte také možnost získat podpis pro váš balíček pomocí keytool s následujícími příkazy:

Pro Windows:`keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

Pro Mac:`keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>Další kroky

Přečtěte si [informace o aspektech používání univerzální platformy Windows s MSAL.NET](msal-net-uwp-considerations.md).
