---
title: Migrace aplikací pro Xamarin iOS pomocí Microsoft Authenticator od ADAL.NET do MSAL.NET | Azure
description: Naučte se migrovat aplikace Xamarin iOS pomocí Microsoft Authenticator z knihovny ověřování Azure AD pro .NET (ADAL.NET) do knihovny Microsoft Authentication Library pro .NET (MSAL.NET).
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
ms.openlocfilehash: 609aeeada78985466cb7c254cab32864e5d84b0e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70875650"
---
# <a name="migrating-ios-applications-using-microsoft-authenticator-from-adalnet-to-msalnet"></a>Migrace aplikací pro iOS pomocí Microsoft Authenticator z ADAL.NET na MSAL.NET

Používali jste ADAL.NET a poskytovatele iOS a je čas migrovat na MSAL.NET [Microsoft Authentication Library](msal-overview.md), která podporuje zprostředkovatele v systému iOS od verze 4,3 a vyšší. 

Kde začít? Tento článek vám pomůže s migrací aplikace pro Xamarin iOS z knihovny ADAL do MSAL.

## <a name="prerequisites"></a>Požadavky
V tomto dokumentu se předpokládá, že už máte aplikaci Xamarin iOS, která je integrovaná s zprostředkovatelem iOS. Pokud to neuděláte, je nejlepší přejít přímo na MSAL.NET a spustit implementaci zprostředkovatele tam. Podrobnosti o vyvolání zprostředkovatele iOS v MSAL.NET s novou aplikací najdete v [této dokumentaci](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications) .

## <a name="background"></a>Pozadí

### <a name="what-are-brokers"></a>Co jsou zprostředkovatelé?

