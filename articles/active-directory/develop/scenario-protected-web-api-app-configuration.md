---
title: Konfigurace chráněných webových aplikací API | Azure
titleSuffix: Microsoft identity platform
description: Naučte se vytvářet chráněné webové rozhraní API a konfigurovat kód vaší aplikace.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5206c2295ee7c01b4a2908e59da1cfdd8782bccd
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102517714"
---
# <a name="protected-web-api-code-configuration"></a>Chráněné webové rozhraní API: Konfigurace kódu

Pokud chcete nakonfigurovat kód pro vaše chráněné webové rozhraní API, seznámete s těmito principy:

- Co definuje rozhraní API jako chráněná.
- Jak nakonfigurovat nosný token.
- Jak ověřit token.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>Co definuje rozhraní API pro ASP.NET a ASP.NET Core jako chráněná?

Podobně jako webové aplikace jsou webová rozhraní API pro ASP.NET a ASP.NET Core chráněná, protože jejich řídicí akce mají předponu atributu **[autorizovat]** . Akce kontroleru se dají volat jenom v případě, že se rozhraní API volá s autorizovanou identitou.

Zvažte následující otázky:

- Webové rozhraní API může volat jenom aplikace. Jak rozhraní API zná identitu aplikace, která ji volá?
- Pokud aplikace volá rozhraní API jménem uživatele, jaká je identita uživatele?

## <a name="bearer-token"></a>Nosný token

Nosný token, který je nastaven v hlavičce při volání aplikace, obsahuje informace o identitě aplikace. Obsahuje také informace o uživateli, pokud webová aplikace nepřijímá volání mezi službami z aplikace démon.

Zde je příklad kódu jazyka C#, který po získání tokenu pomocí knihovny Microsoft Authentication Library pro .NET (MSAL.NET) zobrazuje klienta, který volá rozhraní API.

```csharp
var scopes = new[] {$"api://.../access_as_user"};
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Klientská aplikace požaduje token nosiče na platformě identity Microsoftu *pro webové rozhraní API*. Webové rozhraní API je jediná aplikace, která by měla ověřit token a zobrazit deklarace identity, které obsahuje. Klientské aplikace by se nikdy nepokusily kontrolovat deklarace identity v tokenech.
>
> V budoucnu může webové rozhraní API vyžadovat, aby byl token zašifrovaný. Tento požadavek by bránil přístupu pro klientské aplikace, které mohou zobrazit přístupové tokeny.

## <a name="jwtbearer-configuration"></a>Konfigurace JwtBearer

Tato část popisuje, jak nakonfigurovat nosný token.

### <a name="config-file"></a>Konfigurační soubor

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line-of-business app).
      Otherwise, you can leave them set to common.
      This can be:
      - A GUID (Tenant ID = Directory ID)
      - 'common' (any organization and personal accounts)
      - 'organizations' (any organization)
      - 'consumers' (Microsoft personal accounts)
    */
    "TenantId": "common"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Warning"
    }
  },
  "AllowedHosts": "*"
}
```

#### <a name="case-where-you-used-a-custom-app-id-uri-for-your-web-api"></a>Případ, kdy jste pro vaše webové rozhraní API použili vlastní identifikátor URI ID aplikace

