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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 073eca94ad93c69811b02abe2c8649940a394e8e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "80882467"
---
# <a name="protected-web-api-code-configuration"></a>Chráněné webové rozhraní API: Konfigurace kódu

Pokud chcete nakonfigurovat kód pro vaše chráněné webové rozhraní API, je potřeba pochopit:

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
> Klientská aplikace požaduje *pro webové rozhraní API*nosný token pro koncové body Microsoft Identity Platform. Webové rozhraní API je jediná aplikace, která by měla ověřit token a zobrazit deklarace identity, které obsahuje. Klientské aplikace by se nikdy nepokusily kontrolovat deklarace identity v tokenech.
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

### <a name="code-initialization"></a>Inicializace kódu

Když se aplikace zavolá na akci kontroleru, která obsahuje atribut **[Authorization]** , ASP.NET a ASP.NET Core extrahuje přístupový token z tokenu Bearer autorizační hlavičky. Přístupový token se pak přesměruje do middlewaru JwtBearer, který volá rozšíření Microsoft IdentityModel pro .NET.

V ASP.NET Core se tento middleware Inicializuje v souboru Startup.cs.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Middleware se do webového rozhraní API přidá pomocí této instrukce:

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 V současné době šablony ASP.NET Core vytvářejí webová rozhraní API Azure Active Directory (Azure AD), která přihlásí uživatele v rámci vaší organizace nebo v jakékoli organizaci. Nepodepisují uživatele pomocí osobních účtů. Ale můžete změnit šablony tak, aby používaly koncový bod Microsoft Identity Platform přidáním tohoto kódu do Startup.cs:

```csharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform web API.
    options.Authority += "/v2.0";

    // The web API accepts as audiences both the Client ID (options.Audience) and api://{ClientID}.
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line-of-business apps),
    // we inject our own multitenant validation logic (which even accepts both v1 and v2 tokens).
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.GetIssuerValidator(options.Authority).Validate;;
});
```

Předchozí fragment kódu se extrahuje z přírůstkového kurzu ASP.NET Core webového rozhraní API v [Microsoft. identity. Web/WebApiServiceCollectionExtensions. cs # L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). Metoda **AddProtectedWebApi** , která obsahuje více než fragment kódu, je volána z Startup.cs.

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

Validátory jsou přidruženy k vlastnostem třídy **TokenValidationParameters** . Vlastnosti jsou inicializovány z konfigurace ASP.NET a ASP.NET Core.

Ve většině případů nemusíte měnit parametry. Mezi aplikace, které nejsou jedenmi klienty, patří výjimka. Tyto webové aplikace přijímají uživatele z libovolné organizace nebo z osobních účtů Microsoft. Vystavitelé v tomto případě musí být ověřeni.

## <a name="token-validation-in-azure-functions"></a>Ověřování tokenu v Azure Functions

Tokeny příchozích přístupových oprávnění můžete také ověřit v Azure Functions. Příklady takového ověřování najdete v [Microsoft .NET](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)a [Pythonu](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Ověření oborů a rolí aplikací v kódu](scenario-protected-web-api-verification-scope-app-roles.md)
