---
title: Konfigurace aplikací chráněného webového rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak vytvořit chráněné webové rozhraní API a nakonfigurovat kód aplikace.
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
ms.openlocfilehash: 3f07105c14d4dafeb689eaaf7d679f93e5f235fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262513"
---
# <a name="protected-web-api-code-configuration"></a>Chráněné webové rozhraní API: Konfigurace kódu

Chcete-li nakonfigurovat kód pro chráněné webové rozhraní API, musíte pochopit:

- Co definuje api jako chráněné.
- Jak nakonfigurovat nosný token.
- Jak ověřit token.

## <a name="what-defines-aspnet-and-aspnet-core-apis-as-protected"></a>Co definuje ASP.NET a ASP.NET základní api jako chráněné?

Stejně jako webové aplikace jsou chráněny ASP.NET a ASP.NET webových api core, protože jejich akce kontroleru jsou předponou atributem **[Authorize].** Akce kontroleru lze volat pouze v případě, že rozhraní API je volána s autorizovanou identitou.

Zvažte následující otázky:

- Webové rozhraní API může volat jenom aplikace. Jak rozhraní API zná identitu aplikace, která ji volá?
- Pokud aplikace volá rozhraní API jménem uživatele, jaká je identita uživatele?

## <a name="bearer-token"></a>Žeton na doručitele

Nosný token, který je nastaven v záhlaví při volání aplikace obsahuje informace o identitě aplikace. Obsahuje také informace o uživateli, pokud webová aplikace nepřijímá volání mezi službami z aplikace daemon.

Zde je příklad kódu Jazyka C#, který zobrazuje klienta volajícího rozhraní API poté, co získá token s knihovnou ověřování Microsoft pro .NET (MSAL.NET):

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
> Klientská aplikace požaduje nosný token do koncového bodu platformy identit y Microsoft *pro webové rozhraní API*. Webové rozhraní API je jediná aplikace, která by měla ověřit token a zobrazit deklarace identity, které obsahuje. Klientské aplikace by se nikdy neměly pokoušet kontrolovat deklarace identity v tokenech.
>
> V budoucnu webové rozhraní API může vyžadovat, aby token být zašifrována. Tento požadavek by zabránil přístupu klientských aplikací, které mohou zobrazit přístupové tokeny.

## <a name="jwtbearer-configuration"></a>Konfigurace JwtBearer

Tato část popisuje, jak nakonfigurovat token nosiče.

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

Když je aplikace volána na akci řadiče, který obsahuje atribut **[Authorize],** ASP.NET a ASP.NET Core extrahují přístupový token z tokenu nosiče hlavičky autorizace. Přístupový token je pak předán do middleware JwtBearer, který volá rozšíření Microsoft IdentityModel pro .NET.

V ASP.NET Core je tento middleware inicializován v souboru Startup.cs.

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Middleware je přidán do webového rozhraní API podle této instrukce:

```csharp
 services.AddAuthentication(AzureADDefaults.JwtBearerAuthenticationScheme)
         .AddAzureADBearer(options => Configuration.Bind("AzureAd", options));
```

 V současné době ASP.NET základní šablony vytvořit Azure Active Directory (Azure AD) webová rozhraní API, která přihlašují uživatele v rámci vaší organizace nebo jakékoli organizace. Nepřihlašují uživatele pomocí osobních účtů. Ale můžete změnit šablony používat koncový bod platformy identity Microsoft přidáním tohoto kódu do Startup.cs:

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

Předchozí fragment kódu je extrahován z přírůstkového kurzu ASP.NET core web API v [Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63](https://github.com/Azure-Samples/active-directory-dotnet-native-aspnetcore-v2/blob/154282843da2fc2958fad151e2a11e521e358d42/Microsoft.Identity.Web/WebApiServiceCollectionExtensions.cs#L50-L63). **AddProtectedWebApi** metoda, která dělá více než úryvek ukazuje, je volána z Startup.cs.

## <a name="token-validation"></a>Ověření tokenu

V předchozím úryvku, JwtBearer middleware, stejně jako OpenID Connect middleware ve webových `TokenValidationParameters`aplikacích, ověřuje token na základě hodnoty . Token je dešifrován podle potřeby, deklarace identity jsou extrahovány a podpis je ověřen. Middleware pak ověří token kontrolou těchto dat:

- Cílová skupina: Token je určen pro webové rozhraní API.
- Sub: Byl vydán pro aplikaci, která je povoleno volat webové rozhraní API.
- Vystavitel: Byl vydán službou důvěryhodných tokenů zabezpečení (STS).
- Expirace: Jeho životnost je v dosahu.
- Podpis: Nebylo s ním manipulováno.

K dispozici mohou být také speciální ověření. Například je možné ověřit, že podpisové klíče, když jsou vloženy do tokenu, jsou důvěryhodné a že token není přehrán. Nakonec některé protokoly vyžadují konkrétní ověření.

### <a name="validators"></a>Validátory

Ověřovací kroky jsou zachyceny v validátorech, které jsou [poskytovány rozšířeními Microsoft IdentityModel pro](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) otevřenou knihovnu .NET. Validátory jsou definovány ve zdrojovém souboru [knihovny Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Tato tabulka popisuje validátory:

| Validátor | Popis |
|---------|---------|
| **Ověřit okruh uživatelů** | Zajišťuje, že token je pro aplikaci, která ověřuje token pro vás. |
| **ValidateIssuer** | Zajišťuje, že token byl vydán důvěryhodným STS, což znamená, že je od někoho, komu důvěřujete. |
| **Podpisový klíč validateissuer** | Zajišťuje, že aplikace ověřující token uvěří klíči, který byl použit k podepsání tokenu. Existuje zvláštní případ, kdy je klíč vložen do tokenu. Ale tento případ obvykle nevzniká. |
| **Ověřithodnotu** | Zajišťuje, že token je stále nebo již platný. Validátor zkontroluje, zda je životnost tokenu v rozsahu určeném **notbefore** a **vyprší deklarace identity.** |
| **Ověřit podpis** | Zajišťuje, že s tokenem nebylo manipulováno. |
| **OvěřitTokenReplay** | Zajišťuje, že token není přehrán. Je tu zvláštní případ pro nějaké jednorázové protokoly. |

Validátory jsou přidruženy k vlastnostem **třídy TokenValidationParameters.** Vlastnosti jsou inicializovány z konfigurace ASP.NET a ASP.NET Core.

Ve většině případů není nutné měnit parametry. Aplikace, které nejsou jedním tenantem, jsou výjimky. Tyto webové aplikace přijímají uživatele z jakékoli organizace nebo z osobních účtů Microsoft. Emitenti v tomto případě musí být ověřeny.

## <a name="token-validation-in-azure-functions"></a>Ověření tokenu ve funkcích Azure

Můžete také ověřit tokeny příchozího přístupu v Azure Functions. Příklady takového ověření naleznete v [aplikacích Microsoft .NET](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions), [NodeJS](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions)a [Python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Ověření oborů a rolí aplikací v kódu](scenario-protected-web-api-verification-scope-app-roles.md)
