---
title: Azure funkce protokolu HTTP a webhooku vazby
description: Pochopit, jak používat protokol HTTP a webhooku triggerů a vazeb v Azure Functions.
services: functions
documentationcenter: na
author: tdykstra
manager: cfowler
editor: ''
tags: ''
keywords: Funkce Azure, funkce, událostí zpracování, webhooků, dynamické výpočetní, bez serveru Architektura protokolu HTTP, API REST
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: tdykstra
ms.openlocfilehash: c85af21a40a1b110ae2088fdf6fc8077ec919039
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34724621"
---
# <a name="azure-functions-http-and-webhook-bindings"></a>Azure funkce protokolu HTTP a webhooku vazby

Tento článek vysvětluje, jak pracovat s vazby HTTP v Azure Functions. Azure funkce podporuje HTTP triggerů a vazeb výstup.

Aktivační událost INSTEAD HTTP lze přizpůsobit reagovat na [webhooky](https://en.wikipedia.org/wiki/Webhook). Aktivační události webhooku přijímá pouze datové části JSON a ověří JSON. Existují speciální verze aktivační události webhooku, které usnadňují zpracování webhooky od určitých poskytovatelů, jako je například Githubu a Slack.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Vazby HTTP jsou součástí [Microsoft.Azure.WebJobs.Extensions.Http](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) balíček NuGet verze 1.x. Zdrojový kód pro balíček je v [azure webjobs sdk rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http) úložiště GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Vazby HTTP jsou součástí [Microsoft.Azure.WebJobs.Extensions.Http](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) balíček NuGet verze 3.x. Zdrojový kód pro balíček je v [azure webjobs sdk rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) úložiště GitHub.

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Trigger

Aktivace protokolu HTTP umožňuje vyvolají funkci s žádostí HTTP. Sestavení bez serveru rozhraní API a reagovat na webhooky můžete použít triggeru protokolu HTTP. 

Ve výchozím nastavení, vrátí aktivační procedury HTTP HTTP 200 OK s prázdným textem zprávy ve funkcích 1.x nebo HTTP 204 ne obsahu s prázdným textem zprávy ve funkcích 2.x. Chcete-li upravit odpověď, nakonfigurovat [HTTP výstup vazby](#http-output-binding).

## <a name="trigger---example"></a>Aktivační událost – příklad

Podívejte se na konkrétní jazyk příklad:

* [C#](#trigger---c-example)
* [C# skript (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Aktivační událost – příklad jazyka C#

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) vyhledává `name` parametr buď v řetězci dotazu nebo textu požadavku HTTP. Všimněte si, že návratová hodnota se používá pro výstup vazbu, ale atribut návratová hodnota není povinné.

```cs
[FunctionName("HttpTriggerCSharp")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)]HttpRequestMessage req, 
    TraceWriter log)
{
    log.Info("C# HTTP trigger function processed a request.");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

### <a name="trigger---c-script-example"></a>Aktivační událost – příklad skriptu jazyka C#

Následující příklad ukazuje, aktivační události vazby v *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) používající vazby. Funkce hledá `name` parametr buď v řetězci dotazu nebo textu požadavku HTTP.

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

[Konfigurace](#trigger---configuration) část vysvětluje tyto vlastnosti.

Zde je kód C# skript s vazbou na `HttpRequestMessage`:

```csharp
using System.Net;
using System.Threading.Tasks;

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, TraceWriter log)
{
    log.Info($"C# HTTP trigger function processed a request. RequestUri={req.RequestUri}");

    // parse query parameter
    string name = req.GetQueryNameValuePairs()
        .FirstOrDefault(q => string.Compare(q.Key, "name", true) == 0)
        .Value;

    // Get request body
    dynamic data = await req.Content.ReadAsAsync<object>();

    // Set name to query string or body data
    name = name ?? data?.name;

    return name == null
        ? req.CreateResponse(HttpStatusCode.BadRequest, "Please pass a name on the query string or in the request body")
        : req.CreateResponse(HttpStatusCode.OK, "Hello " + name);
}
```

Můžete vázat na vytvoření vlastního objektu `HttpRequestMessage`. Tento objekt se vytvoří z textu požadavku, analyzovat jako JSON. Podobně typu můžete předat výstup vazby a vrátí jako text odpovědi, společně s 200 stavový kód odpovědi HTTP.

```csharp
using System.Net;
using System.Threading.Tasks;

public static string Run(CustomObject req, TraceWriter log)
{
    return "Hello " + req?.name;
}

public class CustomObject {
     public String name {get; set;}
}
```

### <a name="trigger---f-example"></a>Aktivační událost – příklad F #

Následující příklad ukazuje, aktivační události vazby v *function.json* souboru a [F # funkce](functions-reference-fsharp.md) používající vazby. Funkce hledá `name` parametr buď v řetězci dotazu nebo textu požadavku HTTP.

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

[Konfigurace](#trigger---configuration) část vysvětluje tyto vlastnosti.

Tady je kód F #:

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

Je nutné `project.json` souboru, který používá NuGet tak, aby odkazovaly `FSharp.Interop.Dynamic` a `Dynamitey` sestavení, jak je znázorněno v následujícím příkladu:

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

### <a name="trigger---javascript-example"></a>Aktivační událost – příklad v jazyce JavaScript

Následující příklad ukazuje, aktivační události vazby v *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby. Funkce hledá `name` parametr buď v řetězci dotazu nebo textu požadavku HTTP.

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

[Konfigurace](#trigger---configuration) část vysvětluje tyto vlastnosti.

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
     
## <a name="trigger---webhook-example"></a>Aktivační událost – příklad webhooku

Podívejte se na konkrétní jazyk příklad:

* [C#](#webhook---c-example)
* [C# skript (.csx)](#webhook---c-script-example)
* [F#](#webhook---f-example)
* [JavaScript](#webhook---javascript-example)

### <a name="webhook---c-example"></a>Webhooku – příklad jazyka C#

Následující příklad ukazuje [C# funkce](functions-dotnet-class-library.md) HTTP 200, odešle v odpovědi na žádost o obecný JSON.

```cs
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="webhook---c-script-example"></a>Webhooku – příklad skriptu jazyka C#

Následující příklad ukazuje, aktivační události webhooku vazby ve *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) používající vazby. Funkce zaznamená komentáře problém Githubu.

Tady je *function.json* souboru:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "github",
      "name": "req"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ],
  "disabled": false
}
```

[Konfigurace](#trigger---configuration) část vysvětluje tyto vlastnosti.

Tady je kód skriptu jazyka C#:

```csharp
#r "Newtonsoft.Json"

