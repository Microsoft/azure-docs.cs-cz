---
title: Konfigurace chráněných webových aplikací API | Azure
titleSuffix: Microsoft identity platform
description: Naučte se vytvářet chráněné webové rozhraní API a konfigurovat kód vaší aplikace.
services: active-directory
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f78fa35096b7e17d3736190bfa49619c2c81520
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965394"
---
# <a name="protected-web-api-code-configuration"></a>Chráněné webové rozhraní API: Konfigurace kódu

Pokud chcete nakonfigurovat kód pro vaše chráněné webové rozhraní API, musíte pochopit, co definují rozhraní API jako chráněné, jak nakonfigurovat nosný token a jak ověřit token.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>Co definuje rozhraní API ASP.NET/ASP.NET Core jako chráněná?

Podobně jako webové aplikace jsou webová rozhraní API ASP.NET/ASP.NET Core "chráněná", protože jejich akce kontrol jsou předponou atributu `[Authorize]`. To znamená, že akce kontroleru lze volat pouze v případě, že je rozhraní API voláno s identitou, která je autorizována.

Zvažte následující otázky:

- Jak webové rozhraní API zná identitu aplikace, která ji volá? (Webové rozhraní API může volat jenom aplikace.)
- Jaká je identita uživatele v případě, že aplikace volala webové rozhraní API jménem uživatele?

## <a name="bearer-token"></a>Nosný token

Informace o identitě aplikace a o uživateli (Pokud webová aplikace nepřijímá volání mezi službami z aplikace typu démon), je držena v nosných tokenech, který je nastaven v hlavičce při volání aplikace.

Zde je příklad C# kódu, který zobrazuje klienta volající rozhraní API poté, co získá token pomocí knihovny Microsoft Authentication Library pro .net (MSAL.NET):

```CSharp
var scopes = new[] {$"api://.../access_as_user}";
var result = await app.AcquireToken(scopes)
                      .ExecuteAsync();

httpClient = new HttpClient();
httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

// Call the web API.
HttpResponseMessage response = await _httpClient.GetAsync(apiUri);
```

> [!IMPORTANT]
> Klientská aplikace požádala o token nosiče na koncový bod Microsoft Identity Platform *pro webové rozhraní API*. Webové rozhraní API je jediná aplikace, která by měla ověřit token a zobrazit deklarace identity, které obsahuje. Klientské aplikace by se nikdy nepokusily kontrolovat deklarace identity v tokenech. (Webové rozhraní API může v budoucnu vyžadovat, aby byl token zašifrovaný. Tento požadavek by bránil přístupu pro klientské aplikace, které mohou zobrazit přístupové tokeny.)

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

Když se aplikace zavolá na akci kontroleru, která obsahuje atribut `[Authorize]`, ASP.NET/ASP.NET Core vyhledá nosný token v autorizační hlavičce volajícího požadavku a extrahuje přístupový token. Token se pak přesměruje do middlewaru JwtBearer, který volá rozšíření Microsoft IdentityModel pro .NET.

V ASP.NET Core se tento middleware Inicializuje v souboru Startup.cs:

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Middleware se do webového rozhraní API přidá pomocí této instrukce:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 V současné době šablony ASP.NET Core vytvářejí webová rozhraní API Azure Active Directory (Azure AD), která přihlašuje uživatele v rámci vaší organizace nebo v jakékoli organizaci, nikoli s osobními účty. Ale můžete je snadno změnit na použití koncového bodu Microsoft Identity Platform přidáním tohoto kódu do souboru Startup.cs:

```CSharp
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

Tento fragment kódu se extrahuje z přírůstkového kurzu ASP.NET Core webového rozhraní API v [Microsoft. identity. Web/WebApiServiceCollectionExtensions. cs # L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). Metoda `AddProtectedWebApi`, která je mnohem více, je volána z Startup.cs

## <a name="token-validation"></a>Ověření tokenu

Middleware JwtBearer, jako je middleware OpenID Connect ve službě Web Apps, je směrována `TokenValidationParameters` k ověření tokenu. Token se dešifruje (podle potřeby), deklarace se extrahují a ověří se podpis. Middleware potom ověří token tím, že zkontroluje tato data:

- Cílí na webové rozhraní API (cílovou skupinu).
- Vystavila se pro aplikaci, která má povoleno volání webového rozhraní API (sub).
- Vystavila se službou STS (Trusted Security token Service) (Issuer).
- Jeho životnost je v rozsahu (vypršení platnosti).
- Nebylo manipulováno s (podpis).

Může se také jednat o zvláštní ověřování. Například je možné ověřit, zda jsou podpisové klíče (pokud jsou vložené v tokenu) důvěryhodné a že token nebude znovu přehráván. Některé protokoly nakonec vyžadují konkrétní ověření.

### <a name="validators"></a>Validátory

Kroky ověření jsou zachyceny v validátorech, které jsou všechny v [rozšíření Microsoft IdentityModel Extensions for .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) Open Source Library, v jednom zdrojovém souboru: [Microsoft. IdentityModel. Tokens/validátors. cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Validátory jsou popsány v této tabulce:

| Hodnocení | Popis |
|---------|---------|
| `ValidateAudience` | Ověří, jestli je token pro aplikaci, která ověřuje token (pro mě). |
| `ValidateIssuer` | Zajišťuje, že token byl vydán důvěryhodnou službou STS (od někoho, kdo důvěřuje). |
| `ValidateIssuerSigningKey` | Zajistí, že aplikace ověřující token důvěřuje klíči, který se použil k podepsání tokenu. (Zvláštní případ, kdy je klíč vložený do tokenu. Obvykle není nutné.) |
| `ValidateLifetime` | Zajišťuje, že token je stále (nebo již) platný. Validátor kontroluje, zda je doba života tokenu (`notbefore` a `expires` deklarace) v rozsahu. |
| `ValidateSignature` | Zaručí, že token nebyl zfalšován. |
| `ValidateTokenReplay` | Zajistí, že token nebude znovu přehráván. (Zvláštní případ pro některé protokoly použití Jednorázová) |

Validátory jsou všechny přidruženy k vlastnostem třídy `TokenValidationParameters`, která byla inicializována z konfigurace jádra ASP.NET/ASP.NET. Ve většině případů nebudete muset parametry měnit. Pro aplikace, které nejsou jeden tenant, existuje jedna výjimka. (Tj. webové aplikace, které přijímají uživatele z jakékoli organizace nebo z osobních účtů Microsoft.) V takovém případě musí být Vystavitel ověřený.

## <a name="token-validation-in-azure-functions"></a>Ověřování tokenu v Azure Functions

Je také možné ověřit tokeny příchozích přístupových tokenů ve službě Azure Functions. Příklady ověřování tokenů ve službě Azure Functions můžete najít v [dotnet](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)a [Pythonu](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Ověření oborů a rolí aplikací v kódu](scenario-protected-web-api-verification-scope-app-roles.md)
