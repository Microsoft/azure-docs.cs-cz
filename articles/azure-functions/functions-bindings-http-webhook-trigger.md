---
title: Aktivační událost Azure Functions HTTP
description: Naučte se volat Azure Function přes HTTP.
author: craigshoemaker
ms.topic: reference
ms.date: 02/21/2020
ms.author: cshoe
ms.openlocfilehash: 045f3ccdc8dc09bf657ab39ce15a0d0524c73fcb
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "78162958"
---
# <a name="azure-functions-http-trigger"></a>Aktivační událost Azure Functions HTTP

Trigger HTTP umožňuje vyvolat funkci s požadavkem HTTP. Pomocí triggeru HTTP můžete vytvářet rozhraní API bez serveru a reagovat na Webhooky.

Výchozí návratová hodnota funkce aktivované protokolem HTTP je:

- `HTTP 204 No Content` s prázdným textem ve funkcích 2. x a vyšší
- `HTTP 200 OK` s prázdným textem ve funkcích 1. x

Chcete-li upravit odpověď protokolu HTTP, nakonfigurujte [výstupní vazbu](./functions-bindings-http-webhook-output.md).

Další informace o vazbách HTTP naleznete v tématu [Přehled](./functions-bindings-http-webhook.md) a [výstupní odkaz na vazbu](./functions-bindings-http-webhook-output.md).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="example"></a>Příklad

# <a name="c"></a>[C#](#tab/csharp)