using System;
using System.Net;
using System.Threading.Tasks;
using Newtonsoft.Json;

public static async Task<object> Run(HttpRequestMessage req, TraceWriter log)
{
    string jsonContent = await req.Content.ReadAsStringAsync();
    dynamic data = JsonConvert.DeserializeObject(jsonContent);

    log.Info($"WebHook was triggered! Comment: {data.comment.body}");

    return req.CreateResponse(HttpStatusCode.OK, new {
        body = $"New GitHub comment: {data.comment.body}"
    });
}
```

### <a name="webhook---f-example"></a>Webhooku – příklad F #

Následující příklad ukazuje, aktivační události webhooku vazby ve *function.json* souboru a [F # funkce](functions-reference-fsharp.md) používající vazby. Funkce zaznamená komentáře problém Githubu.

Tady je *function.json* souboru:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "github",
      "name": "req"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ],
  "disabled": false
}
```

[Konfigurace](#trigger---configuration) část vysvětluje tyto vlastnosti.

Tady je kód F #:

```fsharp
open System.Net
open System.Net.Http
open FSharp.Interop.Dynamic
open Newtonsoft.Json

type Response = {
    body: string
}

let Run(req: HttpRequestMessage, log: TraceWriter) =
    async {
        let! content = req.Content.ReadAsStringAsync() |> Async.AwaitTask
        let data = content |> JsonConvert.DeserializeObject
        log.Info(sprintf "GitHub WebHook triggered! %s" data?comment?body)
        return req.CreateResponse(
            HttpStatusCode.OK,
            { body = sprintf "New GitHub comment: %s" data?comment?body })
    } |> Async.StartAsTask
```

### <a name="webhook---javascript-example"></a>Webhooku – příklad v jazyce JavaScript

Následující příklad ukazuje, aktivační události webhooku vazby ve *function.json* souboru a [funkce JavaScript, která](functions-reference-node.md) používající vazby. Funkce zaznamená komentáře problém Githubu.

Zde je vazba dat v *function.json* souboru:

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "webHookType": "github",
      "name": "req"
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ],
  "disabled": false
}
```

[Konfigurace](#trigger---configuration) část vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="trigger---attributes"></a>Aktivační událost – atributy

V [knihovny tříd jazyka C#](functions-dotnet-class-library.md), použijte [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) atribut.

Můžete nastavit autorizaci úrovně a povolených metod HTTP v atributu konstruktoru parametry a nejsou k dispozici vlastnosti webhooku šablony typu a směrování. Další informace o těchto nastaveních najdete v tématu [aktivační událost - konfigurace](#trigger---configuration). Tady je `HttpTrigger` atribut v podpis metody:

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    ...
}
 ```

