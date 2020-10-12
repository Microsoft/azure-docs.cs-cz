---
title: Získání tokenů pro volání webového rozhraní API (aplikace démon) – Microsoft Identity Platform | Azure
description: Naučte se vytvářet aplikace démona, která volá webová rozhraní API (získávání tokenů).
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
ms.openlocfilehash: 13000c5a61dc2c4d49aa395271beddef64d32245
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88119211"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Aplikace démona, která volá webová rozhraní API – získá token.

Po sestavení důvěrné klientské aplikace můžete získat token pro aplikaci voláním `AcquireTokenForClient` , předáním oboru a volitelně vynucením aktualizace tokenu.

## <a name="scopes-to-request"></a>Rozsahy k vyžádání

Obor pro požadavek na tok přihlašovacích údajů klienta je název prostředku následovaný `/.default` . Tento zápis oznamuje službě Azure Active Directory (Azure AD) používání *oprávnění na úrovni aplikace* deklarované staticky během registrace aplikace. Tato oprávnění rozhraní API musí taky udělit správce klienta.

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

### <a name="azure-ad-v10-resources"></a>Prostředky Azure AD (v 1.0)

Obor používaný pro pověření klienta by měl vždy být ID prostředku následovaný `/.default` .

> [!IMPORTANT]
> Když MSAL požádá o přístupový token pro prostředek, který přijímá přístupový token verze 1,0, Azure AD analyzuje požadovanou cílovou skupinu z požadovaného oboru tím, že převezme vše před poslední lomítko a použije ho jako identifikátor prostředku.
> Takže pokud jako Azure SQL Database (**https: \/ /Database.Windows.NET**) očekává prostředek cílovou skupinu, která končí lomítkem (pro Azure SQL Database, `https://database.windows.net/` ), budete muset požádat o obor `https://database.windows.net//.default` . (Poznamenejte si dvojité lomítko.) Viz také MSAL.NET problém [#747: koncové lomítko adresy URL prostředku je vynecháno, což způsobilo selhání ověřování SQL](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747).

## <a name="acquiretokenforclient-api"></a>Rozhraní API pro AcquireTokenForClient

K získání tokenu pro aplikaci použijete `AcquireTokenForClient` nebo její ekvivalent v závislosti na platformě.

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

Tento kód se extrahuje z [MSALch ukázek Java pro vývoj](https://github.com/AzureAD/microsoft-authentication-library-for-java/blob/dev/src/samples/confidential-client/).

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

### <a name="protocol"></a>Protokol

Pokud ještě nemáte knihovnu pro zvolený jazyk, možná budete chtít použít protokol přímo:

#### <a name="first-case-access-the-token-request-by-using-a-shared-secret"></a>První případ: přístup k žádosti o token pomocí sdíleného tajného klíče

```HTTP
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity.
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-the-token-request-by-using-a-certificate"></a>Druhý případ: přístup k žádosti o token pomocí certifikátu

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

Další informace najdete v dokumentaci k protokolu: [Microsoft Identity Platform a tok přihlašovacích údajů klienta OAuth 2,0](v2-oauth2-client-creds-grant-flow.md).

## <a name="application-token-cache"></a>Mezipaměť tokenů aplikace

V MSAL.NET `AcquireTokenForClient` používá mezipaměť tokenů aplikace. (Všechny ostatní metody AcquireToken*XX* používají mezipaměť tokenu uživatele.) Nevolejte `AcquireTokenSilent` před voláním `AcquireTokenForClient` , protože `AcquireTokenSilent` používá mezipaměť tokenu *uživatele* . `AcquireTokenForClient` kontroluje samotný mezipaměť tokenu *aplikace* a aktualizuje ji.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="did-you-use-the-resourcedefault-scope"></a>Použili jste prostředek/. výchozí obor?

Pokud se zobrazí chybová zpráva s oznámením, že jste použili neplatný rozsah, pravděpodobně jste obor nepoužívali `resource/.default` .

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Nezapomněli jste zadat souhlas správce? Aplikace démona vyžaduje!

Pokud získáte **nedostatečná oprávnění k dokončení operace** při volání rozhraní API, správce klienta musí udělit oprávnění k aplikaci. Viz krok 6 registrace klientské aplikace výše.
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
> [Aplikace démona – volání webového rozhraní API](./scenario-daemon-call-api.md?tabs=dotnet)

# <a name="python"></a>[Python](#tab/python)

> [!div class="nextstepaction"]
> [Aplikace démona – volání webového rozhraní API](./scenario-daemon-call-api.md?tabs=python)

# <a name="java"></a>[Java](#tab/java)

> [!div class="nextstepaction"]
> [Aplikace démona – volání webového rozhraní API](./scenario-daemon-call-api.md?tabs=java)

---