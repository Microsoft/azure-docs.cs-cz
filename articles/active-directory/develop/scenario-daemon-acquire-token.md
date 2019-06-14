---
title: Démon procesu aplikace volání webových rozhraní API (získávání tokenů aplikace) – platforma identit Microsoft
description: Zjistěte, jak vytvořit aplikaci pro démona, volá webové rozhraní API (získávání tokenů)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa4f5dc7a5aceaf81f71eacd36d131471a57e5c0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65075367"
---
# <a name="daemon-app-that-calls-web-apis---acquire-a-token"></a>Démon procesu aplikace, která volá webové rozhraní API – získání tokenu

Jakmile je vytvořen důvěrné klientskou aplikaci, můžete získat token pro aplikaci pomocí volání ``AcquireTokenForClient``, předávání oboru a vynucení nebo není aktualizaci tokenu.

## <a name="scopes-to-request"></a>Obory žádosti

Rozsah na žádost o název prostředku je tok klientských přihlašovacích údajů, za nímž následuje `/.default`. Tento typ notation říká službě Azure AD používat **oprávnění na úrovni aplikace** deklarované staticky během registrace aplikace. Také jak je vidět dříve, tato rozhraní API oprávnění musí udělit správce tenanta

### <a name="net"></a>.NET

```CSharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="python"></a>Python

V MSAL. Python, konfigurační soubor by vypadalo podobně jako následující fragment kódu:

```Python
{
    "authority": "https://login.microsoftonline.com/organizations",
    "client_id": "your_client_id",
    "secret": "This is a sample only. You better NOT persist your password."
    "scope": ["https://graph.microsoft.com/.default"]
}
```

### <a name="java"></a>Java

```Java
public final static String KEYVAULT_DEFAULT_SCOPE = "https://vault.azure.net/.default";
```

### <a name="all"></a>Vše

Oborem použitým pro přihlašovací údaje pro klienta by měl vždy být resourceId + "/ hodnotu .default"

### <a name="case-of-v10-resources"></a>Případ v1.0 prostředků

> [!IMPORTANT]
> Azure AD pro MSAL (koncový bod verze 2.0) s dotazem přístupového tokenu pro prostředek přijímání v1.0 přístupový token, analyzuje požadovanou cílovou skupinu z požadovaného oboru převzetím všechno dřív než poslední lomítko a používat jej jako identifikátor prostředku.
> Proto pokud, jako je Azure SQL ( **https://database.windows.net** ) prostředku očekává, že cílové koncové lomítko (pro Azure SQL: `https://database.windows.net/` ), budete muset požádat o oboru `https://database.windows.net//.default` (Poznámka: dvojité lomítka). Viz také MSAL.NET problém [#747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Adresa url prostředku koncové lomítko je vynechán, která způsobila chybu při ověřování sql.

## <a name="acquiretokenforclient-api"></a>AcquireTokenForClient rozhraní API

### <a name="net"></a>.NET

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

#### <a name="application-token-cache"></a>Mezipaměť tokenu aplikace

V MSAL.NET `AcquireTokenForClient` používá **mezipaměť tokenu aplikace** (všechny ostatní metody AcquireTokenXX používat mezipaměť tokenu uživatele) Nevolejte `AcquireTokenSilent` před voláním `AcquireTokenForClient` jako `AcquireTokenSilent` používá **uživatele** token mezipaměti. `AcquireTokenForClient` kontroluje **aplikace** token samotné do mezipaměti a aktualizuje ji.

### <a name="python"></a>Python

```Python
# Firstly, looks up a token from cache
# Since we are looking for token for the current app, NOT for an end user,
# notice we give account parameter as None.
result = app.acquire_token_silent(config["scope"], account=None)

if not result:
    logging.info("No suitable token exists in cache. Let's get a new one from AAD.")
    result = app.acquire_token_for_client(scopes=config["scope"])
```

### <a name="java"></a>Java

```Java
ClientCredentialParameters parameters = ClientCredentialParameters
        .builder(Collections.singleton(KEYVAULT_DEFAULT_SCOPE))
        .build();

CompletableFuture<AuthenticationResult> future = cca.acquireToken(parameters);

// You can complete the future in many different ways. Here we use .get() for simplicity
AuthenticationResult result = future.get();
```

### <a name="protocol"></a>Protocol

Pokud nemáte ještě knihovny pro váš jazyk podle vlastní volby, může být vhodné pro použití přímo protokolu:

#### <a name="first-case-access-token-request-with-a-shared-secret"></a>Prvním případě: Žádost o přístupový token s sdílený tajný klíč

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

### <a name="learn-more-about-the-protocol"></a>Další informace o protokolu

Další informace najdete v dokumentaci k protokolu: [Klient OAuth 2.0 a Azure Active Directory v2.0 pověření tok](v2-oauth2-client-creds-grant-flow.md).

## <a name="troubleshooting"></a>Řešení potíží

### <a name="did-you-use-the-resourcedefault-scope"></a>Použili jste hodnotu prostředek/.default oboru?

Pokud se zobrazí chybová zpráva oznamující, že jste použili neplatný obor, pravděpodobně nepoužili `resource/.default` oboru.

### <a name="did-you-forget-to-provide-admin-consent-daemon-apps-need-it"></a>Nezapomněli jste zadat souhlas správce? Aplikace démonů potřebujete!

Pokud dojde k chybě při volání rozhraní API **nedostatečná oprávnění k dokončení operace**, musí správce tenanta k udělení oprávnění k aplikaci. Přejděte ke kroku 6 Register výše klientskou aplikaci.
Obvykle se zobrazí a chyby, jako jsou následující popis chyby:

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
> [Démon procesu aplikace – volání webového rozhraní API](scenario-daemon-call-api.md)