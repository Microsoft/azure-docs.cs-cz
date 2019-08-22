---
title: Azure Functions triggerů HTTP a vazeb
description: Zjistěte, jak používat triggery a vazby HTTP v Azure Functions.
services: functions
documentationcenter: na
author: craigshoemaker
manager: gwallace
keywords: Azure Functions, Functions, zpracování událostí, Webhooky, dynamická výpočetní funkce, architektura bez serveru, HTTP, rozhraní API, REST
ms.service: azure-functions
ms.devlang: multiple
ms.topic: reference
ms.date: 11/21/2017
ms.author: cshoe
ms.openlocfilehash: 5e3b96d79ddef7940fd4029f9268e3040a63d2fb
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875074"
---
# <a name="azure-functions-http-triggers-and-bindings"></a>Azure Functions triggerů HTTP a vazeb

Tento článek vysvětluje, jak pracovat s triggery HTTP a výstupními vazbami v Azure Functions.

Trigger HTTP se dá přizpůsobit tak, aby reagoval [](https://en.wikipedia.org/wiki/Webhook)na Webhooky.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

Kód v tomto článku je ve výchozím nastavení funkcí 2. x, který používá .NET Core. Informace o syntaxi 1. x naleznete v [šablonách funkcí 1. x](https://github.com/Azure/azure-functions-templates/tree/v1.x/Functions.Templates/Templates).

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Vazby HTTP jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) , verze 1. x. Zdrojový kód balíčku je v úložišti GitHub [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http) .

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Vazby HTTP jsou k dispozici v balíčku NuGet [Microsoft. Azure. WebJobs. Extensions. http](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) , verze 3. x. Zdrojový kód balíčku je v úložišti GitHub [Azure-WebJobs-SDK-Extensions](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) .

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Trigger

Trigger HTTP umožňuje vyvolat funkci s požadavkem HTTP. Pomocí triggeru HTTP můžete vytvářet rozhraní API bez serveru a reagovat na Webhooky.

Ve výchozím nastavení Trigger HTTP vrátí HTTP 200 OK s prázdným textem ve funkcích 1. x nebo HTTP 204 bez obsahu s prázdným textem ve funkcích 2. x. Chcete-li změnit odpověď, nakonfigurujte [výstupní vazbu http](#output).

## <a name="trigger---example"></a>Aktivační události – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#trigger---c-example)
* [C# skript (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [Java](#trigger---java-examples)
* [JavaScript](#trigger---javascript-example)
* [Python](#trigger---python-example)

### <a name="trigger---c-example"></a>Aktivační události – příklad v jazyce C#

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která `name` vyhledává parametr buď v řetězci dotazu, nebo v těle požadavku HTTP. Všimněte si, že návratová hodnota se používá pro výstupní vazbu, ale atribut návratové hodnoty není povinný.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] 
    HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

### <a name="trigger---c-script-example"></a>Aktivační události – příklad skriptu jazyka C#

Následující příklad ukazuje aktivační vazbu v souboru *Function. JSON* a [ C# funkci skriptu](functions-reference-csharp.md) , která používá vazbu. Funkce vyhledá `name` parametr buď v řetězci dotazu, nebo v těle požadavku HTTP.

Tady je *function.json* souboru:

```json
{
    "disabled": false,
    "bindings": [
        {
            "authLevel": "function",
            "name": "req",
            "type": "httpTrigger",
            "direction": "in",
            "methods": [
                "get",
                "post"
            ]
        },
        {
            "name": "$return",
            "type": "http",
            "direction": "out"
        }
    ]
}
```

[Konfigurace](#trigger---configuration) bodu vysvětluje tyto vlastnosti.

Zde je C# kód skriptu, který se váže `HttpRequest`k:

```cs
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
    log.LogInformation("C# HTTP trigger function processed a request.");

    string name = req.Query["name"];

    string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
    dynamic data = JsonConvert.DeserializeObject(requestBody);
    name = name ?? data?.name;

    return name != null
        ? (ActionResult)new OkObjectResult($"Hello, {name}")
        : new BadRequestObjectResult("Please pass a name on the query string or in the request body");
}
```

Můžete vytvořit navázání na vlastní objekt namísto `HttpRequest`. Tento objekt se vytvoří z těla žádosti a analyzuje se jako JSON. Podobně lze typ předat výstupní vazbě odpovědi HTTP a vrátí se jako tělo odpovědi spolu s kódem stavu 200.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static string Run(Person person, ILogger log)
{   
    return person.Name != null
        ? (ActionResult)new OkObjectResult($"Hello, {person.Name}")
        : new BadRequestObjectResult("Please pass an instance of Person.");
}

public class Person {
     public string Name {get; set;}
}
```

### <a name="trigger---f-example"></a>Aktivační události – F# příklad

Následující příklad ukazuje aktivační vazbu v souboru *Function. JSON* a [ F# funkci](functions-reference-fsharp.md) , která používá vazbu. Funkce vyhledá `name` parametr buď v řetězci dotazu, nebo v těle požadavku HTTP.

Tady je *function.json* souboru:

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in"
    },
    {
      "name": "res",
      "type": "http",
      "direction": "out"
    }
  ],
  "disabled": false
}
```

[Konfigurace](#trigger---configuration) bodu vysvětluje tyto vlastnosti.

Tady je F# kódu:

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic

let Run(req: HttpRequestMessage) =
    async {
        let q =
            req.GetQueryNameValuePairs()
                |> Seq.tryFind (fun kv -> kv.Key = "name")
        match q with
        | Some kv ->
            return req.CreateResponse(HttpStatusCode.OK, "Hello " + kv.Value)
        | None ->
            let! data = Async.AwaitTask(req.Content.ReadAsAsync<obj>())
            try
                return req.CreateResponse(HttpStatusCode.OK, "Hello " + data?name)
            with e ->
                return req.CreateErrorResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
    } |> Async.StartAsTask
```

Potřebujete `project.json` soubor, který používá NuGet pro odkazování na `FSharp.Interop.Dynamic` sestavení `Dynamitey` a, jak je znázorněno v následujícím příkladu:

```json
{
  "frameworks": {
    "net46": {
      "dependencies": {
        "Dynamitey": "1.0.2",
        "FSharp.Interop.Dynamic": "3.0.0"
      }
    }
  }
}
```

### <a name="trigger---javascript-example"></a>Aktivační události – příklad v jazyce JavaScript

Následující příklad ukazuje aktivační vazbu v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce vyhledá `name` parametr buď v řetězci dotazu, nebo v těle požadavku HTTP.

Tady je *function.json* souboru:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

[Konfigurace](#trigger---configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

```javascript
module.exports = function(context, req) {
    context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);

    if (req.query.name || (req.body && req.body.name)) {
        context.res = {
            // status defaults to 200 */
            body: "Hello " + (req.query.name || req.body.name)
        };
    }
    else {
        context.res = {
            status: 400,
            body: "Please pass a name on the query string or in the request body"
        };
    }
    context.done();
};
```

### <a name="trigger---python-example"></a>Trigger – příklad Pythonu

Následující příklad ukazuje aktivační vazbu v souboru *Function. JSON* a [funkci Pythonu](functions-reference-python.md) , která používá vazbu. Funkce vyhledá `name` parametr buď v řetězci dotazu, nebo v těle požadavku HTTP.

Tady je *function.json* souboru:

```json
{
    "scriptFile": "__init__.py",
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "function",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

[Konfigurace](#trigger---configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód Pythonu:

```python
import logging
import azure.functions as func


def main(req: func.HttpRequest) -> func.HttpResponse:
    logging.info('Python HTTP trigger function processed a request.')

    name = req.params.get('name')
    if not name:
        try:
            req_body = req.get_json()
        except ValueError:
            pass
        else:
            name = req_body.get('name')

    if name:
        return func.HttpResponse(f"Hello {name}!")
    else:
        return func.HttpResponse(
            "Please pass a name on the query string or in the request body",
            status_code=400
        )
```

### <a name="trigger---java-examples"></a>Trigger – příklady jazyka Java

* [Načíst parametr z řetězce dotazu](#read-parameter-from-the-query-string-java)
* [Přečíst tělo žádosti POST](#read-body-from-a-post-request-java)
* [Načíst parametr z trasy](#read-parameter-from-a-route-java)
* [Číst tělo POJO z požadavku POST](#read-pojo-body-from-a-post-request-java)

Následující příklady znázorňují vazbu triggeru HTTP v souboru *Function. JSON* a příslušné [funkce jazyka Java](functions-reference-java.md) , které používají vazbu. 

Tady je *function.json* souboru:

```json
{
    "disabled": false,    
    "bindings": [
        {
            "authLevel": "anonymous",
            "type": "httpTrigger",
            "direction": "in",
            "name": "req"
        },
        {
            "type": "http",
            "direction": "out",
            "name": "res"
        }
    ]
}
```

#### <a name="read-parameter-from-the-query-string-java"></a>Načíst parametr z řetězce dotazu (Java)  

Tento příklad přečte parametr s názvem ```id```, z řetězce dotazu a použije ho k sestavení dokumentu JSON vráceného klientovi s typem ```application/json```obsahu. 

```java
    @FunctionName("TriggerStringGet")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("GET parameters are: " + request.getQueryParameters());

        // Get named parameter
        String id = request.getQueryParameters().getOrDefault("id", "");

        // Convert and display
        if (id.isEmpty()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String name = "fake_name";
            final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                         "\"description\": \"" + name + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-body-from-a-post-request-java"></a>Přečíst tělo žádosti POST (Java)  

V tomto příkladu se přečte tělo žádosti post jako ```String```a a použije se k sestavení dokumentu JSON vráceného klientovi s typem ```application/json```obsahu.

```java
    @FunctionName("TriggerStringPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<String>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(""));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String body = request.getBody().get();
            final String jsonDocument = "{\"id\":\"123456\", " + 
                                         "\"description\": \"" + body + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-parameter-from-a-route-java"></a>Číst parametr z trasy (Java)  

Tento příklad načte povinný parametr s názvem ```id```a nepovinný parametr ```name``` z cesty trasy a použije je k sestavení dokumentu JSON vráceného klientovi s typem ```application/json```obsahu. T

```java
    @FunctionName("TriggerStringRoute")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.GET}, 
              authLevel = AuthorizationLevel.ANONYMOUS,
              route = "trigger/{id}/{name=EMPTY}") // name is optional and defaults to EMPTY
            HttpRequestMessage<Optional<String>> request,
            @BindingName("id") String id,
            @BindingName("name") String name,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Route parameters are: " + id);

        // Convert and display
        if (id == null) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final String jsonDocument = "{\"id\":\"" + id + "\", " + 
                                         "\"description\": \"" + name + "\"}";
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(jsonDocument)
                          .build();
        }
    }
```

#### <a name="read-pojo-body-from-a-post-request-java"></a>Číst tělo POJO z požadavku POST (Java)  

Zde je kód pro ```ToDoItem``` třídu, na kterou se odkazuje v tomto příkladu:

```java

public class ToDoItem {

  private String id;
  private String description;  

  public ToDoItem(String id, String description) {
    this.id = id;
    this.description = description;
  }

  public String getId() {
    return id;
  }

  public String getDescription() {
    return description;
  }
  
  @Override
  public String toString() {
    return "ToDoItem={id=" + id + ",description=" + description + "}";
  }
}

```

Tento příklad přečte tělo žádosti POST. Text žádosti se automaticky rozdělí do ```ToDoItem``` objektu a vrátí se klientovi s typem ```application/json```obsahu. Parametr je serializován modulem runtime Functions, protože je přiřazen ```body``` vlastnosti ```HttpMessageResponse.Builder``` třídy. ```ToDoItem```

```java
    @FunctionName("TriggerPojoPost")
    public HttpResponseMessage run(
            @HttpTrigger(name = "req", 
              methods = {HttpMethod.POST}, 
              authLevel = AuthorizationLevel.ANONYMOUS)
            HttpRequestMessage<Optional<ToDoItem>> request,
            final ExecutionContext context) {
        
        // Item list
        context.getLogger().info("Request body is: " + request.getBody().orElse(null));

        // Check request body
        if (!request.getBody().isPresent()) {
            return request.createResponseBuilder(HttpStatus.BAD_REQUEST)
                          .body("Document not found.")
                          .build();
        } 
        else {
            // return JSON from to the client
            // Generate document
            final ToDoItem body = request.getBody().get();
            return request.createResponseBuilder(HttpStatus.OK)
                          .header("Content-Type", "application/json")
                          .body(body)
                          .build();
        }
    }
```

## <a name="trigger---attributes"></a>Aktivační události – atributy

V [ C# knihovnách tříd](functions-dotnet-class-library.md)použijte atribut [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) .

Můžete nastavit úroveň autorizace a povolené metody HTTP v parametrech konstruktoru atributu a existují vlastnosti pro typ Webhooku a šablonu směrování. Další informace o těchto nastaveních najdete v tématu [Trigger-Configuration](#trigger---configuration). Zde je `HttpTrigger` atribut v signatuře metody:

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Kompletní příklad naleznete v tématu [Trigger – C# příklad](#trigger---c-example).

## <a name="trigger---configuration"></a>Aktivační události – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `HttpTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
| **type** | neuvedeno| Požadováno – musí být nastavené `httpTrigger`na. |
| **direction** | neuvedeno| Požadováno – musí být nastavené `in`na. |
| **name** | neuvedeno| Required – název proměnné použitý v kódu funkce pro text žádosti nebo žádosti. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Určuje, které klíče (pokud existují) musí být k žádosti přítomny, aby bylo možné funkci vyvolat. Úroveň autorizace může být jedna z následujících hodnot: <ul><li><code>anonymous</code>&mdash;Není vyžadován žádný klíč rozhraní API.</li><li><code>function</code>&mdash;Klíč rozhraní API specifický pro funkci je povinný. Toto je výchozí hodnota, pokud není zadána žádná.</li><li><code>admin</code>&mdash;Hlavní klíč je povinný.</li></ul> Další informace najdete v části o autorizačních [klíčích](#authorization-keys). |
| **methods** |**Způsobů** | Pole metod HTTP, na které funkce reaguje. Pokud není zadaný, funkce reaguje na všechny metody HTTP. Viz [přizpůsobení koncového bodu http](#customize-the-http-endpoint). |
| **route** | **Cestě** | Definuje šablonu směrování, která řídí, které adresy URL žádostí vaše funkce reaguje. Výchozí hodnota, pokud není zadána, je `<functionname>`. Další informace najdete v tématu [přizpůsobení koncového bodu http](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Podporováno pouze pro modul runtime verze 1. x._<br/><br/>Nakonfiguruje Trigger HTTP tak, aby sloužil [](https://en.wikipedia.org/wiki/Webhook) jako přijímač Webhooku pro zadaného zprostředkovatele. Pokud jste nastavili tuto vlastnost, nenastavujte tuto vlastnost.`methods` Typ Webhooku může být jedna z následujících hodnot:<ul><li><code>genericJson</code>&mdash;Koncový bod Webhooku pro obecné účely bez logiky pro konkrétního zprostředkovatele. Toto nastavení omezuje požadavky jenom na ty, které používají http post a s `application/json` typem obsahu.</li><li><code>github</code>&mdash;Funkce reaguje na Webhooky [GitHubu](https://developer.github.com/webhooks/). Nepoužívejte vlastnost _authLevel_ s Webhooky GitHubu. Další informace najdete v části Webhooky GitHubu dále v tomto článku.</li><li><code>slack</code>&mdash;Funkce reaguje na Webhooky [časové rezervy](https://api.slack.com/outgoing-webhooks). Nepoužívejte vlastnost _authLevel_ s Webhooky časové rezervy. Další informace najdete v části časová pole webhooků dále v tomto článku.</li></ul>|

## <a name="trigger---usage"></a>Aktivační události – využití

Pro C# funkce F# a můžete deklarovat typ vstupu triggeru tak, aby byl buď `HttpRequest` nebo vlastní typ. Pokud zvolíte `HttpRequest`, získáte úplný přístup k objektu Request. Pro vlastní typ se modul runtime pokusí analyzovat tělo požadavku JSON pro nastavení vlastností objektu.

Pro funkce JavaScriptu poskytuje modul runtime Functions text žádosti místo objektu Request. Další informace najdete v [příkladu triggeru JavaScriptu](#trigger---javascript-example).

### <a name="customize-the-http-endpoint"></a>Přizpůsobení koncového bodu HTTP

Ve výchozím nastavení platí, že když vytvoříte funkci triggeru HTTP, funkce je adresovat s cestou formuláře:

    http://<yourapp>.azurewebsites.net/api/<funcname>

Tuto trasu můžete přizpůsobit pomocí volitelné `route` vlastnosti vstupní vazby triggeru protokolu HTTP. Například následující soubor *Function. JSON* definuje `route` vlastnost triggeru http:

```json
{
    "bindings": [
    {
        "type": "httpTrigger",
        "name": "req",
        "direction": "in",
        "methods": [ "get" ],
        "route": "products/{category:alpha}/{id:int?}"
    },
    {
        "type": "http",
        "name": "res",
        "direction": "out"
    }
    ]
}
```

Pomocí této konfigurace je nyní funkce adresovat s následujícím směrováním místo původní trasy.

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

To umožňuje, aby kód funkce podporoval dva parametry v adrese, _kategorii_ a _ID_. Pomocí parametrů můžete použít jakékoli [omezení trasy webového rozhraní API](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) . Následující C# kód funkce využívá oba parametry.

```csharp
public static Task<IActionResult> Run(HttpRequest req, string category, int? id, ILogger log)
{
    if (id == null)
    {
        return (ActionResult)new OkObjectResult($"All {category} items were requested.");
    }
    else
    {
        return (ActionResult)new OkObjectResult($"{category} item with id = {id} has been requested.");
    }
    
    // -----
    log.LogInformation($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");
}
```

Tady je funkce Node. js, která používá stejné parametry směrování.

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;

    if (!id) {
        context.res = {
            // status defaults to 200 */
            body: "All " + category + " items were requested."
        };
    }
    else {
        context.res = {
            // status defaults to 200 */
            body: category + " item with id = " + id + " was requested."
        };
    }

    context.done();
}
```

Ve výchozím nastavení jsou všechny trasy funkcí s předponou *rozhraní API*. Můžete také přizpůsobit nebo odebrat předponu pomocí `http.routePrefix` vlastnosti v souboru [Host. JSON](functions-host-json.md) . Následující příklad odebere předponu trasy *rozhraní API* pomocí prázdného řetězce pro předponu v souboru *Host. JSON* .

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="working-with-client-identities"></a>Práce s identitami klientů

Pokud vaše aplikace Function App používá [App Service ověřování/autorizaci](../app-service/overview-authentication-authorization.md), můžete zobrazit informace o ověřených klientech z vašeho kódu. Tyto informace jsou k dispozici jako [hlavičky požadavků vložené platformou](../app-service/app-service-authentication-how-to.md#access-user-claims). 

Tyto informace můžete také přečíst z dat vazby. Tato funkce je k dispozici pouze pro modul runtime Functions 2. x. K dispozici je také v současnosti jenom pro jazyky .NET.

V jazycích .NET jsou tyto informace k dispozici jako [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal je k dispozici jako součást kontextu požadavku, jak je znázorněno v následujícím příkladu:

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;

public static IActionResult Run(HttpRequest req, ILogger log)
{
    ClaimsPrincipal identities = req.HttpContext.User;
    // ...
    return new OkObjectResult();
}
```

Alternativně je možné ClaimsPrincipal jednoduše zahrnout jako další parametr v signatuře funkce:

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using System.Security.Claims;
using Newtonsoft.Json.Linq;

public static void Run(JObject input, ClaimsPrincipal principal, ILogger log)
{
    // ...
    return;
}

```

### <a name="authorization-keys"></a>Autorizační klíče

Funkce umožňují používat klíče k tomu, aby během vývoje měly přístup k koncovým bodům funkce HTTP.  Standardní Trigger HTTP může vyžadovat, aby v žádosti byl přítomen klíč rozhraní API. 

> [!IMPORTANT]
> I když klíče mohou během vývoje přispět k zařazování koncových bodů HTTP, nejsou určené jako způsob zabezpečení triggeru HTTP v produkčním prostředí. Další informace najdete v tématu [zabezpečení koncového bodu http v produkčním prostředí](#secure-an-http-endpoint-in-production).

> [!NOTE]
> V modulu runtime Functions 1. x můžou poskytovatelé Webhooku použít klíče k autorizaci požadavků různými způsoby v závislosti na tom, co poskytovatel podporuje. Tento vztah je popsaný v webhookech [a klíčích](#webhooks-and-keys). Modul runtime verze 2. x neobsahuje integrovanou podporu pro poskytovatele webhooků.

Existují dva typy klíčů:

* **Klíče hostitele**: Tyto klíče jsou sdíleny všemi funkcemi v aplikaci Function App. Při použití jako klíč rozhraní API umožňují tyto funkce přístup k libovolné funkci v aplikaci Function App.
* **Funkční klávesy**: Tyto klíče se vztahují pouze na konkrétní funkce, ve kterých jsou definovány. Když se použije jako klíč rozhraní API, povolí přístup k této funkci.

Každý klíč má název pro referenci a výchozí klíč (s názvem "výchozí") na úrovni funkce a hostitele. Klíče funkcí mají přednost před klíči hostitele. Pokud jsou definovány dva klíče se stejným názvem, je klíč funkce vždy použit.

Každá aplikace Function App má také speciální **hlavní klíč**. Tento klíč je hostitelským klíčem s `_master`názvem, který poskytuje přístup pro správu rozhraní API modulu runtime. Tento klíč nelze odvolat. Když nastavíte úroveň `admin`autorizace, musí žádosti používat hlavní klíč. všechny ostatní klíče mají za následek selhání autorizace.

> [!CAUTION]  
> Vzhledem ke zvýšeným oprávněním v aplikaci Function App udělené hlavním klíčem byste tento klíč neměli sdílet s třetími stranami nebo ho distribuovat v nativních klientských aplikacích. Při volbě úrovně autorizace Správce buďte opatrní.

### <a name="obtaining-keys"></a>Získání klíčů

Klíče se ukládají jako součást aplikace Function App v Azure a jsou zašifrované v klidovém stavu. Chcete-li zobrazit klíče, vytvořte nové nebo zaveďte klíče k novým hodnotám, v [Azure Portal](https://portal.azure.com) přejděte na jednu z funkcí AKTIVOVANÝch protokolem HTTP a vyberte **Spravovat**.

![Spravujte klíče funkcí na portálu.](./media/functions-bindings-http-webhook/manage-function-keys.png)

Klíče funkcí můžete získat programově pomocí [rozhraní API pro správu klíčů](https://github.com/Azure/azure-functions-host/wiki/Key-management-API).

### <a name="api-key-authorization"></a>Autorizace klíče rozhraní API

Většina šablon triggeru HTTP vyžaduje v žádosti klíč rozhraní API. Vaše žádost HTTP obvykle vypadá jako na následující adrese URL:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

Klíč lze zahrnout do proměnné řetězce dotazu s názvem `code`, jak je uvedeno výše. Může být také obsažena v `x-functions-key` hlavičce protokolu HTTP. Hodnotou klíče může být libovolný klíč funkce definovaný pro funkci nebo libovolný klíč hostitele.

Můžete povolit anonymní požadavky, které nevyžadují klíče. Můžete také vyžadovat, aby byl hlavní klíč použit. Výchozí úroveň autorizace se mění pomocí `authLevel` vlastnosti ve formátu JSON vazby. Další informace najdete v tématu [Trigger-Configuration](#trigger---configuration).

> [!NOTE]
> Při místním spouštění funkcí je autorizace zakázaná bez ohledu na zadané nastavení úrovně ověřování. Po publikování do Azure `authLevel` se vynutilo nastavení v triggeru.



### <a name="secure-an-http-endpoint-in-production"></a>Zabezpečení koncového bodu HTTP v produkčním prostředí

Aby bylo možné plně zabezpečit koncové body funkcí v produkčním prostředí, měli byste zvážit implementaci jedné z následujících funkcí zabezpečení na úrovni aplikace.

* Zapněte App Service ověřování/autorizaci aplikace Function App. Platforma App Service umožňuje používat Azure Active Directory (AAD) a několik zprostředkovatelů identity třetích stran k ověřování klientů. To můžete použít k implementaci vlastních autorizačních pravidel pro vaše funkce a můžete pracovat s uživatelskými informacemi z kódu funkce. Další informace najdete v tématu [ověřování a autorizace v Azure App Service](../app-service/overview-authentication-authorization.md) a [práci s identitami klientů](#working-with-client-identities).

* K ověření požadavků použijte Azure API Management (APIM). APIM poskytuje celou řadu možností zabezpečení rozhraní API pro příchozí požadavky. Další informace najdete v tématu [zásady ověřování API Management](../api-management/api-management-authentication-policies.md). Pomocí APIM můžete nakonfigurovat aplikaci Function App, aby přijímala požadavky pouze z IP adresy vaší instance APIM. Další informace najdete v tématu [omezení IP adres](ip-addresses.md#ip-address-restrictions).

* Nasaďte aplikaci Function App na Azure App Service Environment (pomocného mechanismu). Pomocného mechanismu poskytuje vyhrazené hostitelské prostředí, ve kterém se mají spouštět vaše funkce. Pomocného uživatele vám umožní nakonfigurovat jednu front-end bránu, kterou můžete použít k ověřování všech příchozích požadavků. Další informace najdete v tématu [Konfigurace firewallu webových aplikací (WAF) pro App Service Environment](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

Při použití jedné z těchto metod zabezpečení na úrovni aplikace byste měli nastavit úroveň ověřování funkce aktivované protokolem HTTP na `anonymous`.

### <a name="webhooks"></a>webhooks

> [!NOTE]
> Režim Webhooku je dostupný jenom pro verzi 1. x modulu runtime Functions. Tato změna byla provedena za účelem zlepšení výkonu aktivačních událostí HTTP ve verzi 2. x.

V šablonách Webhooku verze 1. x poskytují další ověřování datových částí Webhooku. Ve verzi 2. x stále funguje základní aktivační událost HTTP a je doporučený postup pro Webhooky. 

#### <a name="github-webhooks"></a>Webhooky GitHubu

Pokud chcete reagovat na Webhooky GitHubu, nejprve vytvořte funkci pomocí triggeru HTTP a nastavte vlastnost **webHookType** na `github`hodnotu. Pak zkopírujte jeho adresu URL a klíč rozhraní API do stránky **Přidat Webhook** vašeho úložiště GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

#### <a name="slack-webhooks"></a>Webhooky časové rezervy

Webhook časové rezervy vygeneruje token pro místo jeho zadání, takže musíte nakonfigurovat klíč specifický pro funkci s tokenem z časové rezervy. Podívejte se na [klíče](#authorization-keys)pro autorizaci.

### <a name="webhooks-and-keys"></a>Webhooky a klíče

Autorizace Webhooku se zpracovává komponentou přijímače Webhooku, součástí triggeru HTTP a mechanismus se liší v závislosti na typu Webhooku. Každý mechanismus spoléhá na klíč. Ve výchozím nastavení se používá klíč funkce s názvem "výchozí". Chcete-li použít jiný klíč, nakonfigurujte poskytovatele Webhooku tak, aby odesílal název klíče s požadavkem jedním z následujících způsobů:

* **Řetězec dotazu**: Zprostředkovatel předá název klíče v `clientid` parametru řetězce dotazu, `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`jako je například.
* **Hlavička žádosti**: Zprostředkovatel předá název klíče v `x-functions-clientid` hlavičce.

## <a name="trigger---limits"></a>Aktivační události – omezení

Délka požadavku HTTP je omezená na 100 MB (104 857 600 bajtů) a délka adresy URL je omezená na 4 KB (4 096 bajtů). Tato omezení jsou určena `httpRuntime` prvkem [souboru Web. config](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config)modulu runtime.

Pokud funkce, která používá Trigger HTTP, nebude dokončena během přibližně 2,5 minut, brána vyprší a vrátí chybu HTTP 502. Funkce bude pokračovat v běhu, ale nebude moci vrátit odpověď HTTP. U dlouhotrvajících funkcí doporučujeme, abyste provedli asynchronní vzorce a vraceli umístění, kde můžete testovat stav žádosti pomocí příkazového testu. Informace o tom, jak dlouho může funkce běžet, najdete v tématu [škálování a plán využití hostování](functions-scale.md#timeout).

## <a name="trigger---hostjson-properties"></a>Aktivační události – vlastnosti host.json

Soubor [Host. JSON](functions-host-json.md) obsahuje nastavení, která řídí chování triggeru http.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Výstup

K reakci na odesílatele požadavku HTTP použijte vazbu výstupu HTTP. Tato vazba vyžaduje Trigger HTTP a umožňuje přizpůsobit odpověď přidruženou k žádosti triggeru. Pokud není k dispozici vazba výstupu HTTP, aktivační událost HTTP vrátí HTTP 200 OK s prázdným textem ve funkcích 1. x nebo HTTP 204 bez obsahu s prázdným textem ve funkcích 2. x.

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* . Pro C# knihovny tříd nejsou k dispozici žádné vlastnosti atributů, které by odpovídaly těmto vlastnostem *Functions. JSON* .

|Vlastnost  |Popis  |
|---------|---------|
| **type** |Musí být nastaveno na `http`. |
| **direction** | Musí být nastaveno na `out`. |
|**name** | Název proměnné použitý v kódu funkce pro odpověď nebo `$return` pro použití návratové hodnoty. |

## <a name="output---usage"></a>Výstup – využití

K odeslání odpovědi HTTP použijte standardní vzory odezvy jazyka. V C# nebo C# skriptu, nastavte návratový typ `IActionResult` funkce nebo. `Task<IActionResult>` V C#nástroji není atribut návratové hodnoty požadován.

Například odpovědi najdete v [příkladu triggeru](#trigger---example).

## <a name="next-steps"></a>Další postup

[Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)
