---
title: Webové rozhraní API tohoto volání podřízené webové rozhraní API (konfigurace kódu aplikace) – platforma identit Microsoft
description: Další informace o vytváření webového rozhraní API, že volání webových rozhraní API (konfigurace kódu aplikace)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f62cf65e275d8a9b909bf60103ccbd84e91e4574
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785058"
---
# <a name="web-api-that-calls-web-apis---code-configuration"></a>Webové rozhraní API, že volání webových rozhraní API – konfigurace kódu

Po zaregistrování webového rozhraní API, můžete nakonfigurovat kód aplikace.

Kód ke konfiguraci vašeho webového rozhraní API tak, že volá podřízené webové rozhraní API postavená na kód používaný k ochraně webového rozhraní API. Další informace najdete v tématu [chráněné webové rozhraní API – konfigurace aplikace](scenario-protected-web-api-app-configuration.md).

## <a name="code-subscribed-to-ontokenvalidated"></a>Kód k OnTokenValidated odběru

Nad kód konfigurace pro všechny chráněné webové rozhraní API budete muset přihlásit k ověřování nosného tokenu, který obdržíte, když se volá rozhraní API:

```CSharp
/// <summary>
/// Protects the web API with Microsoft Identity Platform v2.0 (AAD v2.0)
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

Metoda AddAccountToCacheFromJwt() musí:

- Vytvoření instance aplikace MSAL důvěrnému klientovi.
- Volání `AcquireTokenOnBehalf` k výměně nosný token, který byl získán klientem pro webové rozhraní API, proti nosný token pro stejného uživatele, ale pro naše rozhraní API volat podřízené rozhraní API.

### <a name="instantiate-a-confidential-client-application"></a>Vytvoření instance důvěrné klientské aplikace

Tento tok je k dispozici ve službě flow důvěrnému klientovi pouze tak chráněné webové rozhraní API poskytuje přihlašovací údaje pro klienta (tajný kód klienta nebo certifikát) [ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) prostřednictvím `WithClientSecret` nebo `WithCertificate`metody, v uvedeném pořadí.

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

### <a name="how-to-call-on-behalf-of"></a>Jak volat on-behalf-of

Volání (OBO) on-behalf-of se provádí pomocí volání [AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) metodu `IConfidentialClientApplication` rozhraní.

`ClientAssertion` Je vytvořená z tokenu nosiče přijatých webového rozhraní API od svých vlastních klientů. Existují [dva konstruktory](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet), ten, který přebírá tokenu nosiče JWT a jednu, která přijímá libovolný typ kontrolního výrazu uživatele (jiný druh tokenu zabezpečení, který typ je poté zadané v další parametr s názvem `assertionType`).

![image](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

V praxi, tok OBO se často používá k získání tokenu pro příjem dat rozhraní API a ukládání do mezipaměti token uživatele MSAL.NET tak, aby ostatní části webové rozhraní API můžete později volat na [přepíše](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) z ``AcquireTokenOnSilent`` volat podřízené rozhraní API. To má za následek aktualizaci tokeny, v případě potřeby.

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

## <a name="protocol"></a>Protocol

Další informace o protokolu on-behalf-of, naleznete v tématu [platforma identit Microsoft a tok OAuth 2.0 On-Behalf-Of](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Získání tokenu pro aplikaci](scenario-web-api-call-api-acquire-token.md)