Úplný příklad najdete v tématu [aktivační událost - C# příklad](#trigger---c-example).

## <a name="trigger---configuration"></a>Aktivační událost - konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `HttpTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
| **type** | neuvedeno| Vyžaduje - musí být nastavena na `httpTrigger`. |
| **direction** | neuvedeno| Vyžaduje - musí být nastavena na `in`. |
| **Jméno** | neuvedeno| Požadovaná proměnná používá v kódu funkce pro požadavek nebo textu požadavku. |
| <a name="http-auth"></a>**AuthLevel** |  **AuthLevel** |Určuje, co klíče, pokud existuje, musí být přítomen v požadavku k vyvolání funkce. Úroveň oprávnění může být jedna z následujících hodnot: <ul><li><code>anonymous</code>&mdash;Je vyžadován žádný klíč rozhraní API.</li><li><code>function</code>&mdash;Je požadován klíč rozhraní API specifických funkcí. Toto je výchozí hodnota, pokud žádný je k dispozici.</li><li><code>admin</code>&mdash;Je nezbytný hlavní klíč.</li></ul> Další informace najdete v části [autorizace klíče](#authorization-keys). |
| **Metody** |**Metody** | Pole metody HTTP, na které funkce odpoví. Pokud není zadaný, funkce odpoví na všechny metody HTTP. V tématu [přizpůsobit koncový bod http](#customize-the-http-endpoint). |
| **Trasy** | **Trasy** | Definuje šablonu trasy řízení, které žádosti o funkce odpoví adresy URL. Výchozí hodnota, pokud je zadaný žádný je `<functionname>`. Další informace najdete v tématu [přizpůsobit koncový bod http](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** |Nakonfiguruje tak, aby fungoval jako triggeru protokolu HTTP [webhooku](https://en.wikipedia.org/wiki/Webhook) příjemce pro zadaného zprostředkovatele. Není nastavený `methods` vlastnost při nastavení této vlastnosti. Typ webhooku může být jedna z následujících hodnot:<ul><li><code>genericJson</code>&mdash;Koncový bod pro obecné účely webhooku bez logiku pro konkrétního zprostředkovatele. Toto nastavení omezuje jenom na ty pomocí protokolu HTTP POST a s požadavky `application/json` typ obsahu.</li><li><code>github</code>&mdash;Funkce odpoví na [Githubu webhooky](https://developer.github.com/webhooks/). Nepoužívejte _authLevel_ vlastnost s webhooky Githubu. Další informace najdete v části GitHub webhooky později v tomto článku.</li><li><code>slack</code>&mdash;Funkce odpoví na [Slack webhooky](https://api.slack.com/outgoing-webhooks). Nepoužívejte _authLevel_ vlastnost s Slack webhooky. Další informace najdete v části Slack webhooky později v tomto článku.</li></ul>|

## <a name="trigger---usage"></a>Aktivační událost - využití

Pro funkce C# a F #, můžou deklarovat typ aktivační událost vstupu být buď `HttpRequestMessage` nebo vlastního typu. Pokud se rozhodnete `HttpRequestMessage`, získat úplný přístup k objektu žádosti. Pro vlastní typ funkce pokusí analyzovat datovou část požadavku JSON a nastavte vlastnosti objektu. 

Pro funkce jazyka JavaScript Functions runtime poskytuje textu žádosti namísto objektu žádosti. Další informace najdete v tématu [příklad v jazyce JavaScript aktivační událost](#trigger---javascript-example).

### <a name="github-webhooks"></a>Webhooky Githubu

Chcete-li reagovat na Githubu webhooků, nejprve vytvoření funkce s aktivační procedury protokolu HTTP a nastavte **webHookType** vlastnost `github`. Zkopírujte jeho adresu URL a rozhraní API klíč do **přidat webhooku** stránky ve vašem úložišti GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

Příklad najdete v tématu [Vytvoření funkce aktivované webhookem GitHubu](functions-create-github-webhook-triggered-function.md).

### <a name="slack-webhooks"></a>Slack webhooky

Slack webhooku generuje token pro vás místo umožňují určit, takže je nutné nakonfigurovat specifické funkce klíč pomocí tokenu z Slack. V tématu [autorizace klíče](#authorization-keys).

### <a name="customize-the-http-endpoint"></a>Přizpůsobení koncový bod HTTP

Ve výchozím nastavení vytvořit funkci pro triggeru protokolu HTTP, nebo Webhooku, funkce při adresovatelné s trasou ve tvaru:

    http://<yourapp>.azurewebsites.net/api/<funcname> 

Můžete přizpůsobit tuto trasu pomocí volitelné `route` vstup vazbu vlastnosti na triggeru protokolu HTTP. Jako příklad následující *function.json* soubor definuje `route` vlastnost pro aktivační procedury HTTP:

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

Pomocí této konfigurace, funkce je nyní adresovatelné s následující trasa místo původní trasy.

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

To umožňuje kódu funkce, které podporují dva parametry v adrese, _kategorie_ a _id_. Můžete použít libovolnou [omezení trasy webové rozhraní API](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) s parametry. Následující kód funkce jazyka C# využívá oba parametry.

```csharp
public static Task<HttpResponseMessage> Run(HttpRequestMessage req, string category, int? id, 
                                                TraceWriter log)
{
    if (id == null)
        return  req.CreateResponse(HttpStatusCode.OK, $"All {category} items were requested.");
    else
        return  req.CreateResponse(HttpStatusCode.OK, $"{category} item with id = {id} has been requested.");
}
```

Zde je kód Node.js funkce, který používá stejné parametry trasy.

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

Standardně jsou všechny funkce trasy předponou *rozhraní api*. Můžete také upravit nebo odebrat pomocí předpony `http.routePrefix` vlastnost ve vaší [host.json](functions-host-json.md) souboru. Následující příklad odebere *rozhraní api* předpona trasy pomocí prázdný řetězec pro předponu v *host.json* souboru.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="authorization-keys"></a>Autorizace klíče

Aktivace protokolu HTTP vám umožňují používat klíče pro zvýšení zabezpečení. Standardní triggeru protokolu HTTP můžete použít jako klíč rozhraní API nutnosti klíč nacházet v žádosti. Webhooky slouží k autorizaci požadavků v mnoha různými způsoby v závislosti na tom, co poskytovatel podporuje klíče.

> [!NOTE]
> Při místním spuštění funkce autorizace je zakázána bez ohledu `authLevel` nastavit `function.json`. Jakmile publikujete na Azure Functions `authLevel` okamžitě projeví.

Klíče jsou uložené v rámci funkce aplikace v Azure a jsou zašifrovaná přinejmenším. Chcete-li zobrazit vaše klíče, vytvořit nové, nebo vrátit klíče na nové hodnoty, přejděte na jednu z funkcí na portálu a vyberte "Manage". 

Existují dva typy klíčů:

- **Klíče hostitele**: tyto klíče jsou sdíleny všechny funkce v rámci funkce aplikace. Když se použije jako klíč rozhraní API, tyto rutiny umožňují přístup k žádné funkce v rámci funkce aplikace.
- **Funkční klávesy**: tyto klíče se vztahují pouze na určité funkce, pod kterým jsou definovány. Když se použije jako klíč rozhraní API, tyto pouze povolí přístup k této funkce.

Každý klíč jmenuje pro referenci a je výchozí klíč (s názvem "Výchozí") na úrovni funkce a hostitele. Funkční klávesy mají přednost před klíče hostitele. Když dva klíče jsou definovány se stejným názvem, je vždy použít funkční klávesy.

**Hlavní klíč** je výchozí hostitele klíč s názvem "_master", která je definována pro každou aplikaci funkce. Nejde odvolat, tento klíč. Poskytuje přístup pro modul runtime rozhraní API pro správu. Pomocí `"authLevel": "admin"` vazba JSON vyžaduje tento klíč předkládané na vyžádání; další klíč výsledkem ověření se nezdařilo.

> [!IMPORTANT]  
> Z důvodu vyšší úroveň oprávnění udělená pomocí hlavního klíče nesmí sdílet s třetími stranami tento klíč nebo distribuovat v nativní klientské aplikace. Buďte opatrní při výběru úroveň oprávnění správce.

### <a name="api-key-authorization"></a>Autorizace pro klíč rozhraní API

Ve výchozím nastavení aktivační procedury HTTP vyžaduje klíč rozhraní API v požadavku HTTP. Proto požadavku HTTP obvykle vypadá takto:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

Klíč může být součástí proměnnou řetězce dotazu s názvem `code`, jak je uvedeno výše, nebo ho můžou být součástí `x-functions-key` hlavičky protokolu HTTP. Hodnota klíče může být jakékoli funkce definované pro tuto funkci, nebo všechny hostitele klíče.

Můžete povolit anonymní žádosti, které nevyžadují klíče. Můžete také vyžaduje, aby se používané hlavního klíče. Změnit výchozí úroveň ověřování pomocí `authLevel` vlastnost Vazba JSON. Další informace najdete v tématu [aktivační událost - konfigurace](#trigger---configuration).

### <a name="keys-and-webhooks"></a>Klíče a pomocí webhooků

Autorizace Webhooku se zpracovává souborem komponentu příjemce webhooku součástí triggeru protokolu HTTP, a tento mechanismus se může lišit podle typu webhooku. Jednotlivé mechanismy neodpovídá, ale závisí na klíč. Ve výchozím nastavení se používá klíč funkce s názvem "Výchozí". Pokud chcete používat jiný klíč, konfiguraci poskytovatele webhooku odeslat název klíče s požadavkem v jednom z následujících způsobů:

- **Řetězec dotazu**: Zprostředkovatel předá název klíče v `clientid` parametr řetězce dotazu, jako `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`.
- **Hlavička požadavku**: Zprostředkovatel předá název klíče v `x-functions-clientid` záhlaví.

## <a name="trigger---limits"></a>Aktivační událost – omezení

Délka požadavku HTTP je omezený na 100MB (104,857,600 bajtů) a délky adres URL je omezena na 4KB (4 096 bajtů). Tato omezení jsou určené `httpRuntime` element modulu runtime [souboru Web.config](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config).

Pokud funkci, která používá triggeru protokolu HTTP není dokončena v rámci přibližně 2,5 minut, bude časový limit brány a vrátí chybu HTTP 502. Funkce bude nadále používat, ale nebude možné vrátit odpovědi HTTP. Pro dlouhodobé funkce doporučujeme dodržovat asynchronní vzory a vrátí se umístění, kde může odeslat příkaz ping stav žádosti. Informace o jak dlouho může spustit funkci najdete v tématu [škálování a hostování - spotřeba plánování](functions-scale.md#consumption-plan). 

## <a name="trigger---hostjson-properties"></a>Aktivační událost - host.json vlastnosti

[Host.json](functions-host-json.md) soubor obsahuje nastavení, které řídí chování aktivace protokolu HTTP.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Výstup

Použijte protokol HTTP výstup vazby reagovat na odesílatel požadavku HTTP. Tato vazba vyžaduje aktivační procedury protokolu HTTP a umožňuje přizpůsobit odpověď přidružená k požadavku aktivační událost. Pokud výstup vazba HTTP není zadáno, aktivační procedury HTTP vrátí HTTP 200 OK s prázdným textem zprávy ve funkcích 1.x nebo HTTP 204 ne obsahu s prázdným textem zprávy ve funkcích 2.x.

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru. Pro C# třídu knihovny existuje jsou žádný atribut vlastnosti, které odpovídají tyto *function.json* vlastnosti. 

|Vlastnost  |Popis  |
|---------|---------|
| **type** |musí být nastavena na `http`. |
| **direction** | musí být nastavena na `out`. |
|**Jméno** | Název proměnné používá v kódu funkce pro odpověď, nebo `$return` používat návratovou hodnotu. |

## <a name="output---usage"></a>Výstup – použití

K odeslání odpovědi HTTP, použijte vzory language standard odpovědi. V jazyce C# nebo skriptu jazyka C#, ujistěte se, návratový typ funkce `HttpResponseMessage` nebo `Task<HttpResponseMessage>`. V jazyce C# návratová hodnota atributu není povinné.

Například odpovědí, najdete v článku [příklad aktivační událost](#trigger---example) a [webhooku příklad](#trigger---webhook-example).

## <a name="next-steps"></a>Další postup

[Další informace o Azure functions triggerů a vazeb](functions-triggers-bindings.md)
