---
title: Inicializovat MSAL.NET klientských aplikací | Azure
titleSuffix: Microsoft identity platform
description: Informace o inicializaci veřejných klientských a důvěrných klientských aplikací pomocí knihovny Microsoft Authentication Library for .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/12/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 57ce6ab31421cd4016f7e204eeabce82f2f7e6a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083982"
---
# <a name="initialize-client-applications-using-msalnet"></a>Inicializovat klientské aplikace pomocí MSAL.NET
Tento článek popisuje inicializaci veřejných klientských a důvěrných klientských aplikací pomocí knihovny Microsoft Authentication Library pro rozhraní .NET (MSAL.NET).  Další informace o typech klientských aplikací a možnostech konfigurace aplikace naleznete v [přehledu](msal-client-applications.md).

U MSAL.NET 3.x, doporučený způsob, jak vytvořit instanci aplikace `PublicClientApplicationBuilder` je `ConfidentialClientApplicationBuilder`pomocí tvůrce aplikací: a . Nabízejí výkonný mechanismus pro konfiguraci aplikace buď z kódu, nebo z konfiguračního souboru, nebo dokonce smícháním obou přístupů.

## <a name="prerequisites"></a>Požadavky
Před inicializací aplikace je třeba ji [nejprve zaregistrovat,](quickstart-register-app.md) aby bylo možné aplikaci integrovat s platformou identit Microsoftu.  Po registraci budete možná potřebovat následující informace (které najdete na webu Azure Portal):

- ID klienta (řetězec představující identifikátor GUID)
- Adresa URL poskytovatele identity (s názvem instance) a přihlašovací cílovou skupinu pro vaši aplikaci. Tyto dva parametry jsou souhrnně označovány jako autorita.
- ID klienta, pokud píšete řadu obchodních aplikací výhradně pro vaši organizaci (také s názvem aplikace s jedním tenantem).
- Tajný klíč aplikace (tajný řetězec klienta) nebo certifikát (typu X509Certificate2), pokud se jedná o důvěrnou klientskou aplikaci.
- Pro webové aplikace a někdy i pro aplikace veřejných klientů (zejména když vaše aplikace potřebuje používat zprostředkovatele), budete mít také nastavit redirectUri, kde poskytovatel identity bude kontaktovat zpět vaši aplikaci s tokeny zabezpečení.

## <a name="ways-to-initialize-applications"></a>Způsoby inicializace aplikací
Existuje mnoho různých způsobů, jak vytvořit konkretizovat klientské aplikace.

### <a name="initializing-a-public-client-application-from-code"></a>Inicializace aplikace veřejného klienta z kódu

Následující kód inkonsuje veřejnou klientskou aplikaci, přihlášení uživatelů ve veřejném cloudu Microsoft Azure, s jejich pracovními a školními účty nebo jejich osobními účty Microsoft.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Inicializace důvěrné klientské aplikace z kódu

Stejným způsobem následující kód inkonzisuje důvěrnou aplikaci `https://myapp.azurewebsites.net`(webovou aplikaci umístěnou na ) zpracovávající tokeny od uživatelů ve veřejném cloudu Microsoft Azure, s jejich pracovními a školními účty nebo jejich osobními účty Microsoft. Aplikace je identifikována s poskytovatelem identity sdílením tajného klíče klienta:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Jak možná víte, v produkčním prostředí, nikoli pomocí tajného klíče klienta, můžete chtít sdílet s Azure AD certifikát. Kód by pak být následující:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Inicializace aplikace veřejného klienta z možností konfigurace

Následující kód inkonaluje veřejnou klientskou aplikaci z objektu konfigurace, který může být vyplněn programově nebo přečten z konfiguračního souboru:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Inicializace důvěrné klientské aplikace z možností konfigurace

Stejný druh vzoru platí pro důvěrné klientské aplikace. Můžete také přidat další `.WithXXX` parametry pomocí modifikátorů (zde certifikát).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Modifikátory tvůrce

