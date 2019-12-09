---
title: Konfigurace webového rozhraní API, které volá webová rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Naučte se vytvářet webové rozhraní API, které volá webová rozhraní API (konfigurace kódu aplikace).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 219724186e3fa69fec35e89435af495b662c871d
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919745"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>Webové rozhraní API, které volá rozhraní Web API – konfigurace kódu

Po zaregistrování webového rozhraní API můžete nakonfigurovat kód pro aplikaci.

Kód pro konfiguraci webového rozhraní API tak, aby volal podřízená webová rozhraní API vytvořená na začátku kódu, který slouží k ochraně webového rozhraní API. Další informace najdete v tématu [Protected Web API – konfigurace aplikace](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Kód přihlášený k odběru OnTokenValidated

Nad konfigurací kódu pro všechna chráněná webová rozhraní API se musíte přihlásit k odběru ověření nosiče, který se obdrží při volání rozhraní API:

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform (a.k.k AAD v2.0)
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">Service collection to which to add authentication</param>
/// <param name="configuration">Configuration</param>
/// <returns></returns>
public static IServiceCollection AddProtectedApiCallsWebApis(this IServiceCollection services,
                                                             IConfiguration configuration,
                                                             IEnumerable<string> scopes)
{
    services.AddTokenAcquisition();
    services.Configure<JwtBearerOptions>(AzureADDefaults.JwtBearerAuthenticationScheme, options =>
    {
        // When an access token for our own web API is validated, we add it 
        // to MSAL.NET's cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache, and also handles the incremental consent 
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>Tok On-Behalf-Of

Metoda AddAccountToCacheFromJwt () potřebuje:

- Vytvoří instanci klientské aplikace důvěrné pro MSAL.
- Zavolejte `AcquireTokenOnBehalf` k výměně nosných tokenů, který získal klient pro webové rozhraní API, proti nosnému tokenu pro stejného uživatele, ale pro naše rozhraní API pro volání rozhraní API pro příjem dat.

### <a name="instantiate-a-confidential-client-application"></a>Vytvoření instance aplikace důvěrného klienta

Tento tok je k dispozici pouze v důvěrném toku klienta, aby chráněné webové rozhraní API poskytovalo přihlašovací údaje klienta (tajný klíč klienta nebo certifikát) do [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) prostřednictvím `WithClientSecret` nebo `WithCertificate`ch metod, v uvedeném pořadí.

![image](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```CSharp
IConfidentialClientApplication app;

#if !VariationWithCertificateCredentials
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
           .WithClientSecret(config.ClientSecret)
           .Build();
#else
// Building the client credentials from a certificate
X509Certificate2 certificate = ReadCertificate(config.CertificateName);
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
    .WithCertificate(certificate)
    .Build();
#endif
```

Místo toho můžou důvěrné klientské aplikace také prokázat svoji identitu pomocí kontrolních výrazů klienta.
Tento rozšířený scénář je podrobně popsán v [kontrolním výrazu klienta](msal-net-client-assertions.md) .

### <a name="how-to-call-on-behalf-of"></a>Jak volat jménem

Volání on-of (OBO) je provedeno voláním metody [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) na rozhraní `IConfidentialClientApplication`.

`UserAssertion` je sestaven z nosných tokenů přijatých webovým rozhraním API ze svých vlastních klientů. Existují [dva konstruktory](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet), které přebírají NOSNÝ token JWT, a jeden, který přijímá libovolný druh kontrolního výrazu (jiný druh tokenu zabezpečení, který typ je pak uveden v dalším parametru s názvem `assertionType`).

![image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

V praxi se tok OBO často používá k získání tokenu pro rozhraní API pro příjem dat a jeho uložení do mezipaměti tokenů uživatele MSAL.NET, aby ostatní části webového rozhraní API mohly později zavolat na [přepsání](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) ``AcquireTokenOnSilent`` pro volání rozhraní API pro příjem dat. Toto volání má vliv na obnovení tokenů, pokud je to potřeba.

```CSharp
private void AddAccountToCacheFromJwt(IEnumerable<string> scopes, JwtSecurityToken jwtToken, ClaimsPrincipal principal, HttpContext httpContext)
{
    try
    {
        UserAssertion userAssertion;
        IEnumerable<string> requestedScopes;
        if (jwtToken != null)
        {
            userAssertion = new UserAssertion(jwtToken.RawData, "urn:ietf:params:oauth:grant-type:jwt-bearer");
            requestedScopes = scopes ?? jwtToken.Audiences.Select(a => $"{a}/.default");
        }
        else
        {
            throw new ArgumentOutOfRangeException("tokenValidationContext.SecurityToken should be a JWT Token");
        }

        // Create the application
        var application = BuildConfidentialClientApplication(httpContext, principal);

        // .Result to make sure that the cache is filled-in before the controller tries to get access tokens
        var result = application.AcquireTokenOnBehalfOf(requestedScopes.Except(scopesRequestedByMsalNet),
                                                        userAssertion)
                                .ExecuteAsync()
                                .GetAwaiter().GetResult();
     }
     catch (MsalException ex)
     {
         Debug.WriteLine(ex.Message);
         throw;
     }
}
```

Můžete se také podívat na příklad pro implementaci toku v [NodeJS a Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocol (Protokol)

Další informace o protokolu na úrovni služby najdete v článku [Microsoft Identity Platform a OAuth 2,0 na základě toku](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Získání tokenu pro aplikaci](scenario-web-api-call-api-acquire-token.md)
