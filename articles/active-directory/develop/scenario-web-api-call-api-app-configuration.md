---
title: Konfigurace webového rozhraní API, které volá webová rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Naučte se vytvářet webové rozhraní API, které volá webová rozhraní API (konfigurace kódu aplikace).
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
ms.openlocfilehash: 38e319efb100d326d55f6f821e7c903306a7c7d0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80991003"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Webové rozhraní API, které volá webová rozhraní API: Konfigurace kódu

Po zaregistrování webového rozhraní API můžete nakonfigurovat kód pro aplikaci.

Kód, který použijete ke konfiguraci webového rozhraní API tak, aby volal podřízená webová rozhraní API vytvořená na začátku kódu, který se používá k ochraně webového rozhraní API. Další informace najdete v tématu [chráněné webové rozhraní API: Konfigurace aplikace](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

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
- Zavolejte `AcquireTokenOnBehalf` metodu. Toto volání vyměňuje nosný token, který byl získán klientem pro webové rozhraní API proti nosnému tokenu pro stejného uživatele, ale má volání rozhraní API pro jedno rozhraní API.

### <a name="instantiate-a-confidential-client-application"></a>Vytvoření instance aplikace důvěrného klienta

Tento tok je dostupný jenom v důvěrném toku klienta, takže chráněné webové rozhraní API poskytuje přihlašovací údaje klienta (tajný klíč klienta nebo certifikát) ke [třídě ConfidentialClientApplicationBuilder](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder) prostřednictvím metody `WithClientSecret` nebo `WithCertificate` .

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

OBO volání provádíte voláním [metody AcquireTokenOnBehalf](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenonbehalfofparameterbuilder) na `IConfidentialClientApplication` rozhraní.

`UserAssertion` Třída je sestavená z tokenu nosiče, který je WEBOVÝm rozhraním API přijatý z jeho vlastních klientů. Existují [dva konstruktory](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.clientcredential.-ctor?view=azure-dotnet):
* Ten, který přebírá nosný token JSON Web Token (JWT)
* Ten, který přebírá libovolný typ uživatelského kontrolního výrazu, je jiný druh tokenu zabezpečení, jehož typ je pak uveden v dalším parametru s názvem.`assertionType`

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
# <a name="java"></a>[Java](#tab/java)

Tok spouštěný za běhu (OBO) slouží k získání tokenu pro volání webového rozhraní API pro příjem dat. V tomto toku vaše webové rozhraní API obdrží token nosiče s delegovanými oprávněními z klientské aplikace a potom tento token vymění pro jiný přístupový token pro volání webového rozhraní API pro příjem dat.

Následující kód používá rozhraní zabezpečení pružiny `SecurityContextHolder` ve webovém rozhraní API k získání ověřeného nosného tokenu. Pak používá knihovnu Java MSAL k získání tokenu pro rozhraní API pro příjem dat pomocí `acquireToken` volání s `OnBehalfOfParameters`. MSAL ukládá token do mezipaměti, aby následné volání rozhraní API mohla použít `acquireTokenSilently` k získání tokenu v mezipaměti.

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

Tok spouštěný za běhu (OBO) slouží k získání tokenu pro volání webového rozhraní API pro příjem dat. V tomto toku vaše webové rozhraní API obdrží token nosiče s delegovanými oprávněními z klientské aplikace a potom tento token vymění pro jiný přístupový token pro volání webového rozhraní API pro příjem dat.

Webové rozhraní API v Pythonu bude muset použít nějaký middleware k ověření, že je nosný token přijatý od klienta. Webové rozhraní API pak může získat přístupový token pro rozhraní API pro příjem dat pomocí knihovny Python MSAL voláním [`acquire_token_on_behalf_of`](https://msal-python.readthedocs.io/en/latest/?badge=latest#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) metody. Příklad použití tohoto rozhraní API najdete v tématu [testovací kód pro Microsoft-Authentication-Library-for-Python na GitHubu](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.2.0/tests/test_e2e.py#L429-L472). Také se zobrazí diskuze o [problému 53](https://github.com/AzureAD/microsoft-authentication-library-for-python/issues/53) ve stejném úložišti pro přístup, který obchází nutnost použití aplikace střední vrstvy.

---

Můžete také zobrazit příklad implementace toku OBO v [Node. js a Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protocol (Protokol)

Další informace o protokolu OBO naleznete [v tématu Microsoft Identity Platform a OAuth 2,0 na základě toku](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Webové rozhraní API, které volá webová rozhraní API: Získá token pro aplikaci.](scenario-web-api-call-api-acquire-token.md)
