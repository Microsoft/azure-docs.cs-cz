---
title: Volání webového rozhraní API z webové aplikace | Azure
titleSuffix: Microsoft identity platform
description: Naučte se, jak vytvořit webovou aplikaci, která volá webová rozhraní API (volání chráněného webového rozhraní API).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/25/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 815b1789c54d1ce505c16dc89e199d451ae9a588
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91396123"
---
# <a name="a-web-app-that-calls-web-apis-call-a-web-api"></a>Webová aplikace, která volá webová rozhraní API: volá webové rozhraní API.

Teď, když máte token, můžete zavolat chráněné webové rozhraní API. Z kontroleru nebo stránek vaší webové aplikace se obvykle volá rozhraní API pro příjem dat.

## <a name="call-a-protected-web-api"></a>Volání chráněného webového rozhraní API

Volání chráněného webového rozhraní API závisí na vašem jazyku a architektuře výběru:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Pokud používáte *Microsoft. identity. Web*, máte tři možnosti použití pro volání rozhraní API:

- [Možnost 1: volání Microsoft Graph s využitím sady Microsoft Graph SDK](#option-1-call-microsoft-graph-with-the-sdk)
- [Možnost 2: volání webového rozhraní API pro příjem dat s podpůrnou třídou](#option-2-call-a-downstream-web-api-with-the-helper-class)
- [Možnost 3: volání podřízeného webového rozhraní API bez pomocné třídy](#option-3-call-a-downstream-web-api-without-the-helper-class)

#### <a name="option-1-call-microsoft-graph-with-the-sdk"></a>Možnost 1: volání Microsoft Graph se sadou SDK

Chcete volat Microsoft Graph. V tomto scénáři jste přidali `AddMicrosoftGraph` v *Startup.cs* , jak je uvedeno v části [Konfigurace kódu](scenario-web-app-call-api-app-configuration.md#option-1-call-microsoft-graph), a můžete přímo vložit `GraphServiceClient` do svého kontroleru nebo konstruktoru stránky pro použití v akcích. Následující příklad stránky Razor zobrazuje fotografii přihlášeného uživatele.

```CSharp
[Authorize]
[AuthorizeForScopes(Scopes = new[] { "user.read" })]
public class IndexModel : PageModel
{
 private readonly GraphServiceClient _graphServiceClient;

 public IndexModel(GraphServiceClient graphServiceClient)
 {
    _graphServiceClient = graphServiceClient;
 }

 public async Task OnGet()
 {
  var user = await _graphServiceClient.Me.Request().GetAsync();
  try
  {
   using (var photoStream = await _graphServiceClient.Me.Photo.Content.Request().GetAsync())
   {
    byte[] photoByte = ((MemoryStream)photoStream).ToArray();
    ViewData["photo"] = Convert.ToBase64String(photoByte);
   }
   ViewData["name"] = user.DisplayName;
  }
  catch (Exception)
  {
   ViewData["photo"] = null;
  }
 }
}
```

#### <a name="option-2-call-a-downstream-web-api-with-the-helper-class"></a>Možnost 2: volání webového rozhraní API pro příjem dat s podpůrnou třídou

Chcete zavolat webové rozhraní API jiné než Microsoft Graph. V takovém případě jste přidali `AddDownstreamWebApi` v *Startup.cs* , jak je uvedeno v části [Konfigurace kódu](scenario-web-app-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph), a můžete přímo vložit `IDownstreamWebApi` službu do vašeho kontroleru nebo konstruktoru stránky a použít ji v akcích:

```CSharp
[Authorize]
[AuthorizeForScopes(ScopeKeySection = "TodoList:Scopes")]
public class TodoListController : Controller
{
  private IDownstreamWebApi _downstreamWebApi;
  private const string ServiceName = "TodoList";

  public TodoListController(IDownstreamWebApi downstreamWebApi)
  {
    _downstreamWebApi = downstreamWebApi;
  }

  public async Task<ActionResult> Details(int id)
  {
    var value = await _downstreamWebApi.CallWebApiForUserAsync(
      ServiceName,
      options =>
      {
        options.RelativePath = $"me";
      });
      return View(value);
  }
}
```

`CallWebApiForUserAsync`Obsahuje také obecná přepsání silného typu, která vám umožní přímo získat objekt. Například následující metoda obdrží `Todo` instanci, která je silně typovou reprezentací JSON vrácenou webovým rozhraním API.

```CSharp
    // GET: TodoList/Details/5
    public async Task<ActionResult> Details(int id)
    {
        var value = await _downstreamWebApi.CallWebApiForUserAsync<object, Todo>(
            ServiceName,
            null,
            options =>
            {
                options.HttpMethod = HttpMethod.Get;
                options.RelativePath = $"api/todolist/{id}";
            });
        return View(value);
    }
   ```

#### <a name="option-3-call-a-downstream-web-api-without-the-helper-class"></a>Možnost 3: volání podřízeného webového rozhraní API bez pomocné třídy

Rozhodli jste se získat token ručně pomocí `ITokenAcquisition` služby a teď je potřeba použít token. V takovém případě následující kód pokračuje v ukázkovém kódu zobrazeném ve [webové aplikaci, která volá webová rozhraní API: získání tokenu pro aplikaci](scenario-web-app-call-api-acquire-token.md). Kód se volá v akcích řadičů webové aplikace.

Po získání tokenu ho použijte jako nosný token pro volání rozhraní API pro příjem dat, v tomto případě Microsoft Graph.

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
> Většina webových rozhraní API Azure poskytuje sadu SDK, která zjednodušuje volání rozhraní API, jako je případ pro Microsoft Graph. Příklad webové aplikace [, která umožňuje přístup k úložišti objektů BLOB s využitím služby Azure AD](https://docs.microsoft.com/azure/storage/common/storage-auth-aad-app?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json&tabs=dotnet) , najdete v tématu Vytvoření webové aplikace s použitím sady Microsoft. identity. web a Azure Storage SDK.

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
> [Přechod k produkčnímu prostředí](scenario-web-app-call-api-production.md)
