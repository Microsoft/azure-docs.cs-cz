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
ms.openlocfilehash: 25b8aa9b5e80720e9543dafce7970404a62b7d1f
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132643"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Použití Microsoft Authenticator nebo Portál společnosti Intune v aplikacích Xamarin

V zařízeních s Androidem a iOS poskytují zprostředkovatelé jako Microsoft Authenticator a Microsoft Intune Portál společnosti specifické pro Android:

- **Jednotné přihlašování (SSO)** : uživatelé se nemusí přihlašovat ke každé aplikaci.
- **Identifikace zařízení**: Zprostředkovatel přistupuje k certifikátu zařízení. Tento certifikát se vytvoří na zařízení, když je připojený k pracovišti.
- **Ověření identifikace aplikace**: když aplikace volá zprostředkovatele, PŘEDÁ adresu URL pro přesměrování. Zprostředkovatel ověří adresu URL.

Chcete-li povolit jednu z těchto funkcí, použijte při volání metody `PublicClientApplicationBuilder.CreateApplication` parametr `WithBroker()`. Ve výchozím nastavení je parametr `.WithBroker()` nastaven na hodnotu true. 

Pomocí pokynů v následujících částech můžete nastavit zprostředkované ověřování pro aplikace [iOS](#brokered-authentication-for-ios) nebo aplikace pro [Android](#brokered-authentication-for-android) .

## <a name="brokered-authentication-for-ios"></a>Zprostředkované ověřování pro iOS

Pomocí následujících kroků můžete aplikaci Xamarin. iOS povolit komunikaci s aplikací [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

### <a name="step-1-enable-broker-support"></a>Krok 1: povolení podpory zprostředkovatele
Je nutné povolit podporu zprostředkovatele pro jednotlivé instance `PublicClientApplication`. Ve výchozím nastavení je podpora zakázaná. Při vytváření `PublicClientApplication` prostřednictvím `PublicClientApplicationBuilder`použijte parametr `WithBroker()`, jak ukazuje následující příklad. Ve výchozím nastavení je parametr `WithBroker()` nastaven na hodnotu true.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Krok 2: povolení přístupu k řetězci klíčů

Pokud chcete povolit přístup pomocí řetězce klíčů, musíte mít přístupovou skupinu pro řetězce klíčů pro vaši aplikaci. Pomocí rozhraní `WithIosKeychainSecurityGroup()` API můžete nastavit přístupovou skupinu pro řetězce klíčů při vytváření aplikace:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Další informace najdete v tématu [Povolení přístupu k řetězci klíčů](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Krok 3: aktualizace AppDelegate pro zpracování zpětného volání
Když Microsoft Authentication Library pro .NET (MSAL.NET) volá zprostředkovatele, zprostředkovatel volá zpátky do vaší aplikace prostřednictvím metody `OpenUrl` třídy `AppDelegate`. Vzhledem k tomu, že MSAL čeká na odpověď zprostředkovatele, musí vaše aplikace spolupracovat a volat MSAL.NET zpátky. Pokud chcete tuto spolupráci povolit, aktualizujte soubor `AppDelegate.cs`, abyste mohli přepsat následující metodu.

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
Pořád v souboru `AppDelegate.cs` musíte nastavit okno objektu. Pro Xamarin iOS obvykle není nutné nastavovat okno objektu. K posílání a přijímání odpovědí od zprostředkovatele ale potřebujete okno objektu. 

Nastavení okna objektu: 
1. V souboru `AppDelegate.cs` nastavte `App.RootViewController` na novou `UIViewController()`. Toto přiřazení zajišťuje, že volání služby Broker zahrnuje `UIViewController`. Pokud je toto nastavení nesprávně přiřazeno, může se zobrazit tato chyba:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. Ve `AcquireTokenInteractive` volání použijte `.WithParentActivityOrWindow(App.RootViewController)` a pak předejte odkaz na okno objektu, které budete používat.

    V `App.cs`:
    
    ```csharp
       public static object RootViewController { get; set; }
    ```
    
    V `AppDelegate.cs`:
    
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    
    Ve `AcquireToken` volání:
    
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>Krok 5: registrace schématu adresy URL
MSAL.NET používá adresy URL k vyvolání zprostředkovatele a pak vrátí odpověď zprostředkovatele do vaší aplikace. Pokud chcete dokončit zpáteční cestu, zaregistrujte do souboru `Info.plist` schéma URL pro vaši aplikaci.

Název `CFBundleURLSchemes` musí zahrnovat `msauth.` jako předponu. Použijte předponu `CFBundleURLName`. 

V rámci schématu adresy URL `BundleId` jedinečnou identifikaci aplikace: `$"msauth.(BundleId)"`. Takže pokud je `BundleId` `com.yourcompany.xforms`, je schéma adresy URL `msauth.com.yourcompany.xforms`.

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
MSAL používá `–canOpenURL:` ke kontrole, jestli je zprostředkovatel nainstalovaný na zařízení. V systému iOS 9 byla společnost Apple uzamčena schématy, pro které se aplikace může dotazovat. 

Přidejte `msauthv2` do části `LSApplicationQueriesSchemes` souboru `Info.plist`, jak je uvedeno v následujícím příkladu:

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
Všimněte si, že identifikátor URI přesměrování odpovídá názvu `CFBundleURLSchemes`, který jste zahrnuli do souboru `Info.plist`.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Krok 8: Ujistěte se, že je identifikátor URI přesměrování zaregistrován u vaší aplikace.

Identifikátor URI pro přesměrování musí být zaregistrován na [portálu pro registraci aplikací](https://portal.azure.com) jako platný identifikátor URI přesměrování pro vaši aplikaci. 

Portál pro registraci aplikací nabízí nové prostředí, které vám pomůžou vypočítat identifikátor URI zprostředkované odpovědi z ID sady prostředků. 

Výpočet identifikátoru URI přesměrování:

1. Na portálu pro registraci aplikací vyberte **ověřování** > **Vyzkoušejte nové prostředí**.

   ![Vyzkoušejte si nové prostředí pro registraci aplikací](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Vyberte **Přidat platformu**.

   ![Přidat platformu](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Až se seznam platforem podporuje, vyberte **iOS**.

   ![Konfigurace iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Zadejte ID sady prostředků podle požadavků a pak vyberte **Konfigurovat**.

   ![Zadejte ID sady prostředků.](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Po dokončení tohoto postupu se identifikátor URI přesměrování vypočítává za vás.

![Kopírovat identifikátor URI pro přesměrování](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Zprostředkované ověřování pro Android

MSAL.NET podporuje jenom platformu Xamarin. iOS. Ještě nepodporuje zprostředkovatele pro platformu Xamarin. Android.

Nativní knihovna MSAL Android již podporuje zprostředkované ověřování. Další informace najdete v tématu [zprostředkovatelské ověřování v Androidu](brokered-auth.md).

## <a name="next-steps"></a>Další kroky

Seznamte [se s důležitými informacemi o použití Univerzální platforma Windows s MSAL.NET](msal-net-uwp-considerations.md).
