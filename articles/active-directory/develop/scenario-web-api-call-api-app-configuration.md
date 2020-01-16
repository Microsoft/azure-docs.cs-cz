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
ms.openlocfilehash: 1e54e26bba1618a3b5835480ed1b1fe698bc8db4
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76043429"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Webové rozhraní API, které volá webová rozhraní API: Konfigurace kódu

Po zaregistrování webového rozhraní API můžete nakonfigurovat kód pro aplikaci.

Kód, který použijete ke konfiguraci webového rozhraní API tak, aby volal podřízená webová rozhraní API vytvořená na začátku kódu, který se používá k ochraně webového rozhraní API. Další informace najdete v tématu [chráněné webové rozhraní API: Konfigurace aplikace](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Kód přihlášený k odběru OnTokenValidated

Nad konfigurací kódu pro všechna chráněná webová rozhraní API se musíte přihlásit k odběru ověření tokenu nosiče, který obdržíte při volání rozhraní API:

```csharp
/// <summary>
/// Protects the web API with the Microsoft identity platform, or Azure Active Directory (Azure AD) developer platform
/// This supposes that the configuration files have a section named "AzureAD"
/// </summary>
/// <param name="services">The service collection to which to add authentication</param>
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
        // to the MSAL.NET cache so that it can be used from the controllers.
        options.Events = new JwtBearerEvents();

        options.Events.OnTokenValidated = async context =>
        {
            context.Success();

            // Adds the token to the cache and handles the incremental consent 
            // and claim challenges
            AddAccountToCacheFromJwt(context, scopes);
            await Task.FromResult(0);
        };
    });
    return services;
}
```

## <a name="on-behalf-of-flow"></a>Tok za běhu

Metoda AddAccountToCacheFromJwt () potřebuje:

- Vytvořte instanci důvěrné klientské aplikace MSAL (Microsoft Authentication Library).
- Volání `AcquireTokenOnBehalf` metody. Toto volání vyměňuje nosný token, který byl získán klientem pro webové rozhraní API proti nosnému tokenu pro stejného uživatele, ale má volání rozhraní API pro jedno rozhraní API.

### <a name="instantiate-a-confidential-client-application"></a>Vytvoření instance aplikace důvěrného klienta

Tento tok je dostupný jenom v důvěrném toku klienta, takže chráněné webové rozhraní API poskytuje přihlašovací údaje klienta (tajný klíč klienta nebo certifikát) ke [třídě ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) prostřednictvím metody `WithClientSecret` nebo `WithCertificate`.

![Seznam metod IConfidentialClientApplication](https://user-images.githubusercontent.com/13203188/55967244-3d8e1d00-5c7a-11e9-8285-a54b05597ec9.png)

```csharp
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

Místo toho, aby bylo možné prokázat svoji identitu prostřednictvím tajného klíče klienta nebo certifikátu, můžou důvěrné klientské aplikace prokázat svoji identitu pomocí kontrolních výrazů klienta.
Další informace o tomto pokročilém scénáři najdete v tématu [důvěrné kontrolní výrazy klienta](msal-net-client-assertions.md).

### <a name="how-to-call-on-behalf-of"></a>Jak volat jménem

OBO volání provádíte voláním [metody AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) na rozhraní `IConfidentialClientApplication`.

Třída `UserAssertion` je sestavena z nosných tokenů, který webový rozhraní API přijal z vlastních klientů. Existují [dva konstruktory](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet):
* Ten, který přebírá nosný token JSON Web Token (JWT)
* Ten, který přebírá libovolný typ uživatelského kontrolního výrazu, je jiný druh tokenu zabezpečení, jehož typ je pak uveden v dalším parametru s názvem `assertionType`

![Vlastnosti a metody UserAssertion](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

V praxi se tok OBO často používá k získání tokenu pro rozhraní API pro příjem dat a jeho uložení do mezipaměti tokenů uživatele MSAL.NET. To provedete tak, aby ostatní části webového rozhraní API mohly později zavolat na [přepsání](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) ``AcquireTokenOnSilent`` pro volání rozhraní API pro příjem dat. Toto volání má vliv na obnovení tokenů, pokud je to potřeba.

```csharp
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

        // .Result to make sure that the cache is filled in before the controller tries to get access tokens
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

Můžete také zobrazit příklad implementace toku OBO v [Node. js a Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocol (Protokol)

Další informace o protokolu OBO naleznete [v tématu Microsoft Identity Platform a OAuth 2,0 na základě toku](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Webové rozhraní API, které volá webová rozhraní API: Získá token pro aplikaci.](scenario-web-api-call-api-acquire-token.md)
