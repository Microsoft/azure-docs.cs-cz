---
title: Migrace aplikací pro Xamarin pomocí zprostředkovatelů do MSAL.NET
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak migrovat aplikace Xamarin pro iOS, které používají Microsoft Authenticator z ADAL.NET na MSAL.NET.
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
ms.openlocfilehash: de259daa7fd27cc4f138c294a7f347502ca482a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185829"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>Migrace aplikací pro iOS, které používají Microsoft Authenticator z ADAL.NET na MSAL.NET

Používáte Azure Active Directory Authentication Library pro .NET (ADAL.NET) a zprostředkovatele iOS. Nyní je čas migrovat do [knihovny Microsoft Authentication Library](msal-overview.md) pro .NET (MSAL.NET), která podporuje zprostředkovatele v systému iOS od verze 4.3. 

Kde byste měli začít? Tento článek vám pomůže migrovat aplikaci Xamarin iOS z ADAL do MSAL.

## <a name="prerequisites"></a>Požadavky
Tento článek předpokládá, že už máte aplikaci Xamarin pro iOS, která je integrovaná s makléřem iOS. Pokud tak nechcete, přesuňte se přímo na MSAL.NET a začněte zde provádět implementaci makléře. Informace o tom, jak vyvolat zprostředkovatele iOS v MSAL.NET s novou aplikací, naleznete v [této dokumentaci](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications).

## <a name="background"></a>Pozadí

### <a name="what-are-brokers"></a>Co jsou makléři?

Makléři jsou aplikace poskytované společností Microsoft v systémech Android a iOS. (Podívejte se na aplikaci [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) na iOS a Android a aplikaci Intune Company Portal na Androidu.) 

Umožňují:

- Jednotné přihlašování
- Identifikace zařízení, která je vyžadována některými [zásadami podmíněného přístupu](../conditional-access/overview.md). Další informace naleznete v [tématu Správa zařízení](../conditional-access/concept-conditional-access-conditions.md#device-platforms).
- Ověření identifikace aplikace, které je také vyžadováno v některých podnikových scénářích. Další informace najdete [v tématu Intune pro správu mobilních aplikací (MAM).](https://docs.microsoft.com/intune/mam-faq)

## <a name="migrate-from-adal-to-msal"></a>Migrace z ADAL do MSAL

### <a name="step-1-enable-the-broker"></a>Krok 1: Povolení zprostředkovatele

<table>
<tr><td>Aktuální ADAL kód:</td><td>Protějšek MSAL:</td></tr>
<tr><td>
V ADAL.NET byla povolena podpora zprostředkovatele na základě kontextu ověřování. Ve výchozím nastavení je zakázán. Museli jste nastavit 

`useBroker`příznak true v `PlatformParameters` konstruktoru volat makléře:

```csharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
Také v kódu specifickém pro platformu, v tomto příkladu, v rendereru stránky pro iOS nastavte`useBroker` 
příznak na true:
```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

Potom zahrňte parametry do volání tokenu získání:
```csharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource, 
                              ClientId, 
                              new Uri(RedirectURI), 
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
V MSAL.NET je povolena podpora zprostředkovatele na základě publicclientapplication. Ve výchozím nastavení je zakázán. Chcete-li jej povolit, použijte 

`WithBroker()`parametr (nastavenna na hodnotu true ve výchozím nastavení) za účelem volání makléře:

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
V volání tokenu získání:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Krok 2: Nastavení UIViewController()
V ADAL.NET jste předali v UIViewController jako součást `PlatformParameters`. (Viz příklad v kroku 1.) V MSAL.NET, aby vývojáři větší flexibilitu, okno objektu se používá, ale není vyžadováno v běžném používání iOS. Chcete-li použít zprostředkovatele, nastavte okno objektu, aby bylo možné odesílat a přijímat odpovědi od zprostředkovatele. 
<table>
<tr><td>Aktuální ADAL kód:</td><td>Protějšek MSAL:</td></tr>
<tr><td>
UIViewController je předán do 

`PlatformParameters`v platformě specifické pro iOS.

```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
V MSAL.NET můžete nastavit okno objektu pro iOS dvěma věcmi:

1. V `AppDelegate.cs`, `App.RootViewController` nastavte `UIViewController()`na nový . Toto přiřazení zajišťuje, že je UIViewController s voláním zprostředkovatele. Pokud není správně nastavena, může se zobrazí tato chyba:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. Na AcquireTokenInteractive volání, `.WithParentActivityOrWindow(App.RootViewController)`použijte , a předat v odkazu na okno objektu, který budete používat.

**Například:**

V `App.cs`:
```csharp
   public static object RootViewController { get; set; }
```
V `AppDelegate.cs`:
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
V volání tokenu získání:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Krok 3: Aktualizace appdelegate pro zpracování zpětného volání
ADAL a MSAL volání zprostředkovatele a zprostředkovatele zase volá `OpenUrl` zpět do `AppDelegate` vaší aplikace prostřednictvím metody třídy. Další informace naleznete v [této dokumentaci](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback).

Mezi ADAL.NET a MSAL.NET zde nejsou žádné změny.

### <a name="step-4-register-a-url-scheme"></a>Krok 4: Registrace schématu adres URL
ADAL.NET a MSAL.NET pomocí adres URL vyvolat makléře a vrátit odpověď zprostředkovatele zpět do aplikace. Schéma adres URL `Info.plist` v souboru aplikace zaregistrujte následujícím způsobem:

<table>
<tr><td>Aktuální ADAL kód:</td><td>Protějšek MSAL:</td></tr>
<tr><td>
Schéma adres URL je jedinečné pro vaši aplikaci.
</td><td>
Prostředek 

`CFBundleURLSchemes`název musí obsahovat 

`msauth.`

jako předponu, následovanou`CFBundleURLName`

Příklad: `$"msauth.(BundleId")`

```csharp
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

> [!NOTE]
> Toto schéma adres URL se stane součástí identifikátoru URI přesměrování, který se používá k jednoznačné identifikaci aplikace, když obdrží odpověď od zprostředkovatele.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Krok 5: Přidání identifikátoru zprostředkovatele do oddílu LSApplicationQueriesSchemes

ADAL.NET a MSAL.NET `-canOpenURL:` oba použít ke kontrole, zda je v zařízení nainstalován zprostředkovatel. Přidejte správný identifikátor pro zprostředkovatele iOS do oddílu LSApplicationQueriesSchemes v souboru info.plist následujícím způsobem:

<table>
<tr><td>Aktuální ADAL kód:</td><td>Protějšek MSAL:</td></tr>
<tr><td>
Použití 

`msauth`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Použití 

`msauthv2`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-portal"></a>Krok 6: Registrace identifikátoru URI přesměrování na portálu

ADAL.NET i MSAL.NET přidávají další požadavek na identifikátor URI přesměrování, když cílí na zprostředkovatele. Zaregistrujte identifikátor URI přesměrování u aplikace na portálu.
<table>
<tr><td>Aktuální ADAL kód:</td><td>Protějšek MSAL:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

Příklad: 

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Příklad:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Další informace o tom, jak zaregistrovat identifikátor URI přesměrování na portálu, naleznete v [tématu Využití zprostředkovatele v aplikacích Xamarin.iOS](msal-net-use-brokers-with-xamarin-apps.md#step-8-make-sure-the-redirect-uri-is-registered-with-your-app).

## <a name="next-steps"></a>Další kroky

Další informace o [aspektech specifických pro Xamarin pro iOS s MSAL.NET](msal-net-xamarin-ios-considerations.md). 
