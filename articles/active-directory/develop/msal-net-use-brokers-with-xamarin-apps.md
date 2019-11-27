---
title: Použití Microsoft Authenticator nebo Microsoft Intune Portál společnosti v aplikacích pro Xamarin iOS a Android
titleSuffix: Microsoft identity platform
description: Naučte se migrovat aplikace Xamarin iOS, které můžou používat Microsoft Authenticator z knihovny ověřování Azure AD pro .NET (ADAL.NET) do knihovny Microsoft Authentication Library pro .NET (MSAL.NET).
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
ms.openlocfilehash: bd144f1922456520075c1863ec347e85eb3889db
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74379862"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>Použití Microsoft Authenticator nebo Microsoft Intune Portál společnosti v aplikacích Xamarin

V Androidu a iOS se zprostředkovatelé, jako je Microsoft Authenticator nebo Microsoft Intune Portál společnosti povolit (jenom Android):

- Jednotné přihlašování (SSO). Uživatelé se nebudou muset přihlašovat ke každé aplikaci.
- Identifikace zařízení. Zprostředkovatel přistupuje k certifikátu zařízení, který byl vytvořen v zařízení, když byl připojen k síti na pracovišti.
- Ověření identifikace aplikace Když aplikace volá zprostředkovatele, předá adresu URL pro přesměrování a zprostředkovatel ji ověří.

Chcete-li povolit jednu z těchto funkcí, vývojáři aplikací musí při volání metody `PublicClientApplicationBuilder.CreateApplication` použít parametr `WithBroker()`. ve výchozím nastavení je `.WithBroker()` nastaveno na hodnotu true. Vývojáři také musí postupovat podle pokynů pro aplikace pro [iOS](#brokered-authentication-for-ios) nebo [Android](#brokered-authentication-for-android) .

## <a name="brokered-authentication-for-ios"></a>Zprostředkované ověřování pro iOS

Pomocí těchto kroků umožníte aplikaci Xamarin. iOS mluvit s aplikací [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) .

### <a name="step-1-enable-broker-support"></a>Krok 1: povolení podpory zprostředkovatele
Podpora zprostředkovatele je povolená na základě PublicClientApplication. Ve výchozím nastavení je zakázaný. Při vytváření PublicClientApplication prostřednictvím PublicClientApplicationBuilder použijte parametr `WithBroker()` (ve výchozím nastavení nastavenou na hodnotu true).

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Krok 2: aktualizace AppDelegate pro zpracování zpětného volání
Když knihovna Microsoft Authentication Library pro .NET (MSAL.NET) volá zprostředkovatele, zprostředkovatel zase volá zpět do vaší aplikace prostřednictvím metody `OpenUrl` třídy `AppDelegate`. Vzhledem k tomu, že MSAL čeká na odpověď od služby Broker, musí vaše aplikace spolupracovat a volat MSAL.NET zpátky. Pokud chcete tuto spolupráci povolit, aktualizujte soubor `AppDelegate.cs`, abyste mohli přepsat následující metodu.

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

Tato metoda je vyvolána při každém spuštění aplikace. Používá se jako příležitost ke zpracování odpovědi od služby Broker a dokončení procesu ověřování iniciované nástrojem MSAL.NET.

### <a name="step-3-set-a-uiviewcontroller"></a>Krok 3: nastavení UIViewController ()
Pořád v `AppDelegate.cs`musíte nastavit okno objektu. V případě Xamarin iOS nemusíte nastavovat okno objektu. K odesílání a přijímání odpovědí od služby Broker budete potřebovat okno objektu. 

Provedete to tak, že provedete dvě věci. 
1. V `AppDelegate.cs`nastavte `App.RootViewController` na nové `UIViewController()`. Toto přiřazení zajišťuje, že existuje UIViewController se voláním zprostředkovatele. Pokud není správně nastavená, může se zobrazit tato chyba: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. V volání AcquireTokenInteractive použijte `.WithParentActivityOrWindow(App.RootViewController)` a předejte odkaz na okno objektu, které použijete.

**Například:**

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

### <a name="step-4-register-a-url-scheme"></a>Krok 4: registrace schématu adresy URL
MSAL.NET používá adresy URL k vyvolání zprostředkovatele a pak vrátí odpověď zprostředkovatele zpátky do vaší aplikace. Pokud chcete dokončit zpáteční cestu, zaregistrujte do souboru `Info.plist` schéma URL pro vaši aplikaci.

Název `CFBundleURLSchemes` musí zahrnovat `msauth.` jako předponu, za kterou následuje `CFBundleURLName`.

`$"msauth.(BundleId)"`

**Například:**

`msauth.com.yourcompany.xforms`

> [!NOTE]
> Toto schéma adresy URL se označuje jako součást identifikátoru URI přesměrování, který slouží k jednoznačné identifikaci vaší aplikace, když obdrží odpověď od služby Broker.

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

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Krok 5: Přidání identifikátoru zprostředkovatele do oddílu LSApplicationQueriesSchemes
MSAL používá `–canOpenURL:` ke kontrole, jestli je zprostředkovatel nainstalovaný na zařízení. V systému iOS 9 společnost Apple zablokoval, ke kterým schématům se může aplikace dotazovat. 

Přidejte `msauthv2` do části `LSApplicationQueriesSchemes` souboru `Info.plist`.

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="step-6-register-your-redirect-uri-in-the-application-portal"></a>Krok 6: registrace identifikátoru URI přesměrování na portálu aplikací
Použití zprostředkovatele služby přidá dodatečný požadavek na identifikátor URI přesměrování. Identifikátor URI pro přesměrování _musí_ mít následující formát:
```CSharp
$"msauth.{BundleId}://auth"
```
**Například:**
```CSharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
Všimněte si, že identifikátor URI přesměrování odpovídá názvu `CFBundleURLSchemes`, který jste zahrnuli do souboru `Info.plist`.

### <a name="step-7-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Krok 7: Ujistěte se, že je identifikátor URI přesměrování zaregistrován u vaší aplikace.

Tento identifikátor URI pro přesměrování musí být zaregistrován na portálu pro registraci aplikací (https://portal.azure.com) jako platný identifikátor URI přesměrování pro vaši aplikaci. 

Portál má nový portál pro registraci aplikací App Experience, který vám umožní vypočítat identifikátor URI zprostředkované odpovědi z ID sady prostředků.

1. V registraci aplikace zvolte **ověřování** a vyberte **vyzkoušet nové prostředí**.

   ![Vyzkoušejte si nové prostředí pro registraci aplikací](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Vyberte **Přidat platformu**.

   ![Přidat platformu](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Až se seznam platforem podporuje, vyberte **iOS**.

   ![Konfigurace iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Zadejte ID sady prostředků podle požadavků a pak vyberte **Konfigurovat**.

   ![Zadejte ID sady prostředků.](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

1. Identifikátor URI přesměrování je vypočítán za vás.

   ![Kopírovat identifikátor URI pro přesměrování](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Zprostředkované ověřování pro Android

MSAL.NET v tuto chvíli podporuje jenom platformu Xamarin. iOS. Ještě nepodporuje zprostředkovatele pro platformu Xamarin. Android.

Nativní knihovna MSAL Android ji již podporuje. Podrobnosti najdete v tématu zprostředkované [ověřování v Androidu](https://docs.microsoft.com/azure/active-directory/develop/brokered-auth.md) .

## <a name="next-steps"></a>Další kroky

Přečtěte si informace o [Univerzální platforma Windows specifických pro použití s MSAL.NET](msal-net-uwp-considerations.md).