---
title: Inicializovat klientské aplikace MSAL.NET | Azure
titleSuffix: Microsoft identity platform
description: Seznamte se s inicializací veřejných klientských a důvěrných klientských aplikací pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/18/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 7ff61811e8b736f8f6d104a253cfe5dc5e76c428
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2021
ms.locfileid: "104771358"
---
# <a name="initialize-client-applications-using-msalnet"></a>Inicializace klientských aplikací pomocí MSAL.NET
Tento článek popisuje inicializaci veřejného klienta a důvěrných klientských aplikací pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET).  Další informace o typech klientských aplikací najdete v tématu [veřejné klientské a důvěrné klientské aplikace](msal-client-applications.md).

Pomocí MSAL.NET 3. x je doporučený způsob vytvoření instance aplikace pomocí tvůrců aplikací: `PublicClientApplicationBuilder` a `ConfidentialClientApplicationBuilder` . Nabízí účinný mechanismus pro konfiguraci aplikace buď z kódu, nebo z konfiguračního souboru, nebo i smícháním obou přístupů.

[Referenční dokumentace k](/dotnet/api/microsoft.identity.client)  |  rozhraní API [Balíček na nugetu](https://www.nuget.org/packages/Microsoft.Identity.Client/)  |  [Zdrojový kód knihovny](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet)  |  [Ukázky kódu](sample-v2-code.md)

## <a name="prerequisites"></a>Předpoklady
Před inicializací aplikace je nejprve nutné [ji zaregistrovat](quickstart-register-app.md) , aby bylo možné aplikaci integrovat s platformou Microsoft identity.  Po registraci možná budete potřebovat následující informace (které najdete v Azure Portal):

- ID klienta (řetězec představující GUID)
- Adresa URL zprostředkovatele identity (pojmenovaná instance) a cílová skupina pro přihlášení k vaší aplikaci. Tyto dva parametry jsou souhrnně známé jako autorita.
- ID tenanta, pokud píšete obchodní aplikaci výhradně pro vaši organizaci (nazývá se jenom jediná aplikace tenanta).
- Tajný klíč aplikace (tajný řetězec klienta) nebo certifikát (typu X509Certificate2), pokud se jedná o důvěrnou klientskou aplikaci.
- Pro webové aplikace a někdy pro veřejné klientské aplikace (zejména v případě, že vaše aplikace potřebuje použít zprostředkovatele) nastavíte také redirectUri, kde bude poskytovatel identity kontaktovat zpět vaší aplikaci pomocí tokenů zabezpečení.

## <a name="ways-to-initialize-applications"></a>Způsoby inicializace aplikací
Existuje mnoho různých způsobů, jak vytvořit instanci klientských aplikací.

### <a name="initializing-a-public-client-application-from-code"></a>Inicializace veřejné klientské aplikace z kódu

Následující kód vytvoří instanci veřejné klientské aplikace, přihlašuje uživatele ve veřejném cloudu Microsoft Azure, se svými pracovními a školními účty nebo jejich osobními účty Microsoft.

```csharp
IPublicClientApplication app = PublicClientApplicationBuilder.Create(clientId)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-code"></a>Inicializace důvěrné klientské aplikace z kódu

Stejným způsobem následující kód vytvoří instanci důvěrné aplikace (webová aplikace, která se nachází v rámci `https://myapp.azurewebsites.net` ) zpracování tokenů od uživatelů ve veřejném cloudu Microsoft Azure, s pracovními a školními účty nebo jejich osobními účty Microsoft. Aplikace je identifikována poskytovatelem identity sdílením tajného klíče klienta:

```csharp
string redirectUri = "https://myapp.azurewebsites.net";
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithClientSecret(clientSecret)
    .WithRedirectUri(redirectUri )
    .Build();
```

Jak můžete v produkčním prostředí znát místo používání tajného klíče klienta, možná budete chtít sdílet s Azure AD a certifikátem. Kód by pak byl následující:

```csharp
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.Create(clientId)
    .WithCertificate(certificate)
    .WithRedirectUri(redirectUri )
    .Build();
```

### <a name="initializing-a-public-client-application-from-configuration-options"></a>Inicializace veřejné klientské aplikace z možností konfigurace

Následující kód vytvoří instanci veřejné klientské aplikace z konfiguračního objektu, který může být vyplněn programově nebo načten z konfiguračního souboru:

```csharp
PublicClientApplicationOptions options = GetOptions(); // your own method
IPublicClientApplication app = PublicClientApplicationBuilder.CreateWithApplicationOptions(options)
    .Build();
```

### <a name="initializing-a-confidential-client-application-from-configuration-options"></a>Inicializace důvěrné klientské aplikace z možností konfigurace

Stejný druh vzoru se vztahuje na důvěrné klientské aplikace. Můžete také přidat další parametry pomocí `.WithXXX` modifikátorů (tady je certifikát).

```csharp
ConfidentialClientApplicationOptions options = GetOptions(); // your own method
IConfidentialClientApplication app = ConfidentialClientApplicationBuilder.CreateWithApplicationOptions(options)
    .WithCertificate(certificate)
    .Build();
```

## <a name="builder-modifiers"></a>Tvůrce – modifikátory

V fragmentech kódu pomocí tvůrců aplikací `.With` lze použít několik metod jako modifikátory (například `.WithCertificate` a `.WithRedirectUri` ). 

### <a name="modifiers-common-to-public-and-confidential-client-applications"></a>Modifikátory společné pro veřejné a důvěrné klientské aplikace

Modifikátory, které můžete nastavit ve veřejném klientovi nebo v nástroji pro vytváření důvěrných klientských aplikací, jsou tyto:

|Modifikátor | Popis|
|--------- | --------- |
|`.WithAuthority()` 7 přepsání | Nastaví výchozí autoritu aplikace na autoritu Azure AD s možností výběru cloudu Azure, cílové skupiny, tenanta (ID tenanta nebo názvu domény) nebo poskytnutí přímo identifikátoru URI autority.|
|`.WithAdfsAuthority(string)` | Nastaví výchozí autoritu aplikace jako autoritu služby ADFS.|
|`.WithB2CAuthority(string)` | Nastaví výchozí autoritu aplikace jako autoritu Azure AD B2C.|
|`.WithClientId(string)` | Přepíše ID klienta.|
|`.WithComponent(string)` | Nastaví název knihovny pomocí MSAL.NET (z důvodů telemetrie). |
|`.WithDebugLoggingCallback()` | Při volání aplikace bude volána `Debug.Write` jednoduše povolení trasování ladění. Další informace najdete v tématu [protokolování](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) .|
|`.WithExtraQueryParameters(IDictionary<string,string> eqp)` | Nastavte dodatečné parametry dotazu na úrovni aplikace, které budou odeslány ve všech žádostech o ověření. To je přepsatelné na každé úrovni metody získání tokenu (se stejným `.WithExtraQueryParameters pattern` ).|
|`.WithHttpClientFactory(IMsalHttpClientFactory httpClientFactory)` | Povoluje pokročilé scénáře, jako je například konfigurace proxy serveru HTTP, nebo vynucení MSAL používání konkrétního HttpClient (například v ASP.NET Core Web Apps/rozhraní API).|
|`.WithLogging()` | Při volání aplikace bude volat zpětné volání s trasováním ladění. Další informace najdete v tématu [protokolování](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/logging) .|
|`.WithRedirectUri(string redirectUri)` | Přepíše výchozí identifikátor URI přesměrování. V případě veřejných klientských aplikací to bude užitečné pro scénáře týkající se zprostředkovatele.|
|`.WithTelemetry(TelemetryCallback telemetryCallback)` | Nastaví delegáta použitý k odeslání telemetrie.|
|`.WithTenantId(string tenantId)` | Přepíše ID tenanta nebo popis tenanta.|

### <a name="modifiers-specific-to-xamarinios-applications"></a>Modifikátory specifické pro aplikace Xamarin. iOS

Modifikátory, které můžete nastavit na tvůrci veřejné klientské aplikace v Xamarin. iOS, jsou:

|Modifikátor | Popis|
|--------- | --------- |
|`.WithIosKeychainSecurityGroup()` | **Jenom Xamarin. iOS**: nastaví skupinu zabezpečení řetězu klíčů pro iOS (pro trvalost mezipaměti).|

### <a name="modifiers-specific-to-confidential-client-applications"></a>Modifikátory specifické pro důvěrné klientské aplikace

V Tvůrci důvěrných klientských aplikací můžete nastavit Modifikátory:

|Modifikátor | Popis|
|--------- | --------- |
|`.WithCertificate(X509Certificate2 certificate)` | Nastaví certifikát identifikující aplikaci pomocí Azure AD.|
|`.WithClientSecret(string clientSecret)` | Nastaví tajný klíč klienta (heslo aplikace) identifikující aplikaci pomocí Azure AD.|

Tyto modifikátory se vzájemně vylučují. Pokud zadáte obě, MSAL vyvolá smysluplnou výjimku.

### <a name="example-of-usage-of-modifiers"></a>Příklad použití modifikátorů

Předpokládejme, že vaše aplikace je obchodní aplikace, která je určena pouze pro vaši organizaci.  Pak můžete napsat:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAuthority(AzureCloudInstance.AzurePublic, tenantId)
        .Build();
```

Tam, kde se to bude zajímavé, je teď zjednodušené programování pro národní cloudy. Pokud chcete, aby vaše aplikace byla víceklientské aplikace v národním cloudu, můžete napsat například:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAuthority(AzureCloudInstance.AzureUsGovernment, AadAuthorityAudience.AzureAdMultipleOrgs)
        .Build();
```

K dispozici je také přepsání služby ADFS (ADFS 2019 není aktuálně podporováno):
```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithAdfsAuthority("https://consoso.com/adfs")
        .Build();
```

Nakonec, pokud jste vývojář Azure AD B2C, můžete určit svého tenanta takto:

```csharp
IPublicClientApplication app;
app = PublicClientApplicationBuilder.Create(clientId)
        .WithB2CAuthority("https://fabrikamb2c.b2clogin.com/tfp/{tenant}/{PolicySignInSignUp}")
        .Build();
```

## <a name="next-steps"></a>Další kroky

Po inicializaci klientské aplikace je dalším úkolem přidat podporu přihlašování uživatelů, autorizovaný přístup k rozhraní API nebo obojí.

Dokumentace k scénáři použití naší aplikace poskytuje pokyny pro přihlášení uživatele a získání přístupového tokenu pro přístup k rozhraní API jménem tohoto uživatele:

- [Webová aplikace, která přihlašuje uživatele: přihlášení a odhlášení](scenario-web-app-sign-user-sign-in.md)
- [Webová aplikace, která volá webová rozhraní API: Získá token.](scenario-web-app-call-api-acquire-token.md)
