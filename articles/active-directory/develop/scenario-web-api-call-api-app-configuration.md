---
title: Konfigurace webového rozhraní API, které volá webová rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Zjistěte, jak vytvořit webové rozhraní API, které volá webová rozhraní API (konfigurace kódu aplikace)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 4129f1a89575c9a0e7cd6a0090168df659356c1b
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885102"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Webové rozhraní API, které volá webová rozhraní API: Konfigurace kódu

Po registraci webového rozhraní API můžete nakonfigurovat kód aplikace.

Kód, který používáte ke konfiguraci webového rozhraní API tak, aby volání podřízené webové rozhraní API staví na vrcholu kódu, který se používá k ochraně webové rozhraní API. Další informace naleznete [v tématu Protected web API: App configuration](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="code-subscribed-to-ontokenvalidated"></a>Kód objednaný k ontokenvalidated

Kromě konfigurace kódu pro všechna chráněná webová rozhraní API se musíte přihlásit k odběru ověření nožního tokenu, který obdržíte při volání vašeho rozhraní API:

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

## <a name="on-behalf-of-flow"></a>Tok jménem

Metoda AddAccountToCacheFromJwt() musí:

- Vytvořte instanci důvěrné klientské aplikace Knihovny ověřování (MSAL) společnosti Microsoft.
- Volání `AcquireTokenOnBehalf` metody. Toto volání výměny nosný token, který byl získán klientem pro webové rozhraní API proti nosný token pro stejného uživatele, ale má volání rozhraní API příjem rozhraní API.

### <a name="instantiate-a-confidential-client-application"></a>Vytvoření instance důvěrné klientské aplikace

Tento tok je k dispozici pouze v toku důvěrnéklienta, tak, aby chráněné webové rozhraní API poskytuje `WithClientSecret` `WithCertificate` pověření klienta (tajný klíč klienta nebo certifikát) do [třídy ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) prostřednictvím metody nebo.

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

Nakonec namísto prokázání jejich identity prostřednictvím tajného klíče klienta nebo certifikátu, důvěrné klientské aplikace mohou prokázat svou identitu pomocí tvrzení klienta.
Další informace o tomto rozšířeném scénáři naleznete [v tématu Důvěrné kontrolní výrazy klienta](msal-net-client-assertions.md).

### <a name="how-to-call-on-behalf-of"></a>Jak volat jménem

Volání On-Behalf-Of (OBO) voláním [metody AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) `IConfidentialClientApplication` v rozhraní.

Třída `UserAssertion` je sestavena z nosného tokenu, který je přijat webovým rozhraním API od vlastních klientů. Existují [dva konstruktory](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet):
* Ten, který má JSON Web Token (JWT) nosič token
* Ten, který přebírá jakýkoli druh kontrolního výrazu uživatele, jiný druh tokenu zabezpečení, jehož typ je pak zadán v dalším parametru s názvem`assertionType`

![Vlastnosti a metody userassertion](https://user-images.githubusercontent.com/13203188/37082180-afc4b708-21e3-11e8-8af8-a6dcbd2dfba8.png)

V praxi tok OBO se často používá k získání tokenu pro rozhraní API pro příjem dat a uložit jej do mezipaměti tokenu MSAL.NET uživatele. Můžete to provést tak, aby ostatní části webového rozhraní ``AcquireTokenOnSilent`` API můžete později volat na [přepsání](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientapplicationbase.acquiretokensilent?view=azure-dotnet) volání navazující rozhraní API. Toto volání má za následek aktualizace tokeny, v případě potřeby.

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
# <a name="java"></a>[Java](#tab/java)

Tok On-behalf-of (OBO) slouží k získání tokenu pro volání rozhraní API pro příjem webu. V tomto toku vaše webové rozhraní API obdrží nosný token s uživateli delegovaná oprávnění z klientské aplikace a pak výměny tohoto tokenu pro jiný přístupový token pro volání podřízené webové rozhraní API.

Níže uvedený kód používá spring `SecurityContextHolder` security framework u webového rozhraní API k získání ověřeného nožního tokenu. Potom používá knihovnu Jazyka Java MSAL k získání `acquireToken` tokenu `OnBehalfOfParameters`pro rozhraní API pro příjem dat pomocí volání s . MSAL ukládá token do mezipaměti tak, `acquireTokenSilently` aby následná volání rozhraní API můžete použít k získání tokenu uloženého v mezipaměti.

```Java
@Component
class MsalAuthHelper {

    @Value("${security.oauth2.client.authority}")
    private String authority;

    @Value("${security.oauth2.client.client-id}")
    private String clientId;

    @Value("${security.oauth2.client.client-secret}")
    private String secret;

    @Autowired
    CacheManager cacheManager;

    private String getAuthToken(){
        String res = null;
        Authentication authentication = SecurityContextHolder.getContext().getAuthentication();

        if(authentication != null){
            res = ((OAuth2AuthenticationDetails) authentication.getDetails()).getTokenValue();
        }
        return res;
    }

    String getOboToken(String scope) throws MalformedURLException {
        String authToken = getAuthToken();

        ConfidentialClientApplication application =
                ConfidentialClientApplication.builder(clientId, ClientCredentialFactory.create(secret))
                        .authority(authority).build();

        String cacheKey = Hashing.sha256()
                .hashString(authToken, StandardCharsets.UTF_8).toString();

        String cachedTokens = cacheManager.getCache("tokens").get(cacheKey, String.class);
        if(cachedTokens != null){
            application.tokenCache().deserialize(cachedTokens);
        }

        IAuthenticationResult auth;
        SilentParameters silentParameters =
                SilentParameters.builder(Collections.singleton(scope))
                        .build();
        auth = application.acquireTokenSilently(silentParameters).join();

        if (auth == null){
            OnBehalfOfParameters parameters =
                    OnBehalfOfParameters.builder(Collections.singleton(scope),
                            new UserAssertion(authToken))
                            .build();

            auth = application.acquireToken(parameters).join();
        }

        cacheManager.getCache("tokens").put(cacheKey, application.tokenCache().serialize());

        return auth.accessToken();
    }
}
```

# <a name="python"></a>[Python](#tab/python)

Tok On-behalf-of (OBO) slouží k získání tokenu pro volání rozhraní API pro příjem webu. V tomto toku vaše webové rozhraní API obdrží nosný token s uživateli delegovaná oprávnění z klientské aplikace a pak výměny tohoto tokenu pro jiný přístupový token pro volání podřízené webové rozhraní API.

Webové rozhraní API Pythonu bude muset použít nějaký middleware k ověření nosné tokenu přijatého od klienta. Webové rozhraní API pak můžete získat přístupový token pro příjem rozhraní [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) API pomocí knihovny MSAL Python voláním metody. Ukázka demonstrující tento tok s MSAL Python ještě není k dispozici.

---

Můžete také zobrazit příklad implementace toku OBO v [Node.js a Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocol (Protokol)

Další informace o protokolu OBO naleznete v [tématu Microsoft identity platformy a OAuth 2.0 On-Behalf-Of toku](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Webové rozhraní API, které volá webová rozhraní API: Získání tokenu pro aplikaci](scenario-web-api-call-api-acquire-token.md)