Ve fragmentech kódu pomocí tvůrců aplikací lze `.With` jako modifikátory použít řadu `.WithCertificate` metod `.WithRedirectUri`(například a ). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Modifikátory společné pro veřejné a důvěrné klientské aplikace

Modifikátory, které můžete nastavit u veřejného klienta nebo tvůrce důvěrných klientských aplikací, jsou:

|Modifikátor | Popis|
|--------- | --------- |
|`.WithAuthority()`7 přepsání | Nastaví výchozí autoritu aplikace na autoritu Azure AD s možností výběru Azure Cloudu, cílové skupiny, tenanta (ID klienta nebo názvu domény) nebo poskytnutí identifikátoru URI přímo autority.|
|`.WithAdfsAuthority(string)` | Nastaví výchozí autoritu aplikace jako autoritu Služby ADFS.|
|`.WithB2CAuthority(string)` | Nastaví výchozí autoritu aplikace jako autoritu Azure AD B2C.|
|`.WithClientId(string)` | Přepíše ID klienta.|
|`.WithComponent(string)` | Nastaví název knihovny pomocí MSAL.NET (z důvodů telemetrie). |
|`.WithDebugLoggingCallback()` | Pokud je volána, `Debug.Write` aplikace bude volat jednoduše povolení ladění trasování. Další informace naleznete v [tématu Protokolování.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging)|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Nastavte další parametry dotazu na úrovni aplikace, které budou odeslány ve všech požadavcích na ověření. To je overridable na každé úrovni metody `.WithExtraQueryParameters pattern`pořízení tokenu (se stejným ).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Umožňuje pokročilé scénáře, jako je konfigurace proxy protokolu HTTP nebo vynutit MSAL používat konkrétní httpclient (například v ASP.NET základní webové aplikace nebo api).|
|`.WithLogging()` | Pokud je volána, aplikace bude volat zpětné volání s ladění trasování. Další informace naleznete v [tématu Protokolování.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging)|
|`.WithRedirectUri(string redirectUri)` | Přepíše výchozí identifikátor URI přesměrování. V případě veřejných klientských aplikací to bude užitečné pro scénáře zahrnující zprostředkovatele.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Nastaví delegáta, který slouží k odesílání telemetrie.|
|`.WithTenantId(string tenantId)` | Přepíše ID klienta nebo popis klienta.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Modifikátory specifické pro aplikace Xamarin.iOS

Modifikátory, které můžete nastavit u tvůrce aplikací veřejného klienta na Xamarin.iOS, jsou:

|Modifikátor | Popis|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Pouze Xamarin.iOS**: Nastaví skupinu zabezpečení řetězce klíčů iOS (pro trvalost mezipaměti).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Modifikátory specifické pro důvěrné klientské aplikace

Modifikátory, které můžete nastavit v důvěrném tvůrce klientských aplikací, jsou:

|Modifikátor | Popis|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Nastaví certifikát identifikující aplikaci pomocí Služby Azure AD.|
|`.WithClientSecret(string clientSecret)` | Nastaví tajný klíč klienta (heslo aplikace) identifikující aplikaci pomocí azure ad.|

Tyto modifikátory se vzájemně vylučují. Pokud zadáte obojí, MSAL vyvolá smysluplnou výjimku.

### <a name="example-of-usage-of-modifiers"></a>Příklad použití modifikátorů

Předpokládejme, že vaše aplikace je obchodní aplikace, která je pouze pro vaši organizaci.  Pak můžete napsat:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Zajímavé je, že programování pro národní mraky se nyní zjednodušilo. Pokud chcete, aby vaše aplikace byla víceklientská aplikace v národním cloudu, můžete napsat, například:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAadAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

K dispozici je také přepsání pro ADFS (ADFS 2019 není aktuálně podporována):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Nakonec pokud jste vývojář Azure AD B2C, můžete zadat svého tenanta takto:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```
