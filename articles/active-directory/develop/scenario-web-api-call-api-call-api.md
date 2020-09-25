---
title: Webové rozhraní API, které volá webová rozhraní API – Microsoft Identity Platform | Azure
description: Naučte se vytvářet webové rozhraní API, které volá webová rozhraní API.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: b1582af2bbd97579852ead0d4462f80f3a50fe6a
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257142"
---
# <a name="a-web-api-that-calls-web-apis-call-an-api"></a>Webové rozhraní API, které volá webová rozhraní API: volá rozhraní API.

Po vytvoření tokenu můžete zavolat chráněné webové rozhraní API. Rozhraní API pro příjem dat obvykle vyvoláte z kontroleru nebo stránek vašeho webového rozhraní API.

## <a name="controller-code"></a>Kód kontroleru

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/aspnetcore)

Pokud používáte *Microsoft. identity. Web*, máte tři scénáře použití:

- [Microsoft Graph volání](#call-microsoft-graph)
- [Volání webového rozhraní API jiného než Microsoft Graph](#call-web-api-other-than-microsoft-graph)
- [Ruční získání tokenu](#acquire-a-token-manually)

#### <a name="call-microsoft-graph"></a>Microsoft Graph volání

V tomto scénáři jste přidali `.AddMicrosoftGraph()` v *Startup.cs* , jak je uvedeno v části [Konfigurace kódu](scenario-web-api-call-api-app-configuration.md#option-1-call-microsoft-graph), a můžete přímo vložit `GraphServiceClient` do svého kontroleru nebo konstruktoru stránky pro použití v akcích. Následující příklad stránky Razor zobrazuje fotografii přihlášeného uživatele.

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

#### <a name="call-web-api-other-than-microsoft-graph"></a>Volání webového rozhraní API jiné než Microsoft Graph

V tomto scénáři jste přidali `.AddDownstreamWebApi()` v *Startup.cs* , jak je uvedeno v části [Konfigurace kódu](scenario-web-api-call-api-app-configuration.md#option-2-call-a-downstream-web-api-other-than-microsoft-graph), a můžete přímo vložit `IDownstreamWebApi` službu do vašeho kontroleru nebo konstruktoru stránky a použít ji v akcích:

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
```

`CallWebApiForUserAsync`Metoda má také obecné přepsání silného typu, které vám umožní přímo získat objekt. Například následující metoda obdržela `Todo` instanci, která je silně typovou reprezentací JSON vrácenou webovým rozhraním API.

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

#### <a name="acquire-a-token-manually"></a>Ruční získání tokenu

Pokud jste se rozhodli získat token ručně pomocí `ITokenAcquisition` služby, je teď nutné použít token. V takovém případě následující kód pokračuje v ukázkovém kódu zobrazeném ve [webovém rozhraní API, které volá webová rozhraní API: Získejte token pro aplikaci](scenario-web-api-call-api-acquire-token.md). Kód se volá v akcích řadičů rozhraní API. Volá rozhraní API pro příjem dat s názvem *ToDoList*.

 Po získání tokenu ho použijte jako nosný token pro volání rozhraní API pro příjem dat.

```csharp
 private async Task CallTodoListService(string accessToken)
 {
  // After the token has been returned by Microsoft.Identity.Web, add it to the HTTP authorization header before making the call to access the todolist service.
 _httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);

 // Call the todolist service.
 HttpResponseMessage response = await _httpClient.GetAsync(TodoListBaseAddress + "/api/todolist");
 // ...
 }
 ```

# <a name="java"></a>[Java](#tab/java)

Následující kód pokračuje v ukázkovém kódu, který je zobrazen ve [webovém rozhraní API, které volá webová rozhraní API: získání tokenu pro aplikaci](scenario-web-api-call-api-acquire-token.md). Kód se volá v akcích řadičů rozhraní API. Volá API MS Graph pro příjem dat.

Po získání tokenu ho použijte jako nosný token pro volání rozhraní API pro příjem dat.

```Java
private String callMicrosoftGraphMeEndpoint(String accessToken){
    RestTemplate restTemplate = new RestTemplate();

    HttpHeaders headers = new HttpHeaders();
    headers.setContentType(MediaType.APPLICATION_JSON);

    headers.set("Authorization", "Bearer " + accessToken);

    HttpEntity<String> entity = new HttpEntity<>(null, headers);

    String result = restTemplate.exchange("https://graph.microsoft.com/v1.0/me", HttpMethod.GET,
            entity, String.class).getBody();

    return result;
}
```

# <a name="python"></a>[Python](#tab/python)
Ukázka demonstrující tento tok pomocí MSAL Pythonu ještě není k dispozici.

---

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Webové rozhraní API, které volá webová rozhraní API: Přesun do produkčního prostředí](scenario-web-api-call-api-production.md)
