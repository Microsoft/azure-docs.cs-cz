---
title: Použití Microsoft Authenticator nebo Microsoft Intune portálu společnosti v aplikacích Xamarin iOS a Android | Azure
description: Přečtěte si, jak migrovat aplikace Xamarin iOS pomocí Microsoft Authenticator z knihovny ověřování Azure AD pro .NET (ADAL.NET) do knihovny Microsoft Authentication Library pro .NET (MSAL.NET).
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e16ad801b3b691d942c2ba0dc723ba72e24cf4c0
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70875637"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>Použití Microsoft Authenticator nebo Microsoft Intune portálu společnosti v aplikacích Xamarin

V zařízeních s Androidem a iOS, zprostředkovatelé, jako je Microsoft Authenticator nebo Microsoft Intune povolení portálu společnosti (jenom Android):

- Jednotné přihlašování (SSO). Uživatelé se nebudou muset přihlašovat ke každé aplikaci.
- Identifikace zařízení. Přístupem k certifikátu zařízení vytvořenému v zařízení, když se připojil k síti na pracovišti.
- Ověření identifikace aplikace Když aplikace volá zprostředkovatele, předá adresu URL pro přesměrování a zprostředkovatel ji ověří.

Chcete-li povolit jednu z těchto funkcí, vývojáři aplikací musí při `WithBroker()` `PublicClientApplicationBuilder.CreateApplication` volání metody použít parametr. `.WithBroker()`ve výchozím nastavení je nastaveno na hodnotu true. Vývojáři budou muset také postupovat podle následujících kroků pro aplikace pro [iOS](#brokered-authentication-for-ios) nebo [Android](#brokered-authentication-for-android) .

## <a name="brokered-authentication-for-ios"></a>Zprostředkované ověřování pro iOS

Použijte následující postup, chcete-li aplikaci Xamarin. iOS povolit komunikaci s aplikací [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

### <a name="step-1-enable-broker-support"></a>Krok 1: Povolit podporu zprostředkovatele
Podpora zprostředkovatele je povolená na základě PublicClientApplication. Ve výchozím nastavení je zakázaný. Při vytváření PublicClientApplication prostřednictvím PublicClientApplicationBuilder použijte parametr(vevýchozímnastavenínastavenounahodnotutrue).`WithBroker()`

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Krok 2: Aktualizace AppDelegate pro zpracování zpětného volání
Když MSAL.NET volá zprostředkovatele, zprostředkovatel pak vrátí zpět do vaší aplikace prostřednictvím `OpenUrl` metody `AppDelegate` třídy. Vzhledem k tomu, že MSAL bude čekat na odpověď od služby Broker, musí vaše aplikace spolupracovat a volat MSAL.NET zpátky. Pokud chcete tuto spolupráci povolit, aktualizujte `AppDelegate.cs` soubor, aby se přepsala níže uvedená metoda.

```CSharp
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

Tato metoda je vyvolána pokaždé, když se aplikace spustí a používá se jako příležitost pro zpracování odpovědi od služby Broker a dokončení procesu ověřování iniciované nástrojem MSAL.NET.

### <a name="step-3-set-a-uiviewcontroller"></a>Krok 3: Nastavit UIViewController ()
Pořád v `AppDelegate.cs`, budete muset nastavit okno objektu. V případě Xamarin iOS nemusíte nastavovat okno objektu, ale odesílat a přijímat odpovědi od zprostředkovatele, budete potřebovat okno objektu. 

K tomu je potřeba udělat dvě věci. 
1) V `AppDelegate.cs`portálu `UIViewController()`nastavte na nový. `App.RootViewController` U tohoto přiřazení se ujistěte, že existuje UIViewController se voláním zprostředkovatele. Pokud není správně nastavená, může se zobrazit tato chyba:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) V volání AcquireTokenInteractive použijte `.WithParentActivityOrWindow(App.RootViewController)` příkaz a předejte odkaz na okno objektu, které použijete.

**Příklad:**

V `App.cs`:
```CSharp
   public static object RootViewController { get; set; }
```
V `AppDelegate.cs`:
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
V volání metody získání tokenu:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

### <a name="step-4-register-a-url-scheme"></a>Krok 4: Registrace schématu adresy URL
MSAL.NET používá adresy URL k vyvolání zprostředkovatele a pak vrátí odpověď zprostředkovatele zpátky do vaší aplikace. Pokud chcete dokončit zpáteční cestu, musíte v `Info.plist` souboru zaregistrovat schéma URL pro vaši aplikaci.

Název musí `msauth.` obsahovat`CFBundleURLName`předponu, za kterou následuje. `CFBundleURLSchemes`

`$"msauth.(BundleId)"`

**Například:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Tím se stane část RedirectUri, která se používá k jednoznačné identifikaci vaší aplikace při přijímání odpovědi od zprostředkovatele.

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

### <a name="step-5-lsapplicationqueriesschemes"></a>Krok 5: LSApplicationQueriesSchemes
MSAL používá `–canOpenURL:` ke kontrole, jestli je na zařízení nainstalovaný zprostředkovatel. V systému iOS 9 společnost Apple zablokoval, ke kterým schématům se může aplikace dotazovat. 

**Přidat** **`msauthv2`** do`LSApplicationQueriesSchemes` části souboru`Info.plist` .

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="step-6-register-your-redirecturi-in-the-application-portal"></a>Krok 6: Registrace RedirectUri na portálu aplikací
Pomocí zprostředkovatele se do redirectUri přidá dodatečný požadavek. RedirectUri _**musí**_ mít následující formát:
```CSharp
$"msauth.{BundleId}://auth"
```
**Příklad:**
```CSharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
**Všimněte si, že RedirectUri odpovídá `CFBundleURLSchemes` názvu, který jste zahrnuli `Info.plist` do souboru.**

### <a name="step-7-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Krok 7: Ujistěte se, že je identifikátor URI přesměrování zaregistrován u vaší aplikace.

Tento identifikátor URI pro přesměrování musí být zaregistrován na portálu pro registraci aplikací https://portal.azure.com) (jako platný identifikátor URI přesměrování pro vaši aplikaci. 

Portál má nový portál pro registraci aplikací App Experience, který vám umožní vypočítat identifikátor URI zprostředkované odpovědi z ID sady prostředků:

1. V registraci aplikace zvolte možnost **ověřování** a vyberte možnost **vyzkoušet si nové prostředí**.
   ![Vyzkoušejte si nové prostředí pro registraci aplikací](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Vyberte **Přidat platformu**.
   ![Přidat platformu](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Až se seznam platforem podporuje, vyberte **iOS**.
   ![Konfigurace iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Zadejte ID sady prostředků podle požadavků a potom stiskněte **Registrovat**.
   ![Zadejte ID sady prostředků.](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. Identifikátor URI přesměrování je vypočítán za vás.
   ![Kopírovat identifikátor URI pro přesměrování](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Zprostředkované ověřování pro Android

Podpora zprostředkovatele není pro Android k dispozici.

## <a name="next-steps"></a>Další kroky

[MSAL.NET s ohledem na konkrétní Univerzální platforma Windows](msal-net-uwp-considerations.md)