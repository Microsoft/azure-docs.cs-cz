---
title: Aktivační událost HTTP funkce Azure
description: Přečtěte si, jak volat funkci Azure přes HTTP.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 045f3ccdc8dc09bf657ab39ce15a0d0524c73fcb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277593"
---
# <a name="azure-functions-http-trigger"></a>Aktivační událost HTTP funkce Azure

Aktivační událost HTTP umožňuje vyvolat funkci s požadavkem HTTP. Aktivační událost HTTP můžete použít k sestavení bezserverových api a reagovat na webhooky.

Výchozí vrácená hodnota pro funkci spouštěnou protokolem HTTP je:

- `HTTP 204 No Content`s prázdným tělem ve funkcích 2.x a vyšší
- `HTTP 200 OK`s prázdným tělem ve funkcích 1.x

Chcete-li upravit odpověď HTTP, nakonfigurujte [výstupní vazbu](./functions-bindings-http-webhook-output.md).

Další informace o vazbách PROTOKOLU HTTP naleznete v [přehledu](./functions-bindings-http-webhook.md) a [odkazu na výstupní vazbu](./functions-bindings-http-webhook-output.md).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>Příklad

# <a name="c"></a>[C #](#tab/csharp)

Následující příklad ukazuje [funkci Jazyka C#,](functions-dotnet-class-library.md) která hledá `name` parametr v řetězci dotazu nebo v těle požadavku HTTP. Všimněte si, že vrácená hodnota se používá pro výstupní vazbu, ale atribut vrácené hodnoty není vyžadován.

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

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Následující příklad ukazuje aktivační vazby v souboru *function.json* a [c# skript funkce,](functions-reference-csharp.md) která používá vazbu. Funkce hledá `name` parametr v řetězci dotazu nebo v těle požadavku HTTP.

Zde je *soubor function.json:*

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

[Konfigurační](#configuration) část vysvětluje tyto vlastnosti.

Zde je C# skript kód, `HttpRequest`který váže na :

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

Můžete vytvořit vazbu na `HttpRequest`vlastní objekt namísto . Tento objekt je vytvořen z těla požadavku a analyzován jako JSON. Podobně typ může být předán do výstupu odpovědi HTTP vazby a vráceny jako tělo odpovědi, spolu se stavovým kódem. `200`

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

Následující příklad ukazuje aktivační vazbu v souboru *function.json* a [funkci JavaScript,](functions-reference-node.md) která používá vazbu. Funkce hledá `name` parametr v řetězci dotazu nebo v těle požadavku HTTP.

Zde je *soubor function.json:*

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

[Konfigurační](#configuration) část vysvětluje tyto vlastnosti.

Zde je kód JavaScript:

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

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje aktivační vazby v souboru *function.json* a [funkce Pythonu,](functions-reference-python.md) který používá vazbu. Funkce hledá `name` parametr v řetězci dotazu nebo v těle požadavku HTTP.

Zde je *soubor function.json:*

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

[Konfigurační](#configuration) část vysvětluje tyto vlastnosti.

Zde je kód Pythonu:

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

# <a name="java"></a>[Java](#tab/java)

* [Parametr čtení z řetězce dotazu](#read-parameter-from-the-query-string)
* [Čtení textu z požadavku POST](#read-body-from-a-post-request)
* [Parametr čtení z trasy](#read-parameter-from-a-route)
* [Čtení těla POJO z požadavku POST](#read-pojo-body-from-a-post-request)

Následující příklady ukazují vazbu aktivační události HTTP.

#### <a name="read-parameter-from-the-query-string"></a>Parametr čtení z řetězce dotazu

Tento příklad přečte `id`parametr s názvem , z řetězce dotazu a použije jej k sestavení `application/json`dokumentu JSON vráceného klientovi s typem obsahu .

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

#### <a name="read-body-from-a-post-request"></a>Čtení textu z požadavku POST

Tento příklad přečte tělo požadavku POST `String`jako a a používá jej k vytvoření dokumentu JSON vráceného klientovi s typem `application/json`obsahu .

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

#### <a name="read-parameter-from-a-route"></a>Parametr čtení z trasy

Tento příklad přečte povinný `id`parametr s `name` názvem a volitelný parametr z cesty cesty cesty a použije je k `application/json`vytvoření dokumentu JSON vráceného klientovi s typem obsahu . T

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

#### <a name="read-pojo-body-from-a-post-request"></a>Čtení těla POJO z požadavku POST

Zde je kód `ToDoItem` pro třídu, odkazuje v tomto příkladu:

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

Tento příklad přečte tělo požadavku POST. Tělo požadavku získá automaticky de-serialized do objektu `ToDoItem` a je vrácena klientovi, s typem `application/json`obsahu . Parametr `ToDoItem` je serializován funkcí runtime, protože je `body` přiřazen `HttpMessageResponse.Builder` vlastnosti třídy.

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

---

## <a name="attributes-and-annotations"></a>Atributy a poznámky

V [knihovnách tříd Jazyka C#](functions-dotnet-class-library.md) a jazyce Java je `HttpTrigger` atribut k dispozici pro konfiguraci funkce.

Můžete nastavit úroveň autorizace a povolené metody HTTP v parametrech konstruktoru atributů, typu webhooku a šabloně trasy. Další informace o těchto nastaveních naleznete v [tématu konfigurace](#configuration).

# <a name="c"></a>[C #](#tab/csharp)

Tento příklad ukazuje, jak používat [httptrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) atribut.

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Úplný příklad naleznete v [příkladu aktivační události](#example).

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Atributy nejsou podporovány skriptem jazyka C#.

# <a name="javascript"></a>[Javascript](#tab/javascript)

Atributy nejsou podporovány javascriptem.

# <a name="python"></a>[Python](#tab/python)

Atributy nejsou podporovány Pythonem.

# <a name="java"></a>[Java](#tab/java)

Tento příklad ukazuje, jak používat [httptrigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java) atribut.

```java
@FunctionName("HttpTriggerJava")
public HttpResponseMessage<String> HttpTrigger(
        @HttpTrigger(name = "req",
                     methods = {"get"},
                     authLevel = AuthorizationLevel.ANONYMOUS) HttpRequestMessage<String> request,
        final ExecutionContext context) {

    ...
}
```

Úplný příklad naleznete v [příkladu aktivační události](#example).

---

## <a name="configuration"></a>Konfigurace

Následující tabulka vysvětluje vlastnosti konfigurace vazby, které jste nastavili `HttpTrigger` v souboru *function.json* a atributu.

|vlastnost function.json | Vlastnost atributu |Popis|
|---------|---------|----------------------|
| **Typ** | neuvedeno| Povinné - musí `httpTrigger`být nastavena na . |
| **direction** | neuvedeno| Povinné - musí `in`být nastavena na . |
| **Jméno** | neuvedeno| Povinné - název proměnné použitý v kódu funkce pro tělo požadavku nebo požadavku. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Určuje, jaké klíče, pokud existuje, musí být k dispozici v požadavku, aby bylo možné vyvolat funkci. Úroveň autorizace může být jedna z následujících hodnot: <ul><li><code>anonymous</code>&mdash;Není vyžadován žádný klíč rozhraní API.</li><li><code>function</code>&mdash;Je vyžadován klíč rozhraní API specifický pro funkci. Toto je výchozí hodnota, pokud není k dispozici žádná.</li><li><code>admin</code>&mdash;Je vyžadován hlavní klíč.</li></ul> Další informace naleznete v části o [autorizačních klíčích](#authorization-keys). |
| **Metody** |**Metody** | Pole metody HTTP, na které funkce reaguje. Pokud není zadán, funkce reaguje na všechny metody HTTP. Viz [přizpůsobení koncového bodu HTTP](#customize-the-http-endpoint). |
| **Postupu** | **Postupu** | Definuje šablonu trasy a řídí, na které adresy URL požadavků vaše funkce reaguje. Výchozí hodnota, pokud není `<functionname>`k dispozici žádný je . Další informace naleznete [v tématu přizpůsobení koncového bodu HTTP](#customize-the-http-endpoint). |
| **webHookType** | **Typ webhooku** | _Podporováno pouze pro modul runtime verze 1.x._<br/><br/>Nakonfiguruje aktivační událost PROTOKOLU HTTP tak, aby pro zadaného zprostředkovatele působila jako přijímač [webhooku.](https://en.wikipedia.org/wiki/Webhook) Nenastavujte vlastnost, `methods` pokud nastavíte tuto vlastnost. Typ webhooku může být jedna z následujících hodnot:<ul><li><code>genericJson</code>&mdash;Univerzální koncový bod webhooku bez logiky pro konkrétního zprostředkovatele. Toto nastavení omezuje požadavky pouze na ty, `application/json` kteří používají protokol HTTP POST a typ obsahu.</li><li><code>github</code>&mdash;Funkce reaguje na [webové háčky GitHub](https://developer.github.com/webhooks/). Nepoužívejte vlastnost _authLevel_ s webovými háčky GitHub. Další informace najdete v části GitHub webhooks dále v tomto článku.</li><li><code>slack</code>&mdash;Funkce reaguje na [slack webhooks](https://api.slack.com/outgoing-webhooks). Nepoužívejte _vlastnost authLevel_ s webhooky Slack. Další informace naleznete v části Slack webhooks dále v tomto článku.</li></ul>|

## <a name="payload"></a>Datová část

Typ vstupu aktivační události `HttpRequest` je deklarován jako jeden nebo vlastní typ. Pokud zvolíte `HttpRequest`, získáte úplný přístup k objektu požadavku. Pro vlastní typ se runtime pokusí analyzovat tělo požadavku JSON pro nastavení vlastností objektu.

## <a name="customize-the-http-endpoint"></a>Přizpůsobení koncového bodu HTTP

Ve výchozím nastavení při vytváření funkce pro aktivační událost PROTOKOLU HTTP je funkce adresovatelná pomocí trasy formuláře:

    http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>

Tuto trasu můžete přizpůsobit `route` pomocí volitelné vlastnosti na vstupní vazbě aktivační události PROTOKOLU HTTP. Například následující soubor *function.json* definuje `route` vlastnost pro aktivační událost HTTP:

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

Při použití této konfigurace je nyní funkce adresovatelná s následující trasou namísto původní trasy.

```
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

Tato konfigurace umožňuje, aby kód funkce podporoval dva parametry v adrese, _kategorii_ a _id_.

# <a name="c"></a>[C #](#tab/csharp)

S parametry můžete použít libovolné [omezení trasy webového rozhraní API.](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) Následující kód funkce Jazyka C# využívá oba parametry.

```csharp
using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

S parametry můžete použít libovolné [omezení trasy webového rozhraní API.](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) Následující kód funkce Jazyka C# využívá oba parametry.

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;

public static IActionResult Run(HttpRequest req, string category, int? id, ILogger log)
{
    var message = String.Format($"Category: {category}, ID: {id}");
    return (ActionResult)new OkObjectResult(message);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

V uzlu funkce runtime poskytuje tělo požadavku `context` z objektu. Další informace naleznete v [příkladu aktivační události JavaScriptu](#example).

Následující příklad ukazuje, jak číst `context.bindingData`parametry trasy z .

```javascript
module.exports = function (context, req) {

    var category = context.bindingData.category;
    var id = context.bindingData.id;
    var message = `Category: ${category}, ID: ${id}`;

    context.res = {
        body: message;
    }

    context.done();
}
```

# <a name="python"></a>[Python](#tab/python)

Kontext spuštění funkce je vystaven prostřednictvím parametru deklarovaného jako `func.HttpRequest`. Tato instance umožňuje funkci přístup k parametrům datové ho trasy, hodnotám řetězce dotazu a metodám, které umožňují vrátit odpovědi HTTP.

Jakmile jsou definovány, parametry trasy jsou `route_params` k dispozici funkci voláním metody.

```python
import logging

import azure.functions as func

def main(req: func.HttpRequest) -> func.HttpResponse:

    category = req.route_params.get('category')
    id = req.route_params.get('id')
    message = f"Category: {category}, ID: {id}"

    return func.HttpResponse(message)
```

# <a name="java"></a>[Java](#tab/java)

Kontext spuštění funkce je vlastnosti `HttpTrigger` deklarované v atributu. Atribut umožňuje definovat parametry trasy, úrovně autorizace, slovesa HTTP a instanci příchozího požadavku.

Parametry trasy jsou `HttpTrigger` definovány pomocí atributu.

```java
package com.function;

import java.util.*;
import com.microsoft.azure.functions.annotation.*;
import com.microsoft.azure.functions.*;

public class HttpTriggerJava {
    public HttpResponseMessage<String> HttpTrigger(
            @HttpTrigger(name = "req",
                         methods = {"get"},
                         authLevel = AuthorizationLevel.FUNCTION,
                         route = "products/{category:alpha}/{id:int}") HttpRequestMessage<String> request,
            @BindingName("category") String category,
            @BindingName("id") int id,
            final ExecutionContext context) {

        String message = String.format("Category  %s, ID: %d", category, id);
        return request.createResponseBuilder(HttpStatus.OK).body(message).build();
    }
}
```

---

Ve výchozím nastavení jsou všechny trasy funkcí předponou *api*. Předponu můžete také přizpůsobit nebo `http.routePrefix` odebrat pomocí vlastnosti v souboru [host.json.](functions-host-json.md) Následující příklad odebere předponu směrování *rozhraní API* pomocí prázdného řetězce pro předponu v souboru *host.json.*

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

## <a name="using-route-parameters"></a>Použití parametrů trasy

Parametry trasy, které definovaly `route` vzor funkce, jsou k dispozici pro každou vazbu. Například pokud máte trasu `"route": "products/{id}"` definovanou jako pak tabulka vazby `{id}` úložiště můžete použít hodnotu parametru v konfiguraci vazby.

Následující konfigurace ukazuje, `{id}` jak je parametr předán `rowKey`vazbě .

```json
{
    "type": "table",
    "direction": "in",
    "name": "product",
    "partitionKey": "products",
    "tableName": "products",
    "rowKey": "{id}"
}
```

## <a name="working-with-client-identities"></a>Práce s identitami klientů

Pokud vaše aplikace funkce používá [ověřování / autorizaci služby App Service](../app-service/overview-authentication-authorization.md), můžete zobrazit informace o ověřených klientech z vašeho kódu. Tyto informace jsou k dispozici jako [hlavičky požadavku vložené platformou](../app-service/app-service-authentication-how-to.md#access-user-claims). 

Tyto informace můžete také číst z dat vazby. Tato funkce je k dispozici pouze funkce runtime v 2.x a vyšší. V současné době je také k dispozici pouze pro jazyky .NET.

# <a name="c"></a>[C #](#tab/csharp)

Informace týkající se ověřených klientů jsou k dispozici jako [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal je k dispozici jako součást kontextu požadavku, jak je znázorněno v následujícím příkladu:

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

Alternativně ClaimsPrincipal lze jednoduše zahrnout jako další parametr v podpisu funkce:

```csharp
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

# <a name="c-script"></a>[Skript jazyka C#](#tab/csharp-script)

Informace týkající se ověřených klientů jsou k dispozici jako [ClaimsPrincipal](https://docs.microsoft.com/dotnet/api/system.security.claims.claimsprincipal). ClaimsPrincipal je k dispozici jako součást kontextu požadavku, jak je znázorněno v následujícím příkladu:

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

Alternativně ClaimsPrincipal lze jednoduše zahrnout jako další parametr v podpisu funkce:

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

Ověřený uživatel je k dispozici prostřednictvím [hlavičky HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="python"></a>[Python](#tab/python)

Ověřený uživatel je k dispozici prostřednictvím [hlavičky HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="java"></a>[Java](#tab/java)

Ověřený uživatel je k dispozici prostřednictvím [hlavičky HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

---

## <a name="authorization-keys"></a>Autorizační klíče

Funkce umožňuje používat klávesy, aby bylo těžší přístup ke koncovým bodům funkce HTTP během vývoje.  Pokud není úroveň autorizace protokolu HTTP `anonymous`na funkci aktivované protokolem HTTP nastavena na , musí požadavky do požadavku zahrnout klíč rozhraní API. 

> [!IMPORTANT]
> Zatímco klíče může pomoci zamlžovat koncové body HTTP během vývoje, nejsou určeny jako způsob, jak zabezpečit aktivační událost HTTP v produkčním prostředí. Další informace najdete [v tématu Zabezpečení koncového bodu HTTP v produkčním prostředí](#secure-an-http-endpoint-in-production).

> [!NOTE]
> V běhu functions 1.x webhooku mohou poskytovatelé webhooku používat klíče k autorizaci požadavků různými způsoby v závislosti na tom, co zprostředkovatel podporuje. To je pokryto [Webhooks a klíče](#webhooks-and-keys). Functions runtime ve verzi 2.x a vyšší nezahrnuje integrovanou podporu pro zprostředkovatele webhooku.

#### <a name="authorization-scopes-function-level"></a>Obory autorizace (úroveň funkce)

Existují dva obory autorizace pro klíče na úrovni funkce:

* **Funkce**: Tyto klávesy platí pouze pro konkrétní funkce, pod kterými jsou definovány. Při použití jako klíč rozhraní API, tyto umožňují pouze přístup k této funkci.

* **Hostitel**: Klíče s rozsahem hostitele lze použít pro přístup ke všem funkcím v rámci aplikace funkce. Při použití jako klíč rozhraní API, které umožňují přístup k libovolné funkce v rámci aplikace funkce. 

Každý klíč je pojmenován jako odkaz a na úrovni funkce a hostitele je výchozí klíč (s názvem "výchozí"). Funkční klávesy mají přednost před hostitelskými klíči. Pokud jsou definovány dvě klávesy se stejným názvem, je vždy použita funkční klávesa.

#### <a name="master-key-admin-level"></a>Hlavní klíč (na úrovni správce) 

Každá aplikace funkcí má také klíč `_master`hostitele na úrovni správce s názvem . Kromě poskytování přístupu na úrovni hostitele ke všem funkcím v aplikaci poskytuje hlavní klíč také přístup pro správu k runtime REST API. Tento klíč nelze odvolat. Pokud nastavíte úroveň `admin`autorizace aplikace , musí požadavky používat hlavní klíč. jakékoli jiné klíčové výsledky při selhání autorizace.

> [!CAUTION]  
> Vzhledem ke zvýšeným oprávněním v aplikaci funkcí udělených hlavním klíčem byste neměli tento klíč sdílet s třetími stranami ani jej distribuovat v nativních klientských aplikacích. Při výběru úrovně autorizace správce buďte opatrní.

## <a name="obtaining-keys"></a>Získání klíčů

Klíče se ukládají jako součást vaší funkční aplikace v Azure a jsou šifrované v klidovém stavu. Pokud chcete zobrazit klíče, vytvořte nové nebo přejděte na nové hodnoty, přejděte na jednu z funkcí aktivovaných HTTP na [webu Azure portal](https://portal.azure.com) a vyberte **Spravovat**.

![Správa funkčních kláves na portálu.](./media/functions-bindings-http-webhook/manage-function-keys.png)

Pomocí řešení API pro [správu klíčů](https://github.com/Azure/azure-functions-host/wiki/Key-management-API)můžete získat programové klávesy .

## <a name="api-key-authorization"></a>Autorizace klíče rozhraní API

Většina šablon aktivačních událostí PROTOKOLU HTTP vyžaduje klíč rozhraní API v požadavku. Takže váš požadavek HTTP obvykle vypadá jako následující adresa URL:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

Klíč může být zahrnut do proměnné `code`řetězce dotazu s názvem , jak je uvedeno výše. Může být také součástí `x-functions-key` hlavičky HTTP. Hodnota klíče může být libovolný funkční klíč definovaný pro funkci nebo libovolný klíč hostitele.

Můžete povolit anonymní požadavky, které nevyžadují klíče. Můžete také požadovat, aby byl použit hlavní klíč. Výchozí úroveň autorizace můžete `authLevel` změnit pomocí vlastnosti ve vazbě JSON. Další informace naleznete v tématu [Trigger - configuration](#configuration).

> [!NOTE]
> Při místním spuštění funkcí je autorizace zakázána bez ohledu na zadané nastavení úrovně autorizace. Po publikování do `authLevel` Azure se vynucuje nastavení v aktivační události. Klíče jsou stále vyžadovány při místním spuštění [v kontejneru](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally).


## <a name="secure-an-http-endpoint-in-production"></a>Zabezpečení koncového bodu HTTP v produkčním prostředí

Chcete-li plně zabezpečit koncové body funkce v produkčním prostředí, měli byste zvážit implementaci jedné z následujících možností zabezpečení na úrovni aplikace:

* Zapněte ověřování služby App Service / autorizace pro vaši funkční aplikaci. Platforma služby App Service umožňuje k ověřování klientů používat Azure Active Directory (AAD) a několik poskytovatelů identit třetích stran. Tuto strategii můžete použít k implementaci vlastních pravidel autorizace pro vaše funkce a můžete pracovat s informacemi o uživateli z kódu funkce. Další informace najdete [v tématu Ověřování a autorizace ve službě Azure App Service](../app-service/overview-authentication-authorization.md) a Práce s [identitami klientů](#working-with-client-identities).

* K ověřování požadavků použijte Azure API Management (APIM). APIM poskytuje celou řadu možností zabezpečení rozhraní API pro příchozí požadavky. Další informace najdete v tématu [Zásady ověřování správy rozhraní API](../api-management/api-management-authentication-policies.md). S APIM na místě, můžete nakonfigurovat aplikace funkce přijímat požadavky pouze z IP adresy instance APIM. Další informace naleznete v tématu [omezení IP adres](ip-addresses.md#ip-address-restrictions).

* Nasaďte aplikaci funkce do prostředí Azure App Service Environment (ASE). ASE poskytuje vyhrazené hostitelské prostředí, ve kterém můžete spouštět funkce. ASE umožňuje nakonfigurovat jednu front-endbránu, kterou můžete použít k ověření všech příchozích požadavků. Další informace naleznete [v tématu Konfigurace brány firewall webových aplikací (WAF) pro prostředí služby App Service](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

Při použití jedné z těchto metod zabezpečení na úrovni aplikace byste měli `anonymous`nastavit úroveň autorizace funkce spouštěnou protokolem HTTP na .

## <a name="webhooks"></a>Webhooky

> [!NOTE]
> Režim Webhook je k dispozici pouze pro verzi 1.x běhu Functions. Tato změna byla provedena ke zlepšení výkonu aktivačních událostí PROTOKOLU HTTP ve verzi 2.x a vyšší.

Ve verzi 1.x, webhook šablony poskytují další ověření pro webhooku datové části. Ve verzi 2.x a vyšší, základní aktivační událost HTTP stále funguje a je doporučený přístup pro webhooky. 

### <a name="github-webhooks"></a>Webové háčky GitHub

Chcete-li reagovat na webové háčky GitHub, nejprve vytvořte funkci `github`pomocí aktivační události HTTP a nastavte vlastnost **webHookType** na . Pak zkopírujte jeho URL a klíč ROZHRANÍ API do stránky **Přidat webhook** u úložiště GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Slack webhooky

Webhook Slack generuje token pro vás místo toho, aby vám zadávat, takže je nutné nakonfigurovat klíč specifický pro funkci s tokenem z Slack. Viz [Autorizační klíče](#authorization-keys).

## <a name="webhooks-and-keys"></a>Webové háky a klíče

Autorizace webhooku je zpracována komponentou přijímače webhooku, součástí aktivační události HTTP a mechanismus se liší v závislosti na typu webhooku. Každý mechanismus se spoléhat na klíč. Ve výchozím nastavení se používá funkční klávesa s názvem "default". Chcete-li použít jiný klíč, nakonfigurujte zprostředkovatele webhooku tak, aby odeslal název klíče s požadavkem jedním z následujících způsobů:

* **Řetězec dotazu**: Zprostředkovatel předá `clientid` název klíče v `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`parametru řetězce dotazu, například .
* **Hlavička požadavku**: Zprostředkovatel předá `x-functions-clientid` název klíče v záhlaví.

## <a name="limits"></a>Omezení

Délka požadavku HTTP je omezena na 100 MB (104 857 600 bajtů) a délka adresy URL je omezena na 4 kB (4 096 bajtů). Tato omezení jsou `httpRuntime` určena prvkem [souboru Web.config](https://github.com/Azure/azure-functions-host/blob/3.x/src/WebJobs.Script.WebHost/web.config)za běhu .

Pokud funkce, která používá aktivační událost HTTP nedokončí do 230 sekund, [azure nástroj pro vyrovnávání zatížení](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) bude časový plán a vrátí chybu HTTP 502. Funkce bude pokračovat v běhu, ale nebude moci vrátit odpověď HTTP. Pro dlouhotrvající funkce doporučujeme sledovat asynchronní vzory a vrátit umístění, kde můžete ping stav požadavku. Informace o tom, jak dlouho může funkce běžet, naleznete v [tématu Škálování a hostování – plán spotřeby](functions-scale.md#timeout).


## <a name="next-steps"></a>Další kroky

- [Vrácení odpovědi HTTP z funkce](./functions-bindings-http-webhook-output.md)
