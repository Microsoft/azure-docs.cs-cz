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
ms.openlocfilehash: e206cb29338445e30a7462bcbaf0079236e75510
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65074962"
---
# <a name="protected-web-api---code-configuration"></a>Chráněné webové rozhraní API – konfigurace kódu

Kód pro chráněné webové rozhraní API se úspěšně nakonfiguroval, potřebujete pochopit, co dělá rozhraní API služby chráněný, co je potřeba nakonfigurovat nosného tokenu a ověření tokenu.

## <a name="what-makes-aspnetaspnet-core-apis-protected"></a>Co dělá rozhraní API Core ASP.NET/ASP.NET chráněná?

Stejně jako webových aplikací ASP.NET/ASP.NET základní webové rozhraní API se "chráněný" protože jejich akce kontroleru mají předponu `[Authorize]` atribut. Proto akce kontroleru lze volat pouze pokud se volá rozhraní API s identitou, která má oprávnění.

Vezměte v úvahu následující otázky:

- Jak webové rozhraní API znát identitu aplikace, která volá (pouze aplikace může volat webové rozhraní API)?
- Pokud aplikace volá webové rozhraní API jménem uživatele, co je identita uživatele?

## <a name="bearer-token"></a>Nosný token

Informace o identitě aplikace a informace o uživateli (Pokud je webová aplikace přijímá volání služba služba z démona aplikace), se nachází v nosný token, který je nastaven v hlavičce při volání aplikace.

Tady je C# příklad kódu, který ukazuje volání rozhraní API po získání token pomocí MSAL.NET klienta.

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
> Nosný token byl požadován klientské aplikace do koncového bodu Microsoft identity platform **pro webové rozhraní API**. Webové rozhraní API je pouze aplikace, která by měl ověřit token a podívejte se na deklarace identity, které obsahuje. Klientské aplikace by nikdy podívejte se na deklarace identity v tokenech (webového rozhraní API může rozhodování o tom, v budoucnu, kterou vyžaduje, že token zašifrovaná a to přeruší klientské aplikace, které lze odhalit otevřete přístupové tokeny).

## <a name="jwtbearer-configuration"></a>Konfigurace JwtBearer

Tato část popisuje, co je potřeba nakonfigurovat nosný token.

### <a name="config-file"></a>Konfigurační soubor

```Json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    /*
      You need specify the TenantId only if you want to accept access tokens from a single tenant
     (line of business app)
      Otherwise you can leave them set to common
      This can be:
      - A guid (Tenant ID = Directory ID)
      - 'common' (Any organization and personal accounts)
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

Když je volána aplikace v podniku akce kontroleru `[Authorize]` atributu ASP.NET/ASP.NET core zkoumá nosného tokenu v autorizační hlavičce požadavku volání a extrahuje přístupový token, který je předán JwtBearer Middleware volá modelu rozšíření identit společnosti Microsoft pro .NET.

V ASP.NET Core se inicializuje tento middleware `Startup.cs` souboru.

```CSharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
```

Middleware přidává do webového rozhraní API podle následujících pokynů:

```CSharp
 services.AddAzureAdBearer(options => Configuration.Bind("AzureAd", options));
```

 Šablony ASP.NET Core v současné době vytvoření služby Azure AD v1.0 webového rozhraní API. Však můžete snadno změnit jejich použití koncového bodu Microsoft identity platform přidáním následujícího kódu `Startup.cs` souboru.

```CSharp
services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
{
    // This is a Microsoft identity platform v2.0 web API
    options.Authority += "/v2.0";

    // The web API accepts as audiences are both the Client ID (options.Audience) and api://{ClientID}
    options.TokenValidationParameters.ValidAudiences = new []
    {
     options.Audience,
     $"api://{options.Audience}"
    };

    // Instead of using the default validation (validating against a single tenant,
    // as we do in line of business apps),
    // we inject our own multi-tenant validation logic (which even accepts both V1 and V2 tokens)
    options.TokenValidationParameters.IssuerValidator = AadIssuerValidator.ValidateAadIssuer;
});
```

## <a name="token-validation"></a>Ověření tokenu

Směřuje JwtBearer middlewaru, jako je middleware OpenID Connect ve službě web apps, tak `TokenValidationParameters` token ověří. Token se dešifrují (v případě potřeby), deklarace identity se extrahují a podpis se neověřuje. Token se potom ověřit kontrolou následující data:

- Je určená pro webové rozhraní API (cílová skupina)
- Byl vydán pro aplikaci, která může volat webové rozhraní API (sub)
- Bylo vydáno podle udržitelný tokenů zabezpečení serveru (STS) (issuer)
- Životnost tokenu je v rozsahu (vypršení platnosti)
- Nebylo manipulováno se (podpis)

Také může být speciální ověření. Například je možné ověřit, že jsou důvěryhodná podpisové klíče (po vložení do tokenu) a že není se odesílal token. A konečně některé protokoly vyžadují konkrétní ověření.

### <a name="validators"></a>Validátory

Ověřovací kroky se zaznamenají do validátory, které jsou všechny na [modelu rozšíření identit společnosti Microsoft pro .NET](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) open source knihovny, do jednoho zdrojového souboru: [Microsoft.IdentityModel.Tokens/Validators.cs](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/blob/master/src/Microsoft.IdentityModel.Tokens/Validators.cs)

Validátory jsou popsány v následující tabulce:

| Program pro ověření | Popis |
|---------|---------|
| `ValidateAudience` | Zajišťuje, že token, který je pro aplikaci, která ověří token (pro mě). |
| `ValidateIssuer` | Zajišťuje, že token, který byl vydán důvěryhodné služby tokenů zabezpečení (od uživatele, který je důvěryhodný). |
| `ValidateIssuerSigningKey` | Zajišťuje ověřování tokenu vztahy důvěryhodnosti aplikace klíč, který se použil k podepsání token (kde klíče je vložená do tokenu, obvykle není nutné zvláštní případ). |
| `ValidateLifetime` | Zajišťuje, že je token platný stále (nebo již). Provádí kontroly, který doba platnosti tokenu (`notbefore`, `expires` deklarace identity) je v dosahu. |
| `ValidateSignature` | Zajišťuje, že token nikdo neoprávněně nemanipuloval. |
| `ValidateTokenReplay` | Zajišťuje není přehrály token (zvláštní případ pro některé jednorázově používající protokoly). |

Validátory jsou všechny přidružené vlastnosti `TokenValidationParameters` třídy, se inicializuje z konfigurace ASP.NET/ASP.NET Core. Ve většině případů nebudete muset změnit parametry. Existuje jedna výjimka pro aplikace, které nejsou jeden tenantů (tj. webové aplikace, které akceptovat uživatele z jakékoli organizace nebo osobní účty Microsoft) – v takovém případě musí být Vystavitel ověřen.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přejít do produkčního prostředí](scenario-protected-web-api-production.md)