Zprostředkovatelé jsou aplikace poskytované Microsoftem v Androidu a iOS ([Microsoft Authenticator](https://www.microsoft.com/account/authenticator) v systémech iOS a Android portál společnosti Intune na Androidu). 

Umožňují:

- Jednotné přihlašování,
- Identifikace zařízení, kterou vyžadují některé [zásady podmíněného přístupu](../conditional-access/overview.md) (viz [Správa zařízení](../conditional-access/conditions.md#device-platforms))
- Ověřování totožnosti aplikace, které se také vyžaduje v některých podnikových scénářích (viz téma [Správa mobilních aplikací Intune nebo mam](https://docs.microsoft.com/intune/mam-faq))

## <a name="migrate-from-adal-to-msal"></a>Migrace z ADAL na MSAL

### <a name="step-1-enable-the-broker"></a>Krok 1: Povolit zprostředkovatele

<table>
<tr><td>Aktuální kód ADAL:</td><td>MSAL protějšek:</td></tr>
<tr><td>
V ADAL.NET je podpora zprostředkovatele povolená u kontextu pro ověřování, ale ve výchozím nastavení je zakázaná. Museli jste nastavit `useBroker` příznak na hodnotu true `PlatformParameters` v konstruktoru pro volání zprostředkovatele:

```CSharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
V kódu specifickém pro platformu v tomto příkladu můžete v nástroji pro vykreslování stránky pro iOS nastavit`useBroker` 
Příznak na hodnotu true:
```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

Pak zahrňte parametry v volání metody získání tokenu:
```CSharp
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
V MSAL.NET je podpora zprostředkovatele povolená u jednotlivých veřejných klientských aplikací. Ve výchozím nastavení je zakázaná. Pokud ho chcete povolit, použijte: 

`WithBroker()`parametr (ve výchozím nastavení nastaven na hodnotu true), aby zprostředkovatel volal:

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
V volání metody získání tokenu:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Krok 2: Nastavit UIViewController ()
V ADAL.NET jste předali v UIViewController jako součást PlatformParameters (viz příklad v kroku 1). V MSAL.NET ale, aby vývojář poskytoval větší flexibilitu, se používá okno objektu, ale v běžném použití iOS se nevyžaduje. Chcete-li však použít zprostředkovatele, budete muset nastavit okno objektu, aby bylo možné odesílat a přijímat odpovědi od zprostředkovatele. 
<table>
<tr><td>Aktuální kód ADAL:</td><td>MSAL protějšek:</td></tr>
<tr><td>
UIViewController se předává do PlatformParamters na platformě specifické pro iOS.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
V MSAL.NET musíte provést dvě věci, abyste mohli nastavit okno objektu pro iOS:

1) V `AppDelegate.cs`portálu `UIViewController()`nastavte na nový. `App.RootViewController` Toto přiřazení zajistí, že bude UIViewController s voláním zprostředkovatele. Pokud není správně nastavená, může se zobrazit tato chyba:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) Na AcquireTokenInteractive volání použijte`.WithParentActivityOrWindow(App.RootViewController)`
a předejte odkaz na okno objektu you'will use.

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

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Krok 3: Aktualizace AppDelegate pro zpracování zpětného volání
ADAL i MSAL budou volat zprostředkovatele a zprostředkovatel pak bude volat zpět do vaší aplikace prostřednictvím `OpenUrl` metody `AppDelegate` třídy. Další informace najdete [tady](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS/_edit#step-two-update-appdelegate-to-handle-the-callback) .

: heavy_check_mark:**mezi ADAL.NET a MSAL.NET nejsou žádné změny** .

### <a name="step-4-register-a-url-scheme"></a>Krok 4: Registrace schématu adresy URL
ADAL.NET a MSAL.NET používají adresy URL k vyvolání zprostředkovatele a vrátí odpověď zprostředkovatele zpět do aplikace. Zaregistrujte schéma URL v `Info.plist` souboru pro vaši aplikaci následujícím způsobem:

<table>
<tr><td>Aktuální kód ADAL:</td><td>MSAL protějšek:</td></tr>
<tr><td>
Schéma adresy URL je pro vaši aplikaci jedinečné.
</td><td>
Rozhraní 

`CFBundleURLSchemes`Název musí zahrnovat 

`msauth.`

jako předponu, za kterou následuje vaše`CFBundleURLName`

Příklad: `$"msauth.(BundleId")`

```CSharp
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
>  Toto schéma URL se stane součástí RedirectUri, který slouží k jednoznačné identifikaci aplikace při přijetí odpovědi od zprostředkovatele.

</table>

### <a name="step-5-lsapplicationqueriesschemes"></a>Krok 5: LSApplicationQueriesSchemes

ADAL.NET a MSAL.NET oba používají `-canOpenURL:` ke kontrole, jestli je zprostředkovatel nainstalovaný na zařízení. Přidejte do části LSApplicationQueriesSchemes souboru info. plist správný identifikátor pro zprostředkovatele iOS následujícím způsobem: 
<table>
<tr><td>Aktuální kód ADAL:</td><td>MSAL protějšek:</td></tr>
<tr><td>
Využití 

`msauth`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Využití 

`msauthv2`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
</array>
```
</table>

### <a name="step-6-register-you-redirecturi-in-the-portal"></a>Krok 6: Zaregistrujte se RedirectUri na portálu.

ADAL.NET a MSAL.NET při cílení na zprostředkovatele přidávají další požadavky na redirectUri. Zaregistrujte identifikátor URI přesměrování do vaší aplikace na portálu.
<table>
<tr><td>Aktuální kód ADAL:</td><td>MSAL protějšek:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`případě`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Příklad:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Další informace o registraci redirectUri na portálu najdete v tématu [využití zprostředkovatele v aplikacích Xamarin. iOS](msal-net-use-brokers-with-xamarin-apps.md#step-7-make-sure-the-redirect-uri-is-registered-with-your-app) , kde najdete další podrobnosti.

## <a name="next-steps"></a>Další postup

Přečtěte si o [otázkách specifických pro Xamarin iOS pomocí MSAL.NET](msal-net-xamarin-ios-considerations.md). 
