---
title: Získání tokenů pro volání webového rozhraní API (aplikace daemon) – platforma identit Microsoftu | Azure
description: Zjistěte, jak vytvořit aplikaci pro daemon, která volá webová api (získávání tokenů)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: d755573b53eb63d85165fb73fe4b97298dbeff09
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868995"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Daemon aplikace, která volá webová API - získat token

Po vytvoření důvěrné klientské aplikace můžete získat token pro aplikaci `AcquireTokenForClient`voláním , předáním oboru a volitelně vynucením aktualizace tokenu.

## <a name="scopes-to-request"></a>Obory, které chcete požádat

Obor, který má být požadován pro tok pověření `/.default`klienta, je název prostředku následovaný písmenem . Tento zápis říká Azure Active Directory (Azure AD) používat *oprávnění na úrovni aplikace* deklarované staticky během registrace aplikace. Tato oprávnění rozhraní API musí být také udělena správcem klienta.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="python"></a>[Python](#tab/python)

V MSAL Pythonu vypadá konfigurační soubor jako tento fragment kódu:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="java"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="azure-ad-v10-resources"></a>Prostředky Azure AD (v1.0)

Obor používaný pro pověření klienta by měl být `/.default`vždy ID prostředku následované .

> [!IMPORTANT]
> Když MSAL požaduje přístupový token pro prostředek, který přijímá přístupový token verze 1.0, Azure AD analyzuje požadovanou cílovou skupinu z požadovaného oboru tím, že vezme vše před posledním lomítkem a použije ho jako identifikátor prostředku.
> Pokud tedy, stejně jako Azure SQL Database (**https:\//database.windows.net),** prostředek očekává cílovou skupinu, která končí `https://database.windows.net//.default`lomítkem (pro Azure SQL Database), `https://database.windows.net/`budete muset požádat o rozsah . (Všimněte si dvojitélohože.) Viz také MSAL.NET problém [#747: Stopovací lomítko adresy URL prostředku je vynecháno, což způsobilo selhání sql auth](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient API

Chcete-li získat token pro aplikaci, budete používat `AcquireTokenForClient` nebo jeho ekvivalent, v závislosti na platformě.

# <a name="net"></a>[.NET](#tab/dotnet)

```csharp
using Microsoft.Identity.Client;

// With client credentials flows, the scope is always of the shape "resource/.default" because the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator.
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application doesn't have sufficient permissions.
    // - Did you declare enough app permissions during app creation?
    // - Did the tenant admin grant permissions to the application?
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be in the form "https://resourceurl/.default"
    // Mitigation: Change the scope to be as expected.
}
```

# <a name="python"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# First, the code looks up a token from the cache.
# Because we're looking for a token for the current app, not for a user,
# use None for the account parameter.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token.
    print(result["token_type"])
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You might need this when reporting a bug.
```

# <a name="java"></a>[Java](#tab/java)

Tento kód je extrahován ze [vzorků dev Jazyka MSAL Java](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/).

```Java
private static IAuthenticationResult acquireToken() throws Exception {

     // Load token cache from file and initialize token cache aspect. The token cache will have
     // dummy data, so the acquireTokenSilently call will fail.
     TokenCacheAspect tokenCacheAspect = new TokenCacheAspect("sample_cache.json");

     IClientCredential credential = ClientCredentialFactory.createFromSecret(CLIENT_SECRET);
     ConfidentialClientApplication cca =
             ConfidentialClientApplication
                     .builder(CLIENT_ID, credential)
                     .authority(AUTHORITY)
                     .setTokenCacheAccessAspect(tokenCacheAspect)
                     .build();

     IAuthenticationResult result;
     try {
         SilentParameters silentParameters =
                 SilentParameters
                         .builder(SCOPE)
                         .build();

         // try to acquire token silently. This call will fail since the token cache does not
         // have a token for the application you are requesting an access token for
         result = cca.acquireTokenSilently(silentParameters).join();
     } catch (Exception ex) {
         if (ex.getCause() instanceof MsalException) {

             ClientCredentialParameters parameters =
                     ClientCredentialParameters
                             .builder(SCOPE)
                             .build();

             // Try to acquire a token. If successful, you should see
             // the token information printed out to console
             result = cca.acquireToken(parameters).join();
         } else {
             // Handle other exceptions accordingly
             throw ex;
         }
     }
     return result;
 }
```

---

### <a name="protocol"></a>Protocol (Protokol)

Pokud ještě nemáte knihovnu pro zvolený jazyk, můžete protokol použít přímo:

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>První případ: Přístup k žádosti o token pomocí sdíleného tajného klíče

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>Druhý případ: Přístup k žádosti o token pomocí certifikátu

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_id=97e0a5b7-d745-40b6-94fe-5f77d35c6e05
&client_assertion_type=urn%3Aietf%3Aparams%3Aoauth%3Aclient-assertion-type%3Ajwt-bearer
&client_assertion=eyJhbGciOiJSUzI1NiIsIng1dCI6Imd4OHRHeXN5amNScUtqRlBuZDdSRnd2d1pJMCJ9.eyJ{a lot of characters here}M8U3bSUKKJDEg
&grant_type=client_credentials
```

Další informace naleznete v dokumentaci k protokolu: [Platforma identit společnosti Microsoft a tok pověření klienta OAuth 2.0](v2-oauth2-client-creds-grant-flow.md).

## <a name="application-token-cache"></a>Mezipaměť tokenů aplikace

V MSAL.NET `AcquireTokenForClient` používá mezipaměť tokenů aplikace. (Všechny ostatní metody AcquireToken*XX* používají mezipaměť tokenů uživatele.) Nevolejte `AcquireTokenSilent` před voláním `AcquireTokenForClient`, `AcquireTokenSilent` protože používá mezipaměť *tokenů uživatele.* `AcquireTokenForClient`zkontroluje samotnou mezipaměť tokenů *aplikace* a aktualizuje ji.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="did-you-use-the-resourcedefault-scope"></a>Použili jste obor resource/.default?

Pokud se zobrazí chybová zpráva, že jste použili neplatný `resource/.default` obor, pravděpodobně jste nepoužili obor.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Zapomněli jste poskytnout souhlas správce? Daemon aplikace potřebují!

Pokud získáte **nedostatečná oprávnění k dokončení chyby operace** při volání rozhraní API, musí správce klienta udělit oprávnění k aplikaci. Viz krok 6 Registrace klientské aplikace výše.
Obvykle se zobrazí chyba, která vypadá jako tato chyba:

```json
Failed to call the web API: Forbidden
Content: {
  "error": {
    "code": "Authorization_RequestDenied",
    "message": "Insufficient privileges to complete the operation.",
    "innerError": {
      "request-id": "<guid>",
      "date": "<date>"
    }
  }
}
```

## <a name="next-steps"></a>Další kroky

# <a name="net"></a>[.NET](#tab/dotnet)

> [!div class="nextstepaction"]
> [Aplikace Daemon - volání webového rozhraní API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Aplikace Daemon - volání webového rozhraní API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Aplikace Daemon - volání webového rozhraní API](https://docs.microsoft.com/azure/active-directory/develop/scenario-daemon-call-api?tabs=java)

---
