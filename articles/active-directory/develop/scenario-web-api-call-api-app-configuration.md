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
ms.date: 09/26/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d1ab7da9753642093505fa91ea76b9327612a6ac
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99582362"
---
# <a name="a-web-api-that-calls-web-apis-code-configuration"></a>Webové rozhraní API, které volá webová rozhraní API: Konfigurace kódu

Po zaregistrování webového rozhraní API můžete nakonfigurovat kód pro aplikaci.

Kód, který použijete ke konfiguraci webového rozhraní API tak, aby volal podřízená webová rozhraní API vytvořená na začátku kódu, který se používá k ochraně webového rozhraní API. Další informace najdete v tématu [chráněné webové rozhraní API: Konfigurace aplikace](scenario-protected-web-api-app-configuration.md).

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

## <a name="microsoftidentityweb"></a>Microsoft. identity. Web

Microsoft doporučuje při vývoji ASP.NET Coreho rozhraní API s voláním pro podřízená webová rozhraní API použít balíček NuGet [Microsoft. identity. Web](https://www.nuget.org/packages/Microsoft.Identity.Web) . Viz [chráněné webové rozhraní API: Konfigurace kódu | Microsoft. identity. Web](scenario-protected-web-api-app-configuration.md#microsoftidentityweb) pro rychlou prezentaci této knihovny v kontextu webového rozhraní API.

## <a name="client-secrets-or-client-certificates"></a>Klientské tajné klíče nebo klientské certifikáty

Vzhledem k tomu, že vaše webové rozhraní API nyní volá webové rozhraní API pro příjem dat, zadejte tajný klíč klienta nebo klientský certifikát do *appsettings.js* v souboru. Můžete také přidat oddíl, který určuje:

- Adresa URL webového rozhraní API pro příjem dat
- Rozsahy vyžadované pro volání rozhraní API

V následujícím příkladu obsahuje `GraphBeta` oddíl tato nastavení.

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
 },
 "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
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
  },
  "GraphBeta": {
    "BaseUrl": "https://graph.microsoft.com/beta",
    "Scopes": "user.read"
  }
}
```

Microsoft. identity. Web nabízí několik způsobů, jak popsat certifikáty, jak pomocí konfigurace, tak pomocí kódu. Podrobnosti najdete v tématu [Microsoft. identity. Web wiki – používání certifikátů](https://github.com/AzureAD/microsoft-identity-web/wiki/Using-certificates) na GitHubu.

## <a name="startupcs"></a>Startup.cs

Vaše webové rozhraní API bude potřebovat získat token pro rozhraní API pro příjem dat. Zadejte ho přidáním `.EnableTokenAcquisitionToCallDownstreamApi()` řádku po `.AddMicrosoftIdentityWebApi(Configuration)` . Tento řádek zveřejňuje `ITokenAcquisition` službu, kterou můžete použít v akcích kontroleru nebo stránek. Jak vidíte v následujících dvou odrážekch, můžete dokonce dělat jednodušší. Budete také muset zvolit implementaci mezipaměti tokenů, například `.AddInMemoryTokenCaches()` v *Startup.cs*:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Pokud nechcete získat token sami, *Microsoft. identity. Web* poskytuje dva mechanismy pro volání webového rozhraní API pro příjem dat z jiného rozhraní API. Možnost, kterou zvolíte, závisí na tom, jestli chcete volat Microsoft Graph nebo jiné rozhraní API.

### <a name="option-1-call-microsoft-graph"></a>Možnost 1: volání Microsoft Graph

Pokud chcete volat Microsoft Graph, Microsoft. identity. Web vám umožní přímo použít `GraphServiceClient` (zveřejněné v sadě Microsoft Graph SDK) ve svých akcích rozhraní API. Postup vystavení Microsoft Graph:

1. Do projektu přidejte balíček NuGet [Microsoft. identity. Web. MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) .
1. Přidejte `.AddMicrosoftGraph()` po `.EnableTokenAcquisitionToCallDownstreamApi()` v souboru *Startup.cs* . `.AddMicrosoftGraph()` má několik přepsání. Pomocí přepsání, které přebírá konfigurační oddíl jako parametr, se kód změní na:

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, Configuration.GetSection("AzureAd"))
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddMicrosoftGraph(Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

### <a name="option-2-call-a-downstream-web-api-other-than-microsoft-graph"></a>Možnost 2: volání podřízeného webového rozhraní API kromě Microsoft Graph

Chcete-li volat jiné než Microsoft Graph rozhraní API, poskytuje *Microsoft. identity. Web* `.AddDownstreamWebApi()` , který vyžaduje tokeny a volá webové rozhraní API pro příjem dat.

```csharp
using Microsoft.Identity.Web;

public class Startup
{
  // ...
  public void ConfigureServices(IServiceCollection services)
  {
  // ...
  services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
          .AddMicrosoftIdentityWebApi(Configuration, "AzureAd")
            .EnableTokenAcquisitionToCallDownstreamApi()
               .AddDownstreamWebApi("MyApi", Configuration.GetSection("GraphBeta"))
            .AddInMemoryTokenCaches();
   // ...
  }
  // ...
}
```

Stejně jako u Web Apps můžete zvolit různé implementace mezipaměti tokenů. Podrobnosti najdete v tématu [serializace mezipaměti webového tokenu Microsoft Identity](https://aka.ms/ms-id-web/token-cache-serialization) na GitHubu.

Následující obrázek znázorňuje různé možnosti *Microsoft. identity. Web* a jejich dopad na soubor *Startup.cs* :

:::image type="content" source="media/scenarios/microsoft-identity-web-startup-cs.svg" alt-text="Blokový diagram znázorňující možnosti konfigurace služby ve spouštěcí tečkě C S pro volání webového rozhraní API a určení implementace mezipaměti tokenů":::

> [!NOTE]
> Chcete-li plně pochopit příklady kódu, Seznamte se s [ASP.NET Core základy](/aspnet/core/fundamentals)a zejména pomocí injektáže a [možností](/aspnet/core/fundamentals/configuration/options) [závislosti](/aspnet/core/fundamentals/dependency-injection) .

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

Můžete si také prohlédnout příklad implementace toku OBO v [Node.js a Azure Functions](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions/blob/master/Function/MyHttpTrigger/index.js#L61).

## <a name="protocol"></a>Protokol

Další informace o protokolu OBO najdete v tématu [Flow Microsoft Identity Platform a OAuth 2,0](./v2-oauth2-on-behalf-of-flow.md).

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku v tomto scénáři, [Získejte token pro aplikaci](scenario-web-api-call-api-acquire-token.md).
