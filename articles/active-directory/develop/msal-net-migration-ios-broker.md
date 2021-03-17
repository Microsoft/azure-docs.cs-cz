---
title: Migrace aplikací pro Xamarin pomocí zprostředkovatelů do MSAL.NET
titleSuffix: Microsoft identity platform
description: Naučte se migrovat aplikace pro Xamarin iOS, které používají Microsoft Authenticator od ADAL.NET do MSAL.NET.
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
ms.openlocfilehash: b4eff5910ff5230902d497b55b2afbe6d605365a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89177427"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>Migrace aplikací pro iOS, které používají Microsoft Authenticator z ADAL.NET na MSAL.NET

Používali jste knihovnu Azure Active Directory Authentication Library pro .NET (ADAL.NET) a zprostředkovatele iOS. Nyní je čas migrovat do [knihovny Microsoft Authentication Library](msal-overview.md) pro .net (MSAL.NET), která podporuje zprostředkovatele v systému iOS z verze 4,3.

Kde byste měli začít? Tento článek vám pomůže s migrací aplikace pro Xamarin iOS z ADAL do MSAL.

## <a name="prerequisites"></a>Požadavky
V tomto článku se předpokládá, že už máte aplikaci Xamarin iOS integrovanou se zprostředkovatelem iOS. Pokud to neuděláte, přejděte přímo na MSAL.NET a spusťte implementaci zprostředkovatele tam. Informace o tom, jak vyvolat zprostředkovatele iOS v MSAL.NET pomocí nové aplikace, najdete v [této dokumentaci](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications).

## <a name="background"></a>Pozadí

### <a name="what-are-brokers"></a>Co jsou zprostředkovatelé?

Zprostředkovatelé jsou aplikace poskytované Microsoftem v Androidu a iOS. (Podívejte se na [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) aplikaci v systémech iOS a Android a v aplikaci Portál společnosti Intune na Androidu.)

Umožňují:

