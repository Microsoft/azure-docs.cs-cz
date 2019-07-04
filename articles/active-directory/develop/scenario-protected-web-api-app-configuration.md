---
title: Chráněné webové rozhraní API – konfigurace kódu aplikace | Azure
description: Zjistěte, jak sestavit chráněné webové rozhraní API a konfigurovat kódu vaší aplikace.
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
ms.openlocfilehash: fa262c1c6a091575a70c5670b1c7a7c96e8e2128
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536890"
---
# <a name="protected-web-api-code-configuration"></a>Chráněné webové rozhraní API: Konfigurace kódu

Pokud chcete nakonfigurovat kód pro chráněné webové rozhraní API, musíte pochopit, co definuje rozhraní API jako chráněný, jak nakonfigurovat nosného tokenu a ověření tokenu.

## <a name="what-defines-aspnetaspnet-core-apis-as-protected"></a>Co definuje rozhraní API Core ASP.NET/ASP.NET chráněná?

Stejně jako webových aplikací, ASP.NET/ASP.NET Core webová rozhraní API "chránily" protože jejich akce kontroleru mají předponu `[Authorize]` atribut. Takže akce kontroleru lze volat pouze v případě, že se volá rozhraní API s identitou, která má oprávnění.

Vezměte v úvahu následující otázky:

- Jak webové rozhraní API znát identitu aplikace, která ho zavolá? (Pouze aplikace může volat webové rozhraní API).
- Pokud aplikace volá webové rozhraní API jménem uživatele, co je identita uživatele?

## <a name="bearer-token"></a>Nosný token

Informace o identitě aplikace a informace o uživateli (Pokud je webová aplikace přijímá volání služba služba z démona aplikace), se nachází v nosný token, který je nastaven v hlavičce při volání aplikace.

Tady je C# příklad kódu, který ukazuje volání rozhraní API po získá token se knihovna Microsoft Authentication Library pro .NET (MSAL.NET) klienta:

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
> Nosný token byl požadován klientské aplikace do koncového bodu Microsoft identity platform *pro webové rozhraní API*. Webové rozhraní API je pouze aplikace, která by měl ověřit token a zobrazit deklarace identity, které obsahuje. Klientské aplikace by nikdy pokusí zkontrolovat deklarace identity do tokenů. (Webového rozhraní API může vyžadovat, v budoucnu se, že token zašifrovaná. Tento požadavek by jinak znemožňovaly přístup pro klientské aplikace, které můžete zobrazit přístupové tokeny.)

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

### <a name="code-initialization"></a>Inicializační kód

Když je aplikace volá na akce kontroleru, který obsahuje `[Authorize]` atribut, ASP.NET/ASP.NET Core zabývá nosného tokenu v autorizační hlavičce požadavku volání a extrahuje přístupový token. Token, který je předán JwtBearer middlewaru, který volá IdentityModel rozšíření společnosti Microsoft pro .NET.

V ASP.NET Core je tento middleware inicializován v souboru Startup.cs:

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Middleware přidává do webového rozhraní API tento pokyn:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Šablony ASP.NET Core v současné době vytvářet webové rozhraní API, která přihlášení Azure Active Directory (Azure AD) u uživatelů v rámci vaší organizace nebo všechny organizace, ne osobní účty. Ale můžete snadno změnit jejich použití koncového bodu Microsoft identity platform tak, že přidáte tento kód do souboru Startup.cs:

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API.
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
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>Ověření tokenu

JwtBearer middlewaru, jako je middleware OpenID Connect ve službě web apps, je přesměrován podle `TokenValidationParameters` token ověří. Token se dešifrují (podle potřeby), deklarace identity se extrahují a podpis se neověřuje. Middleware pak ověří token kontrolou pro tato data:

- Je určená pro webové rozhraní API (cílová skupina).
- Byl vydán pro aplikaci, která má povoleno volat webové rozhraní API (sub).
- Byl vydán token služby důvěryhodné zabezpečení (STS) (issuer).
- Svého životního cyklu je v rozsahu (vypršení platnosti).
- Nebylo manipulováno se (podpis).

Také může být speciální ověření. Například je možné ověřit, že jsou důvěryhodná podpisové klíče (po vložení do tokenu) a že není se odesílal token. A konečně některé protokoly vyžadují konkrétní ověření.

### <a name="validators"></a>Validátory

Ověřovací kroky se zaznamenají v validátory, které jsou všechny na [IdentityModel rozšíření společnosti Microsoft pro .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) open source knihovny, do jednoho zdrojového souboru: [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs).

Validátory jsou popsané v této tabulce:

| Program pro ověření | Popis |
|---------|---------|
| `ValidateAudience` | Zajišťuje, že token, který je pro aplikaci, která ověří token (pro mě). |
| `ValidateIssuer` | Zajišťuje, že token, který byl vydán důvěryhodné služby tokenů zabezpečení (od uživatele, který je důvěryhodný). |
| `ValidateIssuerSigningKey` | Zajišťuje ověřování tokenu vztahy důvěryhodnosti aplikace klíč, který se použil k podepsání token. (Kde klíče je vložená do tokenu zvláštní případ. Obvykle nevyžadováno.) |
| `ValidateLifetime` | Zajišťuje, že je token platný stále (nebo již). Validátor ověří, zda se doba platnosti tokenu (`notbefore` a `expires` deklarace identity) je v dosahu. |
| `ValidateSignature` | Zajišťuje, že token nebylo manipulováno. |
| `ValidateTokenReplay` | Zajišťuje, že token, který není znovu přehrát. (Zvláštní případ pro některé jednorázově používající protokoly). |

Validátory jsou všechny přidružené vlastnosti `TokenValidationParameters` třídy, se inicializuje z konfigurace ASP.NET/ASP.NET Core. Ve většině případů nebudete muset změnit parametry. Existuje jedna výjimka, pro aplikace, které nejsou jeden tenantů. (To znamená, webové aplikace, které akceptovat uživatele z jakékoli organizace nebo osobní účty Microsoft.) V takovém případě musí být Vystavitel ověřen.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Ověření oborů a role aplikace ve vašem kódu](scenario-protected-web-api-verification-scope-app-roles.md)
