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
ms.date: 08/05/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e9faea3462ae953e474b5053b651808b03f07c23
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88855456"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Webové rozhraní API, které volá webová rozhraní API: Konfigurace kódu

Po zaregistrování webového rozhraní API můžete nakonfigurovat kód pro aplikaci.

Kód, který použijete ke konfiguraci webového rozhraní API tak, aby volal podřízená webová rozhraní API vytvořená na začátku kódu, který se používá k ochraně webového rozhraní API. Další informace najdete v tématu [chráněné webové rozhraní API: Konfigurace aplikace](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="client-secrets-or-client-certificates"></a>Klientské tajné klíče nebo klientské certifikáty

Vzhledem k tomu, že vaše webové rozhraní API nyní volá webové rozhraní API pro příjem dat, je nutné zadat tajný klíč klienta nebo klientský certifikát v *appsettings.js* souboru.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"
  
   // To call an API
   "ClientSecret": "[Copy the client secret added to the app from the Azure portal]",
   "ClientCertificates": [
  ]
 }
}
```

Místo tajného kódu klienta můžete zadat klientský certifikát. Následující fragment kódu ukazuje použití certifikátu uloženého v Azure Key Vault.

```JSON
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    "ClientId": "[Client_id-of-web-api-eg-2ec40e65-ba09-4853-bcde-bcb60029e596]",
    "TenantId": "common"
  
   // To call an API
   "ClientCertificates": [
      {
        "SourceType": "KeyVault",
        "KeyVaultUrl": "https://msidentitywebsamples.vault.azure.net",
        "KeyVaultCertificateName": "MicrosoftIdentitySamplesCert"
      }
  ]
 }
}
```

Microsoft. identity. Web nabízí několik způsobů, jak popsat certifikáty, jak pomocí konfigurace, tak pomocí kódu. Podrobnosti najdete v tématu [Microsoft. identity. Web wiki – používání certifikátů](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) na GitHubu.

## <a name="startupcs"></a>Startup.cs

Pokud chcete, aby vaše webové rozhraní API volalo webové rozhraní API pro příjem dat, použijte Microsoft. identity. Web, přidejte `.EnableTokenAcquisitionToCallDownstreamApi()` řádek po `.AddMicrosoftIdentityWebApi(Configuration)` a pak zvolte implementaci mezipaměti tokenů, například `.AddInMemoryTokenCaches()` v *Startup.cs*:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  ...
  public void ConfigureServices(IServiceCollection services)
  {
   // ...
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddMicrosoftIdentityWebApi(Configuration, "AzureAd")
                .EnableTokenAcquisitionToCallDownstreamApi()
                .AddInMemoryTokenCaches();
  // ...
  }
  // ...
}
```

Stejně jako u Web Apps můžete zvolit různé implementace mezipaměti tokenů. Podrobnosti najdete v tématu [Microsoft Identity web wiki – serializace mezipaměti tokenu](https://aka.ms/ms-id-web/token-cache-serialization) na GitHubu.

# <a name="java"></a>[Java](#tab/java)

Tok spouštěný za běhu (OBO) slouží k získání tokenu pro volání webového rozhraní API pro příjem dat. V tomto toku vaše webové rozhraní API obdrží token nosiče s delegovanými oprávněními z klientské aplikace a potom tento token vymění pro jiný přístupový token pro volání webového rozhraní API pro příjem dat.

Následující kód používá rozhraní zabezpečení pružiny `SecurityContextHolder` ve webovém rozhraní API k získání ověřeného nosného tokenu. Pak používá knihovnu Java MSAL k získání tokenu pro rozhraní API pro příjem dat pomocí `acquireToken` volání s `OnBehalfOfParameters` . MSAL ukládá token do mezipaměti, aby následné volání rozhraní API mohla použít `acquireTokenSilently` k získání tokenu v mezipaměti.

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

Můžete si také prohlédnout příklad implementace toku OBO v [Node.js a Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/MiddleTierAPI/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protokol

Další informace o protokolu OBO naleznete [v tématu Microsoft Identity Platform a OAuth 2,0 na základě toku](./v2-oauth2-on-behalf-of-flow.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Webové rozhraní API, které volá webová rozhraní API: Získá token pro aplikaci.](scenario-web-api-call-api-acquire-token.md)