- Jednotné přihlašování
- Identifikace zařízení, která je vyžadována některými [zásadami podmíněného přístupu](../conditional-access/overview.md). Další informace najdete v tématu [Správa zařízení](../conditional-access/concept-conditional-access-conditions.md#device-platforms).
- Ověřování totožnosti aplikace, které je také vyžadováno v některých podnikových scénářích. Další informace najdete v tématu [Správa mobilních aplikací (MAM) v Intune](/intune/mam-faq).

## <a name="migrate-from-adal-to-msal"></a>Migrace z ADAL na MSAL

### <a name="step-1-enable-the-broker"></a>Krok 1: povolení zprostředkovatele

<table>
<tr><td>Aktuální kód ADAL:</td><td>MSAL protějšek:</td></tr>
<tr><td>
V ADAL.NET je podpora zprostředkovatele povolená na základě kontextu pro ověřování. Ve výchozím nastavení je zakázaný. Museli jste nastavit

`useBroker` Příznak na hodnotu true v `PlatformParameters` konstruktoru pro volání zprostředkovatele:

```csharp
public PlatformParameters(
        UIViewController callerViewController,
        bool useBroker)
```
V kódu specifickém pro platformu v tomto příkladu můžete v nástroji pro vykreslování stránky pro iOS nastavit `useBroker`
Příznak na hodnotu true:
```csharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```

Pak zahrňte parametry v volání metody získání tokenu:
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
V MSAL.NET je podpora zprostředkovatelů povolená na základě PublicClientApplication. Ve výchozím nastavení je zakázaný. Pokud ho chcete povolit, použijte

`WithBroker()` parametr (ve výchozím nastavení nastaven na hodnotu true), aby se mohl volat Zprostředkovatel:

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
V volání metody získání tokenu:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Krok 2: nastavení UIViewController ()
V ADAL.NET jste předali v UIViewController jako součást `PlatformParameters` . (Viz příklad v kroku 1.) V MSAL.NET, aby vývojářům poskytoval větší flexibilitu, se používá okno objektu, ale v běžném použití iOS se nepožaduje. Chcete-li použít zprostředkovatele, nastavte okno objektu tak, aby odesílalo a přijímalo odpovědi od zprostředkovatele.
<table>
<tr><td>Aktuální kód ADAL:</td><td>MSAL protějšek:</td></tr>
<tr><td>
UIViewController se předává do

`PlatformParameters` v platformě specifické pro iOS.

```csharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>
V MSAL.NET provedete dvě věci pro nastavení okna objektu pro iOS:

1. V `AppDelegate.cs` nastavte `App.RootViewController` na nový `UIViewController()` .
Toto přiřazení zajišťuje, že existuje UIViewController se voláním zprostředkovatele. Pokud není správně nastavená, může se zobrazit tato chyba: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. Na volání AcquireTokenInteractive použijte `.WithParentActivityOrWindow(App.RootViewController)` a předejte odkaz na okno objektu, které použijete.

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
V volání metody získání tokenu:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Krok 3: aktualizace AppDelegate pro zpracování zpětného volání
ADAL i MSAL volají zprostředkovatele a zprostředkovatel pak volá zpět do vaší aplikace prostřednictvím `OpenUrl` metody `AppDelegate` třídy. Další informace najdete v [této dokumentaci](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback).

Mezi ADAL.NET a MSAL.NET nejsou žádné změny.

### <a name="step-4-register-a-url-scheme"></a>Krok 4: registrace schématu adresy URL
ADAL.NET a MSAL.NET používají adresy URL k vyvolání zprostředkovatele a vrátí odpověď zprostředkovatele zpět do aplikace. Zaregistrujte schéma URL v `Info.plist` souboru pro vaši aplikaci následujícím způsobem:

<table>
<tr><td>Aktuální kód ADAL:</td><td>MSAL protějšek:</td></tr>
<tr><td>
Schéma adresy URL je pro vaši aplikaci jedinečné.
</td><td>
Rozhraní

`CFBundleURLSchemes` Název musí zahrnovat

`msauth.`

jako předponu, za kterou následuje vaše `CFBundleURLName`

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
> Toto schéma adresy URL se označuje jako součást identifikátoru URI přesměrování, který se používá k jednoznačné identifikaci aplikace, když obdrží odpověď od služby Broker.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Krok 5: Přidání identifikátoru zprostředkovatele do oddílu LSApplicationQueriesSchemes

ADAL.NET a MSAL.NET oba používají `-canOpenURL:` ke kontrole, jestli je zprostředkovatel nainstalovaný na zařízení. Přidejte do části LSApplicationQueriesSchemes souboru info. plist správný identifikátor pro zprostředkovatele iOS následujícím způsobem:

<table>
<tr><td>Aktuální kód ADAL:</td><td>MSAL protějšek:</td></tr>
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

### <a name="step-6-register-your-redirect-uri-in-the-azure-portal"></a>Krok 6: registrace identifikátoru URI přesměrování v Azure Portal

ADAL.NET a MSAL.NET v případě cílení na zprostředkovatele přidávají dodatečný požadavek na identifikátor URI přesměrování. Zaregistrujte identifikátor URI přesměrování do aplikace v Azure Portal.
<table>
<tr><td>Aktuální kód ADAL:</td><td>MSAL protějšek:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

Příklad:

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Příklad:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Další informace o tom, jak zaregistrovat identifikátor URI přesměrování v Azure Portal, najdete v části [Krok 7: Přidání identifikátoru URI přesměrování do registrace aplikace](msal-net-use-brokers-with-xamarin-apps.md#step-7-add-a-redirect-uri-to-your-app-registration).

### <a name="step-7-set-the-entitlementsplist"></a>**Krok 7: nastavte oprávnění. plist**

Povolit přístup k řetězci klíčů v souboru *oprávnění. plist* :

```xml
 <key>keychain-access-groups</key>
    <array>
      <string>$(AppIdentifierPrefix)com.microsoft.adalcache</string>
    </array>
```

Další informace o povolení přístupu k řetězci klíčů najdete v tématu [Povolení přístupu k řetězci klíčů](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

## <a name="next-steps"></a>Další kroky

Přečtěte si o [otázkách specifických pro Xamarin iOS pomocí MSAL.NET](msal-net-xamarin-ios-considerations.md).