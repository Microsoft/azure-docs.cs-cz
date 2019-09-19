---
title: Aplikace démona, která volá webová rozhraní API (získává tokeny pro aplikaci) – Microsoft Identity Platform
description: Naučte se vytvářet aplikace démona, která volá webová rozhraní API (získávání tokenů).
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
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef28520edd8500be0da52996e6484a0407fb03c8
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71056447"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Aplikace démona, která volá webová rozhraní API – získá token.

Jakmile bude důvěrná klientská aplikace vytvořená, můžete získat token pro aplikaci voláním ``AcquireTokenForClient``, předáním oboru a vynucením nebo neobnovením tokenu.

## <a name="scopes-to-request"></a>Rozsahy k vyžádání

Obor pro požadavek na tok přihlašovacích údajů klienta je název prostředku následovaný `/.default`. Tento zápis oznamuje službě Azure AD, aby používala **oprávnění na úrovni aplikace** deklarované staticky během registrace aplikace. Jak už bylo uvedeno dříve, tato oprávnění rozhraní API musí udělit správce klienta.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

# <a name="pythontabpython"></a>[Python](#tab/python)

V MSAL. Python, konfigurační soubor by vypadal jako následující fragment kódu:

```Json
{
    "scope": ["https://graph.microsoft.com/.default"],
}
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
final static String GRAPH_DEFAULT_SCOPE = "https://graph.microsoft.com/.default";
```

---

### <a name="case-of-azure-ad-v10-resources"></a>Případ prostředků Azure AD (v 1.0)

Obor používaný pro přihlašovací údaje klienta by měl vždycky být resourceId + "/.default".

> [!IMPORTANT]
> V případě, že MSAL žádá o přístupový token pro prostředek, který přijímá přístupový token v 1.0, Azure AD analyzuje požadovanou cílovou skupinu z požadovaného oboru tím, že převezme vše před poslední lomítko a použije ho jako identifikátor prostředku.
> Proto pokud by jako Azure SQL ( **https://database.windows.net** ) prostředek očekává cílovou skupinu končící lomítkem (pro Azure SQL: `https://database.windows.net/` ), budete muset požádat o obor `https://database.windows.net//.default` (Všimněte si dvojitého lomítka). Viz také MSAL.NET problém [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Koncové lomítko adresy URL prostředku je vynecháno, což způsobilo selhání ověřování SQL.

## <a name="acquiretokenforclient-api"></a>Rozhraní API pro AcquireTokenForClient

K získání tokenu pro aplikaci použijete `AcquireTokenForClient` nebo ekvivalent v závislosti na platformách.

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

```CSharp
using Microsoft.Identity.Client;

// With client credentials flows the scopes is ALWAYS of the shape "resource/.default", as the
// application permissions need to be set statically (in the portal or by PowerShell), and then granted by
// a tenant administrator
string[] scopes = new string[] { "https://graph.microsoft.com/.default" };

AuthenticationResult result = null;
try
{
 result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
}
catch (MsalUiRequiredException ex)
{
    // The application does not have sufficient permissions
    // - did you declare enough app permissions in during the app creation?
    // - did the tenant admin needs to grant permissions to the application.
}
catch (MsalServiceException ex) when (ex.Message.Contains("AADSTS70011"))
{
    // Invalid scope. The scope has to be of the form "https://resourceurl/.default"
    // Mitigation: change the scope to be as expected !
}
```

# <a name="pythontabpython"></a>[Python](#tab/python)

```Python
# The pattern to acquire a token looks like this.
result = None

# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])

if "access_token" in result:
    # Call a protected API with the access token
    print(result["token_type"])
    print(result["expires_in"])  # You don't normally need to care about this.
                                 # It will be good for at least 5 minutes.
else:
    print(result.get("error"))
    print(result.get("error_description"))
    print(result.get("correlation_id"))  # You may need this when reporting a bug
```

# <a name="javatabjava"></a>[Java](#tab/java)

```Java
ClientCredentialParameters clientCredentialParam = ClientCredentialParameters.builder(
        Collections.singleton(GRAPH_DEFAULT_SCOPE))
        .build();

CompletableFuture<IAuthenticationResult> future = app.acquireToken(clientCredentialParam);

BiConsumer<IAuthenticationResult, Throwable> processAuthResult = (res, ex) -> {
    if (ex != null) {
        System.out.println("Oops! We have an exception - " + ex.getMessage());
    }
    System.out.println("Returned ok - " + res);
    System.out.println("ID Token - " + res.idToken());

    /* call a protected API with res.accessToken() */
};

future.whenCompleteAsync(processAuthResult);
future.join();
```

---

### <a name="protocol"></a>Protocol

Pokud ještě nemáte knihovnu pro svůj jazyk podle vlastního výběru, můžete použít protokol přímo:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>První případ: Žádost o přístupový token se sdíleným tajným klíčem

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1           //Line breaks for clarity
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=535fb089-9ff3-47b6-9bfb-4f1264799865
&scope=https%3A%2F%2Fgraph.microsoft.com%2F.default
&client_secret=qWgdYAmab0YSkuL1qKv5bPX
&grant_type=client_credentials
```

#### <a name="second-case-access-token-request-with-a-certificate"></a>Druhý případ: Žádost o přístupový token s certifikátem

```Text
POST /{tenant}/oauth2/v2.0/token HTTP/1.1               // Line breaks for clarity
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

`AcquireTokenForClient` V MSAL.NET používá **mezipaměť tokenu aplikace** (všechny ostatní metody AcquireTokenXX používají mezipaměť tokenu uživatele) `AcquireTokenSilent` nevolají před voláním `AcquireTokenForClient` jako `AcquireTokenSilent` používá mezipaměť tokenu **uživatele** . `AcquireTokenForClient`kontroluje samotný mezipaměť tokenu **aplikace** a aktualizuje ji.

## <a name="troubleshooting"></a>Řešení potíží

### <a name="did-you-use-the-resourcedefault-scope"></a>Použili jste prostředek/. výchozí obor?

Pokud se zobrazí chybová zpráva s oznámením, že jste použili neplatný rozsah, pravděpodobně jste `resource/.default` obor nepoužívali.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Nezapomněli jste zadat souhlas správce? Aplikace démona vyžaduje!

Pokud při volání rozhraní API **nedostatečná oprávnění k dokončení operace**dojde k chybě, správce klienta musí udělit oprávnění k aplikaci. Viz krok 6 registrace klientské aplikace výše.
Obvykle se zobrazí a chyba podobá následujícímu popisu chyby:

```JSon
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

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Aplikace démona – volání webového rozhraní API](scenario-daemon-call-api.md)