Pokud jste přijali identifikátor URI ID aplikace navržený portálem pro registraci aplikací, nemusíte zadávat cílovou skupinu (viz [identifikátor URI a obory ID aplikace](scenario-protected-web-api-app-registration.md#application-id-uri-and-scopes)). V opačném případě byste měli přidat `Audience` vlastnost, jejíž hodnota je identifikátor URI ID aplikace pro vaše webové rozhraní API.

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common",
    "Audience": "custom App ID URI for your web API"
  },
  // more lines
}
```

### <a name="code-initialization"></a>Inicializace kódu

Když se aplikace zavolá na akci kontroleru, která obsahuje atribut **[Authorization]** , ASP.NET a ASP.NET Core extrahuje přístupový token z tokenu Bearer autorizační hlavičky. Přístupový token se pak přesměruje do middlewaru JwtBearer, který volá rozšíření Microsoft IdentityModel pro .NET.

#### <a name="microsoftidentityweb"></a>Microsoft. identity. Web

Microsoft doporučuje při vývoji webového rozhraní API pomocí ASP.NET Core použít balíček NuGet [Microsoft. identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) .

_Microsoft. identity. Web_ nabízí připevnit mezi ASP.NET Core, middlewarem ověřování a [Microsoft Authentication Library (MSAL)](msal-overview.md) pro .NET. Umožňuje vyjasnější a robustnější vývojové prostředí a využívá sílu platformy a Azure AD B2C Microsoft identity.

#### <a name="using-microsoftidentityweb-templates"></a>Používání šablon Microsoft. identity. Web

Můžete vytvořit webové rozhraní API od nuly pomocí šablon Microsoft. identity. Web Project. Podrobnosti najdete v tématu [Šablona projektu Microsoft. identity. Web-Web API](https://aka.ms/ms-id-web/webapi-project-templates).

#### <a name="starting-from-an-existing-aspnet-core-31-application"></a>Počínaje existující aplikací ASP.NET Core 3,1

Dnes ASP.NET Core 3,1 používá knihovnu Microsoft. AspNetCore. AzureAD. UI. Middleware je inicializována v souboru Startup. cs.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Middleware se do webového rozhraní API přidá pomocí této instrukce:

```csharp
// This method gets called by the runtime. Use this method to add services to the container.
public void ConfigureServices(IServiceCollection services)
{
  services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
          .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
}
```

 V současné době šablony ASP.NET Core vytvářejí webová rozhraní API Azure Active Directory (Azure AD), která přihlásí uživatele v rámci vaší organizace nebo v jakékoli organizaci. Nepodepisují uživatele pomocí osobních účtů. Šablony však můžete změnit tak, aby používaly platformu Microsoft Identity Platform pomocí [Microsoft. identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) nahrazující kód při *spuštění. cs*:

```csharp
using Microsoft.Identity.Web;
```

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddMicrosoftIdentityWebApiAuthentication(Configuration, "AzureAd");

 services.AddControllers();
}
```

Můžete také zapsat následující (což je ekvivalentní)

```csharp
public void ConfigureServices(IServiceCollection services)
{
 // Adds Microsoft Identity platform (AAD v2.0) support to protect this API
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
             .AddMicrosoftIdentityWebApi(Configuration, "AzureAd");

services.AddControllers();
}
```

