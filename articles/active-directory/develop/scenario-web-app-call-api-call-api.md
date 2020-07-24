---
title: Volání webového rozhraní API z webové aplikace – Microsoft Identity Platform | Azure
description: Naučte se, jak vytvořit webovou aplikaci, která volá webová rozhraní API (volání chráněného webového rozhraní API).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/14/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 1e448f52f4e8c24dd8552cae873edac841e57fc6
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87058440"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>Webová aplikace, která volá webová rozhraní API: volá webové rozhraní API.

Teď, když máte token, můžete zavolat chráněné webové rozhraní API.

## <a name="call-a-protected-web-api"></a>Volání chráněného webového rozhraní API

Volání chráněného webového rozhraní API závisí na vašem jazyku a architektuře výběru:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Zde je zjednodušený kód pro akci `HomeController` . Tento kód získá token pro volání Microsoft Graph. Byl přidán kód, který ukazuje, jak volat Microsoft Graph jako REST API. Adresa URL pro rozhraní Microsoft Graph API je k dispozici v appsettings.jssouboru a je čtena v proměnné s názvem `webOptions` :

```json
{
  "AzureAd": {
    "Instance": "https://login.microsoftonline.com/",
    ...
  },
  ...
  "GraphApiUrl": "https://graph.microsoft.com"
}
```

```csharp
public async Task<IActionResult> Profile()
{
 // Acquire the access token.
 string[] scopes = new string[]{"user.read"};
 string accessToken = await tokenAcquisition.GetAccessTokenForUserAsync(scopes);

 // Use the access token to call a protected web API.
 HttpClient client = new HttpClient();
 client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", accessToken);

  var response = await httpClient.GetAsync($"{webOptions.GraphApiUrl}/beta/me");

  if (response.StatusCode == HttpStatusCode.OK)
  {
   var content = await response.Content.ReadAsStringAsync();

   dynamic me = JsonConvert.DeserializeObject(content);
   ViewData["Me"] = me;
  }

  return View();
}
```

> [!NOTE]
> Stejný princip můžete použít pro volání libovolného webového rozhraní API.
>
> Většina webových rozhraní API Azure poskytuje sadu SDK, která zjednodušuje volání rozhraní API. To platí také Microsoft Graph. V dalším článku se naučíte, jak najít kurz, který ilustruje použití rozhraní API.

# <a name="java"></a>[Java](#tab/java)

```Java
private String getUserInfoFromGraph(String accessToken) throws Exception {
    // Microsoft Graph user endpoint
    URL url = new URL("https://graph.microsoft.com/v1.0/me");

    HttpURLConnection conn = (HttpURLConnection) url.openConnection();

    // Set the appropriate header fields in the request header.
    conn.setRequestProperty("Authorization", "Bearer " + accessToken);
    conn.setRequestProperty("Accept", "application/json");

    String response = HttpClientHelper.getResponseStringFromConn(conn);

    int responseCode = conn.getResponseCode();
    if(responseCode != HttpURLConnection.HTTP_OK) {
        throw new IOException(response);
    }

    JSONObject responseObject = HttpClientHelper.processResponse(responseCode, response);
    return responseObject.toString();
}

```

# <a name="python"></a>[Python](#tab/python)

```Python
@app.route("/graphcall")
def graphcall():
    token = _get_token_from_cache(app_config.SCOPE)
    if not token:
        return redirect(url_for("login"))
    graph_data = requests.get(  # Use token to call downstream service.
        app_config.ENDPOINT,
        headers={'Authorization': 'Bearer ' + token['access_token']},
        ).json()
    return render_template('display.html', result=graph_data)
```

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přesun do produkčního prostředí](scenario-web-app-call-api-production.md)