Následující příklad ukazuje [ C# funkci](functions-dotnet-class-library.md) , která vyhledává parametr `name` buď v řetězci dotazu, nebo v těle požadavku HTTP. Všimněte si, že návratová hodnota se používá pro výstupní vazbu, ale atribut návratové hodnoty není povinný.

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

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

Následující příklad ukazuje aktivační vazbu v souboru *Function. JSON* a [ C# funkci skriptu](functions-reference-csharp.md) , která používá vazbu. Funkce vyhledá parametr `name` buď v řetězci dotazu, nebo v těle požadavku HTTP.

Tady je soubor *Function. JSON* :

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

Zde je C# kód skriptu, který se váže k `HttpRequest`:

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

Místo `HttpRequest`můžete vytvořit vazby na vlastní objekt. Tento objekt se vytvoří z těla žádosti a analyzuje se jako JSON. Podobně lze typ předat výstupní vazbě odpovědi HTTP a vrátí se jako tělo odpovědi spolu s kódem stavu `200`.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Následující příklad ukazuje aktivační vazbu v souboru *Function. JSON* a [funkci JavaScriptu](functions-reference-node.md) , která používá vazbu. Funkce vyhledá parametr `name` buď v řetězci dotazu, nebo v těle požadavku HTTP.

Tady je soubor *Function. JSON* :

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

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

# <a name="python"></a>[Python](#tab/python)

Následující příklad ukazuje aktivační vazbu v souboru *Function. JSON* a [funkci Pythonu](functions-reference-python.md) , která používá vazbu. Funkce vyhledá parametr `name` buď v řetězci dotazu, nebo v těle požadavku HTTP.

Tady je soubor *Function. JSON* :

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

Tyto vlastnosti jsou vysvětleny v části [Konfigurace](#configuration) .

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

# <a name="java"></a>[Java](#tab/java)

* [Načíst parametr z řetězce dotazu](#read-parameter-from-the-query-string)
* [Přečíst tělo žádosti POST](#read-body-from-a-post-request)
* [Načíst parametr z trasy](#read-parameter-from-a-route)
* [Číst tělo POJO z požadavku POST](#read-pojo-body-from-a-post-request)

Následující příklady znázorňují vazbu triggeru HTTP.

#### <a name="read-parameter-from-the-query-string"></a>Načíst parametr z řetězce dotazu

Tento příklad přečte parametr s názvem `id`, z řetězce dotazu a použije ho k sestavení dokumentu JSON vráceného klientovi, s typem obsahu `application/json`.

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

#### <a name="read-body-from-a-post-request"></a>Přečíst tělo žádosti POST

V tomto příkladu se přečte tělo žádosti POST, jako `String`a použije se k sestavení dokumentu JSON vráceného klientovi s typem obsahu `application/json`.

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

#### <a name="read-parameter-from-a-route"></a>Načíst parametr z trasy

Tento příklad přečte povinný parametr s názvem `id`a volitelný parametr `name` z cesty trasy a použije je k sestavení dokumentu JSON vráceného klientovi s typem obsahu `application/json`. T

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

#### <a name="read-pojo-body-from-a-post-request"></a>Číst tělo POJO z požadavku POST

Zde je kód pro třídu `ToDoItem`, na kterou se odkazuje v tomto příkladu:

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

Tento příklad přečte tělo žádosti POST. Text žádosti se automaticky rozdělí do objektu `ToDoItem` a vrátí se klientovi s typem obsahu `application/json`. Parametr `ToDoItem` je serializován modulem runtime Functions, protože je přiřazen vlastnosti `body` třídy `HttpMessageResponse.Builder`.

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

V [ C# knihovnách tříd](functions-dotnet-class-library.md) a Java je k dispozici atribut `HttpTrigger` pro konfiguraci funkce.

Můžete nastavit úroveň autorizace a povolené metody HTTP v parametrech konstruktoru atributu, typ Webhooku a šablonu směrování. Další informace o těchto nastaveních najdete v tématu věnovaném [konfiguraci](#configuration).

# <a name="c"></a>[C#](#tab/csharp)

Tento příklad ukazuje, jak použít atribut [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) .

```csharp
[FunctionName("HttpTriggerCSharp")]
public static Task<IActionResult> Run(
    [HttpTrigger(AuthorizationLevel.Anonymous)] HttpRequest req)
{
    ...
}
```

Úplný příklad najdete v [příkladu triggeru](#example).

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

C# Skript nepodporuje atributy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Atributy nejsou podporovány jazykem JavaScript.

# <a name="python"></a>[Python](#tab/python)

Python nepodporuje atributy.

# <a name="java"></a>[Java](#tab/java)

Tento příklad ukazuje, jak použít atribut [HttpTrigger](https://github.com/Azure/azure-functions-java-library/blob/dev/src/main/java/com/microsoft/azure/functions/annotation/HttpTrigger.java) .

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

Úplný příklad najdete v [příkladu triggeru](#example).

---

## <a name="configuration"></a>Konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v souboru *Function. JSON* a atributu `HttpTrigger`.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
| **type** | neuvedeno| Požadováno – musí být nastavené na `httpTrigger`. |
| **direction** | neuvedeno| Požadováno – musí být nastavené na `in`. |
| **Jméno** | neuvedeno| Required – název proměnné použitý v kódu funkce pro text žádosti nebo žádosti. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Určuje, které klíče (pokud existují) musí být k žádosti přítomny, aby bylo možné funkci vyvolat. Úroveň autorizace může být jedna z následujících hodnot: <ul><li><code>anonymous</code>&mdash;není vyžadován žádný klíč rozhraní API.</li><li><code>function</code>&mdash;je vyžadován klíč rozhraní API specifický pro danou funkci. Toto je výchozí hodnota, pokud není zadána žádná.</li><li><code>admin</code>&mdash;je vyžadován hlavní klíč.</li></ul> Další informace najdete v části o [autorizačních klíčích](#authorization-keys). |
| **způsobů** |**Způsobů** | Pole metod HTTP, na které funkce reaguje. Pokud není zadaný, funkce reaguje na všechny metody HTTP. Viz [přizpůsobení koncového bodu http](#customize-the-http-endpoint). |
| **cestě** | **Cestě** | Definuje šablonu směrování, která řídí, které adresy URL žádostí vaše funkce reaguje. Výchozí hodnota, pokud není zadána, je `<functionname>`. Další informace najdete v tématu [přizpůsobení koncového bodu http](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** | _Podporováno pouze pro modul runtime verze 1. x._<br/><br/>Nakonfiguruje Trigger HTTP tak, aby sloužil jako přijímač [Webhooku](https://en.wikipedia.org/wiki/Webhook) pro zadaného zprostředkovatele. Pokud jste tuto vlastnost nastavili, nenastavujte vlastnost `methods`. Typ Webhooku může být jedna z následujících hodnot:<ul><li><code>genericJson</code>&mdash;koncový bod Webhooku pro obecné účely bez logiky pro konkrétního poskytovatele. Toto nastavení omezuje požadavky jenom na ty, které používají HTTP POST a s typem obsahu `application/json`.</li><li><code>github</code>&mdash;funkce reaguje na [Webhooky GitHubu](https://developer.github.com/webhooks/). Nepoužívejte vlastnost _authLevel_ s Webhooky GitHubu. Další informace najdete v části Webhooky GitHubu dále v tomto článku.</li><li><code>slack</code>&mdash;funkce reaguje na [Webhooky časové rezervy](https://api.slack.com/outgoing-webhooks). Nepoužívejte vlastnost _authLevel_ s Webhooky časové rezervy. Další informace najdete v části časová pole webhooků dále v tomto článku.</li></ul>|

## <a name="payload"></a>Délka

Vstupní typ triggeru je deklarován jako buď `HttpRequest`, nebo jako vlastní typ. Pokud zvolíte možnost `HttpRequest`, získáte úplný přístup k objektu Request. Pro vlastní typ se modul runtime pokusí analyzovat tělo požadavku JSON pro nastavení vlastností objektu.

## <a name="customize-the-http-endpoint"></a>Přizpůsobení koncového bodu HTTP

Ve výchozím nastavení platí, že když vytvoříte funkci triggeru HTTP, funkce je adresovat s cestou formuláře:

    http://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>

Tuto trasu můžete přizpůsobit pomocí vlastnosti volitelné `route` vstupní vazby triggeru protokolu HTTP. Například následující soubor *Function. JSON* definuje vlastnost `route` pro Trigger http:

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
http://<APP_NAME>.azurewebsites.net/api/products/electronics/357
```

Tato konfigurace umožňuje, aby kód funkce podporoval dva parametry v adrese, _kategorii_ a _ID_.

# <a name="c"></a>[C#](#tab/csharp)

Pomocí parametrů můžete použít jakékoli [omezení trasy webového rozhraní API](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) . Následující C# kód funkce využívá oba parametry.

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

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

Pomocí parametrů můžete použít jakékoli [omezení trasy webového rozhraní API](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) . Následující C# kód funkce využívá oba parametry.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

V uzlu poskytuje modul runtime Functions tělo žádosti z objektu `context`. Další informace najdete v [příkladu triggeru JavaScriptu](#example).

Následující příklad ukazuje, jak číst parametry směrování z `context.bindingData`.

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

Kontext spuštění funkce je zveřejněn prostřednictvím parametru deklarovaného jako `func.HttpRequest`. Tato instance umožňuje funkci přistupovat k parametrům datových tras, hodnotám řetězce dotazu a metodám, které umožňují vracet odpovědi HTTP.

Po definování jsou parametry směrování k dispozici funkci voláním metody `route_params`.

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

Kontext spuštění funkce je vlastnosti deklarované v atributu `HttpTrigger`. Atribut umožňuje definovat parametry směrování, úrovně autorizace, příkazy HTTP a instanci příchozího požadavku.

Parametry směrování jsou definovány pomocí atributu `HttpTrigger`.

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

Ve výchozím nastavení jsou všechny trasy funkcí s předponou *rozhraní API*. Můžete také přizpůsobit nebo odebrat předponu pomocí vlastnosti `http.routePrefix` v souboru [Host. JSON](functions-host-json.md) . Následující příklad odebere předponu trasy *rozhraní API* pomocí prázdného řetězce pro předponu v souboru *Host. JSON* .

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

## <a name="using-route-parameters"></a>Použití parametrů směrování

Parametry směrování, které definují vzor `route` funkce jsou k dispozici pro každou vazbu. Například pokud máte trasu definovanou jako `"route": "products/{id}"` pak může vazba úložiště tabulky použít hodnotu parametru `{id}` v konfiguraci vazby.

Následující konfigurace ukazuje, jak se parametr `{id}` předává do `rowKey`vazby.

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

Pokud vaše aplikace Function App používá [App Service ověřování/autorizaci](../app-service/overview-authentication-authorization.md), můžete zobrazit informace o ověřených klientech z vašeho kódu. Tyto informace jsou k dispozici jako [hlavičky požadavků vložené platformou](../app-service/app-service-authentication-how-to.md#access-user-claims). 

Tyto informace můžete také přečíst z dat vazby. Tato funkce je dostupná pouze pro modul runtime Functions v 2. x a vyšší. K dispozici je také v současnosti jenom pro jazyky .NET.

# <a name="c"></a>[C#](#tab/csharp)

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

Alternativně je možné ClaimsPrincipal jednoduše zahrnout jako další parametr v signatuře funkce:

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

# <a name="c-script"></a>[C#Pravidel](#tab/csharp-script)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Ověřený uživatel je k dispozici prostřednictvím [hlaviček protokolu HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="python"></a>[Python](#tab/python)

Ověřený uživatel je k dispozici prostřednictvím [hlaviček protokolu HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

# <a name="java"></a>[Java](#tab/java)

Ověřený uživatel je k dispozici prostřednictvím [hlaviček protokolu HTTP](../app-service/app-service-authentication-how-to.md#access-user-claims).

---

## <a name="authorization-keys"></a>Autorizační klíče

Funkce umožňují používat klíče k tomu, aby během vývoje měly přístup k koncovým bodům funkce HTTP.  Pokud není úroveň autorizace protokolu HTTP u funkce aktivované protokolem HTTP nastavená na `anonymous`, požadavky musí v žádosti zahrnovat klíč rozhraní API. 

> [!IMPORTANT]
> I když klíče mohou během vývoje přispět k zařazování koncových bodů HTTP, nejsou určené jako způsob zabezpečení triggeru HTTP v produkčním prostředí. Další informace najdete v tématu [zabezpečení koncového bodu http v produkčním prostředí](#secure-an-http-endpoint-in-production).

> [!NOTE]
> V modulu runtime Functions 1. x můžou poskytovatelé Webhooku použít klíče k autorizaci požadavků různými způsoby v závislosti na tom, co poskytovatel podporuje. Tento vztah je popsaný v [webhookech a klíčích](#webhooks-and-keys). Modul runtime Functions ve verzi 2. x a vyšší neobsahuje integrovanou podporu pro poskytovatele webhooků.

#### <a name="authorization-scopes-function-level"></a>Obory autorizace (na úrovni funkcí)

Existují dva obory autorizace pro klíče na úrovni funkce:

* **Funkce**: tyto klíče se vztahují pouze na konkrétní funkce, ve kterých jsou definovány. Když se použije jako klíč rozhraní API, povolí přístup k této funkci.

* **Hostitel**: klíče s oborem hostitele lze použít pro přístup ke všem funkcím v aplikaci Function App. Při použití jako klíč rozhraní API umožňují tyto funkce přístup k libovolné funkci v aplikaci Function App. 

Každý klíč má název pro referenci a výchozí klíč (s názvem "výchozí") na úrovni funkce a hostitele. Klíče funkcí mají přednost před klíči hostitele. Pokud jsou definovány dva klíče se stejným názvem, je klíč funkce vždy použit.

#### <a name="master-key-admin-level"></a>Hlavní klíč (úroveň správce) 

Každá aplikace Function App má také klíč hostitele na úrovni správce s názvem `_master`. Kromě poskytování přístupu na úrovni hostitele ke všem funkcím v aplikaci poskytuje hlavní klíč také přístup správce k rozhraním REST API pro modul runtime. Tento klíč nelze odvolat. Když nastavíte úroveň autorizace `admin`, musí žádosti používat hlavní klíč. jakýkoli jiný klíč způsobí selhání autorizace.

> [!CAUTION]  
> Vzhledem ke zvýšeným oprávněním v aplikaci Function App udělené hlavním klíčem byste tento klíč neměli sdílet s třetími stranami nebo ho distribuovat v nativních klientských aplikacích. Při volbě úrovně autorizace Správce buďte opatrní.

## <a name="obtaining-keys"></a>Získání klíčů

Klíče se ukládají jako součást aplikace Function App v Azure a jsou zašifrované v klidovém stavu. Chcete-li zobrazit klíče, vytvořte nové nebo zaveďte klíče k novým hodnotám, v [Azure Portal](https://portal.azure.com) přejděte na jednu z funkcí AKTIVOVANÝch protokolem HTTP a vyberte **Spravovat**.

![Spravujte klíče funkcí na portálu.](./media/functions-bindings-http-webhook/manage-function-keys.png)

Klíče funkcí můžete získat programově pomocí [rozhraní API pro správu klíčů](https://github.com/Azure/azure-functions-host/wiki/Key-management-API).

## <a name="api-key-authorization"></a>Autorizace klíče rozhraní API

Většina šablon triggeru HTTP vyžaduje v žádosti klíč rozhraní API. Vaše žádost HTTP obvykle vypadá jako na následující adrese URL:

    https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?code=<API_KEY>

Klíč lze zahrnout do proměnné řetězce dotazu s názvem `code`, jak je uvedeno výše. Může být také zahrnutá v hlavičce `x-functions-key` HTTP. Hodnotou klíče může být libovolný klíč funkce definovaný pro funkci nebo libovolný klíč hostitele.

Můžete povolit anonymní požadavky, které nevyžadují klíče. Můžete také vyžadovat, aby byl hlavní klíč použit. Výchozí úroveň autorizace se mění pomocí vlastnosti `authLevel` ve formátu JSON vazby. Další informace najdete v tématu [Trigger-Configuration](#configuration).

> [!NOTE]
> Při místním spouštění funkcí je autorizace zakázaná bez ohledu na zadané nastavení úrovně autorizace. Po publikování do Azure se vynutilo nastavení `authLevel` v triggeru. Klíče jsou stále vyžadovány při spuštění [místně v kontejneru](functions-create-function-linux-custom-image.md#build-the-container-image-and-test-locally).


## <a name="secure-an-http-endpoint-in-production"></a>Zabezpečení koncového bodu HTTP v produkčním prostředí

Aby bylo možné plně zabezpečit koncové body funkcí v produkčním prostředí, měli byste zvážit implementaci jedné z následujících funkcí zabezpečení na úrovni aplikace.

* Zapněte App Service ověřování/autorizaci aplikace Function App. Platforma App Service umožňuje používat Azure Active Directory (AAD) a několik zprostředkovatelů identity třetích stran k ověřování klientů. Tuto strategii můžete použít k implementaci vlastních autorizačních pravidel pro vaše funkce a můžete pracovat s uživatelskými informacemi z kódu funkce. Další informace najdete v tématu [ověřování a autorizace v Azure App Service](../app-service/overview-authentication-authorization.md) a [práci s identitami klientů](#working-with-client-identities).

* K ověření požadavků použijte Azure API Management (APIM). APIM poskytuje celou řadu možností zabezpečení rozhraní API pro příchozí požadavky. Další informace najdete v tématu [zásady ověřování API Management](../api-management/api-management-authentication-policies.md). Pomocí APIM můžete nakonfigurovat aplikaci Function App, aby přijímala požadavky pouze z IP adresy vaší instance APIM. Další informace najdete v tématu [omezení IP adres](ip-addresses.md#ip-address-restrictions).

* Nasaďte aplikaci Function App na Azure App Service Environment (pomocného mechanismu). Pomocného mechanismu poskytuje vyhrazené hostitelské prostředí, ve kterém se mají spouštět vaše funkce. Pomocného uživatele vám umožní nakonfigurovat jednu front-end bránu, kterou můžete použít k ověřování všech příchozích požadavků. Další informace najdete v tématu [Konfigurace firewallu webových aplikací (WAF) pro App Service Environment](../app-service/environment/app-service-app-service-environment-web-application-firewall.md).

Při použití jedné z těchto metod zabezpečení na úrovni aplikace byste měli nastavit úroveň autorizace funkce aktivované protokolem HTTP na `anonymous`.

## <a name="webhooks"></a>Webhooky

> [!NOTE]
> Režim Webhooku je dostupný jenom pro verzi 1. x modulu runtime Functions. Tato změna byla provedena za účelem zlepšení výkonu aktivačních událostí HTTP ve verzi 2. x a vyšší.

V šablonách Webhooku verze 1. x poskytují další ověřování datových částí Webhooku. Ve verzi 2. x a vyšší stále funguje základní aktivační událost HTTP a je doporučený postup pro Webhooky. 

### <a name="github-webhooks"></a>Webhooky GitHubu

Pokud chcete reagovat na Webhooky GitHubu, nejprve vytvořte funkci pomocí triggeru HTTP a nastavte vlastnost **webHookType** na hodnotu `github`. Pak zkopírujte jeho adresu URL a klíč rozhraní API do stránky **Přidat Webhook** vašeho úložiště GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

### <a name="slack-webhooks"></a>Webhooky časové rezervy

Webhook časové rezervy vygeneruje token pro místo jeho zadání, takže musíte nakonfigurovat klíč specifický pro funkci s tokenem z časové rezervy. Podívejte se na [klíče pro autorizaci](#authorization-keys).

## <a name="webhooks-and-keys"></a>Webhooky a klíče

Autorizace Webhooku se zpracovává komponentou přijímače Webhooku, součástí triggeru HTTP a mechanismus se liší v závislosti na typu Webhooku. Každý mechanismus spoléhá na klíč. Ve výchozím nastavení se používá klíč funkce s názvem "výchozí". Chcete-li použít jiný klíč, nakonfigurujte poskytovatele Webhooku tak, aby odesílal název klíče s požadavkem jedním z následujících způsobů:

* **Řetězec dotazu**: poskytovatel předá název klíče v parametru řetězce dotazu `clientid`, například `https://<APP_NAME>.azurewebsites.net/api/<FUNCTION_NAME>?clientid=<KEY_NAME>`.
* **Hlavička žádosti**: poskytovatel předá název klíče v hlavičce `x-functions-clientid`.

## <a name="limits"></a>Omezení

Délka požadavku HTTP je omezená na 100 MB (104 857 600 bajtů) a délka adresy URL je omezená na 4 KB (4 096 bajtů). Tato omezení jsou určena `httpRuntime` prvkem [souboru Web. config](https://github.com/Azure/azure-functions-host/blob/3.x/src/WebJobs.Script.WebHost/web.config)modulu runtime.

Pokud funkce, která používá Trigger HTTP, nebude dokončena během 230 sekund, vyprší časový limit [Azure Load Balancer](../app-service/faq-availability-performance-application-issues.md#why-does-my-request-time-out-after-230-seconds) a vrátí chybu HTTP 502. Funkce bude pokračovat v běhu, ale nebude moci vrátit odpověď HTTP. U dlouhotrvajících funkcí doporučujeme, abyste provedli asynchronní vzorce a vraceli umístění, kde můžete testovat stav žádosti pomocí příkazového testu. Informace o tom, jak dlouho může funkce běžet, najdete v tématu [škálování a plán využití hostování](functions-scale.md#timeout).


## <a name="next-steps"></a>Další kroky

- [Vrácení odpovědi HTTP z funkce](./functions-bindings-http-webhook-output.md)
