---
title: Inicializace klientské aplikace (knihovna Microsoft Authentication Library pro .NET) | Azure
description: Další informace o inicializaci veřejným klientem a důvěrné klientské aplikace pomocí knihovna Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f22ff41e380a16af2aa45df9a61eefbf293ff83
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65544322"
---
# <a name="initialize-client-applications-using-msalnet"></a>Inicializace klientské aplikace pomocí MSAL.NET
Tento článek popisuje inicializace veřejným klientem a důvěrné klientské aplikace pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET).  Další informace o typy klientských aplikací a možnosti konfigurace aplikace, [přehled](msal-client-applications.md).

Pomocí MSAL.NET 3.x, je doporučený postup pro vytvoření instance aplikace s použitím tvůrci aplikací: `PublicClientApplicationBuilder` a `ConfidentialClientApplicationBuilder`. Nabízí výkonný mechanismus pro konfiguraci aplikace od kódu, nebo z konfiguračního souboru nebo dokonce kombinace obou metod.

## <a name="prerequisites"></a>Požadavky
Před inicializací aplikace, musíte nejprve [ho zaregistrovat](quickstart-register-app.md) tak, aby vaše aplikace je možné integrovat s platformou identity Microsoft.  Po registraci může potřebovat následující informace (který se nachází na webu Azure Portal):

- ID klienta (řetězec představující GUID)
- Adresa URL zprostředkovatele identity (s názvem instance) a skupinou přihlášení pro aplikaci. Tyto dva parametry jsou souhrnně označovány jako autorita.
- ID tenanta, pokud píšete řádek obchodní aplikace pouze pro vaši organizaci (také pojmenované jednoho tenanta aplikaci).
- Tajný klíč aplikace (tajného kódu klienta string) nebo certifikátu (typu X509Certificate2) Pokud je aplikace důvěrnému klientovi.
- Pro webové aplikace a někdy pro aplikace veřejným klientem (zejména když vaše aplikace potřebuje pomocí zprostředkovatele) budete mít také nastavíte redirectUri kde kontaktuje zprostředkovatele identity back aplikace s použitím tokenů zabezpečení.

## <a name="ways-to-initialize-applications"></a>Způsoby, jak inicializovat aplikací
Chcete-li vytvořit instanci klientské aplikace mnoha různými způsoby.

### <a name="initializing-a-public-client-application-from-code"></a>Inicializace aplikace veřejným klientem z kódu

Následující kód vytvoří instanci aplikace veřejným klientem přihlašování uživatelů ve veřejném cloudu Microsoft Azure, pomocí jejich pracovních a školních účtů nebo svých osobních účtů Microsoft.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Inicializace aplikace důvěrnému klientovi z kódu

Stejným způsobem, následující kód vytvoří instanci aplikace důvěrné (webová aplikace umístěné v `https://myapp.azurewebsites.net`) zpracování tokeny od uživatelů ve veřejném cloudu Microsoft Azure, pomocí jejich pracovních a školních účtů nebo svých osobních účtů Microsoft. Aplikace identifikovaný pomocí zprostředkovatele identity sdílení tajný kód klienta:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Protože možná znáte, a v produkčním prostředí, nikoli pomocí tajného klíče klienta, můžete chtít sdílet certifikát s Azure AD. Pak budou následující kód:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Inicializace aplikace veřejným klientem z možností konfigurace

Následující kód vytvoří instanci aplikace veřejným klientem z objekt konfigurace, které by mohly být vyplněné prostřednictvím kódu programu nebo číst z konfiguračního souboru:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Inicializace aplikace důvěrnému klientovi z možností konfigurace

Stejný druh vzor se vztahuje na aplikace důvěrnému klientovi. Můžete také přidat další parametry s využitím `.WithXXX` modifikátory (tady certifikát).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Modifikátory Tvůrce

Ve fragmentech kódu pomocí Tvůrce aplikací, řadu `.With` metody mohou být použity jako modifikátory (například `.WithCertificate` a `.WithRedirectUri`). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Modifikátory společné pro veřejné a důvěrné klientské aplikace

Modifikátory, můžete nastavit na veřejným klientem nebo Tvůrce aplikací důvěrnému klientovi jsou:

|Parametr | Popis|
|--------- | --------- |
|`.WithAuthority()` 7 přepsání | Nastaví výchozí oprávnění aplikace pro autoritu Azure AD, s možností výběru cloudu Azure, cílovou skupinu, tenanta (tenant ID nebo názvu domény), nebo které uvádějí přímo identifikátor URI autority.|
|`.WithAdfsAuthority(string)` | Nastaví výchozí oprávnění aplikace se autoritu služby AD FS.|
|`.WithB2CAuthority(string)` | Nastaví výchozí oprávnění aplikace se autoritu Azure AD B2C.|
|`.WithClientId(string)` | Přepíše ID klienta.|
|`.WithComponent(string)` | Nastaví název knihovny pomocí MSAL.NET (z důvodů telemetrie). |
|`.WithDebugLoggingCallback()` | Pokud je volána, aplikace bude volat `Debug.Write` jednoduše povolení trasování ladění. Zobrazit [protokolování](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) Další informace.|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Nastavte parametry aplikace úrovně dalších dotazů, které se pošle všechny žádosti o ověření. Toto je přepsatelný na každé úrovni způsob získání tokenu (se stejným `.WithExtraQueryParameters pattern`).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Umožňuje používat pokročilé scénáře, jako je konfigurace pro proxy server HTTP, nebo vynutit MSAL používat konkrétní HttpClient (například v ASP.NET Core webová aplikace/rozhraní API).|
|`.WithLogging()` | Pokud je volána, aplikace bude volat zpětného volání s trasování ladění. Zobrazit [protokolování](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) Další informace.|
|`.WithRedirectUri(string redirectUri)` | Přepíše výchozí URI přesměrování. V případě aplikace veřejným klientem to být užitečné pro scénáře zahrnující zprostředkovatele.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Nastaví delegáta používaný k odesílání telemetrie.|
|`.WithTenantId(string tenantId)` | Přepisuje ID tenanta nebo popis tenanta.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Modifikátory specifické pro aplikace Xamarin.iOS

Modifikátory, můžete nastavit na tvůrce aplikace veřejným klientem na Xamarin.iOS jsou:

|Parametr | Popis|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Xamarin.iOS only**: Nastaví zabezpečení skupiny pro řetězce klíčů iOS (pro zachování v mezipaměti).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Modifikátory specifické pro důvěrné klientské aplikace

Modifikátory, můžete nastavit na tvůrce aplikací důvěrnému klientovi jsou:

|Parametr | Popis|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Nastaví certifikát Identifikace aplikací s Azure AD.|
|`.WithClientSecret(string clientSecret)` | Nastaví tajný klíč klienta (heslo aplikace) identifikaci aplikace v Azure AD.|

Tyto modifikátory se vzájemně vylučují. Pokud zadáte obě, MSAL vyvolá smysluplné výjimku.

### <a name="example-of-usage-of-modifiers"></a>Příklad použití modifikátorů

Předpokládejme, že je vaše aplikace – obchodní aplikace, která je pouze pro vaši organizaci.  Potom můžete psát:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Pokud bude zajímavé je, že má nyní zjednodušená programování pro národní cloudy. Pokud chcete aplikaci jako aplikaci s více tenanty v národních cloudů, můžete napsat, například:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

K dispozici je také přepsání pro AD FS (2019 služby AD FS není aktuálně podporována):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Pokud pro vývojáře Azure AD B2C, můžete zadat tenanta takto:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