> [!NOTE]
> Pokud používáte Microsoft. identity. Web a nenastavíte `Audience` v *appsettings.jsna*, použije se následující:
> -  `$"{ClientId}"` Pokud jste nastavili [verzi přístupového tokenu](scenario-protected-web-api-app-registration.md#accepted-token-version) na `2` nebo pro Azure AD B2C webová rozhraní API.
> - `$"api://{ClientId}` ve všech ostatních případech (pro [přístupové tokeny](access-tokens.md)v 1.0).
> Podrobnosti najdete v tématu Microsoft. identity. Web [Source Code](https://github.com/AzureAD/microsoft-identity-web/blob/d2ad0f5f830391a34175d48621a2c56011a45082/src/Microsoft.Identity.Web/Resource/RegisterValidAudience.cs#L70-L83).

Předchozí fragment kódu je extrahován z [přírůstkového kurzu ASP.NET Core webového rozhraní API](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/63087e83326e6a332d05fee6e1586b66d840b08f/1.%20Desktop%20app%20calls%20Web%20API/TodoListService/Startup.cs#L23-L28). Podrobnosti o **AddMicrosoftIdentityWebApiAuthentication** jsou k dispozici v [Microsoft. identity. Web](microsoft-identity-web.md). Tato metoda volá [AddMicrosoftIdentityWebAPI](/dotnet/api/microsoft.identity.web.microsoftidentitywebapiauthenticationbuilderextensions.addmicrosoftidentitywebapi), která sám instruuje middleware k ověření tokenu.

## <a name="token-validation"></a>Ověření tokenu

V předchozím fragmentu kódu JwtBearer middleware, jako je middleware OpenID Connect ve službě Web Apps, ověřuje token na základě hodnoty `TokenValidationParameters` . Token se v případě potřeby dešifruje, deklarace se extrahují a podpis se ověří. Middleware potom ověří token tím, že zkontroluje tato data:

- Cílová skupina: token je zaměřen na webové rozhraní API.
- Sub: byla vydána pro aplikaci, která má povoleno volání webového rozhraní API.
- Vystavitel: vystavila služba STS (Trusted Security tokens).
- Vypršení platnosti: jeho životnost je v rozsahu.
- Podpis: nebylo manipulováno s.

Může se také jednat o zvláštní ověřování. Například je možné ověřit, jestli jsou podpisové klíče, když jsou vložené v tokenu, důvěryhodné a že se token nebude přehrávat. Některé protokoly nakonec vyžadují konkrétní ověření.

### <a name="validators"></a>Validátory

Kroky ověření jsou zachyceny ve validátorech, které jsou poskytovány pomocí [rozšíření Microsoft IdentityModel Extensions pro](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) open source knihovny .NET. Validátory jsou definovány ve zdrojovém souboru knihovny [Microsoft. IdentityModel. tokeny/validátory. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Tato tabulka popisuje validátory:

| Hodnocení | Description |
|---------|---------|
| **ValidateAudience** | Ověří, jestli je token pro aplikaci, která token ověřuje za vás. |
| **ValidateIssuer** | Zajišťuje, že token byl vydán důvěryhodnou službou STS, což znamená, že je od někoho, kterému důvěřujete. |
| **ValidateIssuerSigningKey** | Zajistí, že aplikace ověřující token důvěřuje klíči, který se použil k podepsání tokenu. Je k dispozici zvláštní případ, ve kterém je klíč vložen do tokenu. K tomuto případu ale obvykle nedochází. |
| **ValidateLifetime** | Zajišťuje, že token je stále nebo již platný. Validátor kontroluje, zda je životnost tokenu v rozsahu určeném parametrem **NotBefore** a **vypršení platnosti** deklarací identity. |
| **ValidateSignature** | Zaručí, že token nebyl zfalšován. |
| **ValidateTokenReplay** | Zajistí, že token nebude znovu přehráván. Pro některé protokoly jednorázová se používá zvláštní případ. |

#### <a name="customizing-token-validation"></a>Přizpůsobení ověření tokenu

Validátory jsou přidruženy k vlastnostem třídy **TokenValidationParameters** . Vlastnosti jsou inicializovány z konfigurace ASP.NET a ASP.NET Core.

Ve většině případů nemusíte měnit parametry. Mezi aplikace, které nejsou jedenmi klienty, patří výjimka. Tyto webové aplikace přijímají uživatele z libovolné organizace nebo z osobních účtů Microsoft. Vystavitelé v tomto případě musí být ověřeni. Microsoft. identity. Web se zajímá také o ověření vystavitele. Podrobnosti najdete v článku Microsoft. identity. Web [AadIssuerValidator](https://github.com/AzureAD/microsoft-identity-web/blob/master/src/Microsoft.Identity.Web/Resource/AadIssuerValidator.cs).

Pokud v ASP.NET Core chcete přizpůsobit parametry ověření tokenu, použijte následující fragment kódu při *spuštění. cs*:

```c#
services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
        .AddMicrosoftIdentityWebApi(Configuration);
services.Configure<JwtBearerOptions>(JwtBearerDefaults.AuthenticationScheme, options =>
{
  var existingOnTokenValidatedHandler = options.Events.OnTokenValidated;
  options.Events.OnTokenValidated = async context =>
  {
       await existingOnTokenValidatedHandler(context);
      // Your code to add extra configuration that will be executed after the current event implementation.
      options.TokenValidationParameters.ValidIssuers = new[] { /* list of valid issuers */ };
      options.TokenValidationParameters.ValidAudiences = new[] { /* list of valid audiences */};
  };
});
```

V případě ASP.NET MVC ukazuje následující ukázka kódu, jak provést vlastní ověření tokenu:

https://github.com/azure-samples/active-directory-dotnet-webapi-manual-jwt-validation

## <a name="token-validation-in-azure-functions"></a>Ověřování tokenu v Azure Functions

Tokeny příchozích přístupových oprávnění můžete také ověřit v Azure Functions. Příklady takového ověřování najdete v následujících ukázkách kódu na GitHubu:

- .NET: [Azure-Samples/MS-identity-dotnet-WebApi-azurefunctions](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)
- Node.js: [Azure-Samples/MS-identity-NodeJS-WebApi-azurefunctions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)
- Python: [Azure-Samples/MS-identity-Python-WebApi-azurefunctions)](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions)

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku v tomto scénáři, [Ověřte obory a role aplikací ve vašem kódu](scenario-protected-web-api-verification-scope-app-roles.md).
