---
title: Azure Functions HTTP a webhookové vazby
description: Naučte se používat HTTP a webhookové triggerů a vazeb ve službě Azure Functions.
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: ''
tags: ''
keywords: Azure functions, functions, událostí zpracování, webhook, dynamické, architekturu bez serveru, protokolu HTTP, rozhraní API, rozhraní REST pro compute
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/21/2017
ms.author: glenga
ms.openlocfilehash: 5f6538c69139b8cd254b44cb9875e18a14c8fa8b
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2018
ms.locfileid: "39344143"
---
# <a name="azure-functions-http-and-webhook-bindings"></a>Azure Functions HTTP a webhookové vazby

Tento článek vysvětluje, jak pracovat s vazby HTTP ve službě Azure Functions. Azure Functions podporuje HTTP triggery a vazby výstupu.

Aktivační událost HTTP je možné přizpůsobit reagovat na [webhooky](https://en.wikipedia.org/wiki/Webhook). Aktivační událost webhooku přijímá pouze datovou část JSON a ověří ve formátu JSON. Existují speciální verze triggeru webhooku, které usnadňují zpracování webhooků z určitých poskytovatelů, jako jsou GitHub a Slack.

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="packages---functions-1x"></a>Balíčky – funkce 1.x

Vazby protokolu HTTP jsou součástí [Microsoft.Azure.WebJobs.Extensions.Http](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) balíčku NuGet, verzi 1.x. Zdrojový kód pro tento balíček je v [azure webjobs sdk rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/tree/v2.x/src/WebJobs.Extensions.Http) úložiště GitHub.

[!INCLUDE [functions-package-auto](../../includes/functions-package-auto.md)]

## <a name="packages---functions-2x"></a>Balíčky – funkce 2.x

Jsou součástí vazby HTTP [Microsoft.Azure.WebJobs.Extensions.Http](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Http) balíčku NuGet, verze 3.x. Zdrojový kód pro tento balíček je v [azure webjobs sdk rozšíření](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Http/) úložiště GitHub.

[!INCLUDE [functions-package](../../includes/functions-package-auto.md)]

## <a name="trigger"></a>Trigger

HTTP trigger umožňuje vyvolání funkce s žádostí HTTP. Aktivační událost HTTP slouží k vytvoření rozhraní API bez serveru a reagovat na webhooky. 

Ve výchozím nastavení, vrátí aktivační událost HTTP HTTP 200 OK s prázdným textem zprávy ve funkcích 1.x nebo HTTP 204 žádný obsah s prázdným textem zprávy ve funkcích 2.x. Chcete-li upravit odpověď, nakonfigurovat [HTTP výstupní vazby](#http-output-binding).

## <a name="trigger---example"></a>Aktivační události – příklad

Podívejte se na příklad specifické pro jazyk:

* [C#](#trigger---c-example)
* [C# skript (.csx)](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="trigger---c-example"></a>Aktivační události – příklad v jazyce C#

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) vyhledává `name` parametr v řetězci dotazu nebo textu požadavku HTTP. Všimněte si, že návratová hodnota se používá pro výstupní vazby, ale vrácená hodnota atributu není povinné.

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

### <a name="trigger---c-script-example"></a>Aktivační události – příklad skriptu jazyka C#

Následující příklad ukazuje vazbu aktivační události v *function.json* souboru a [funkce skriptu jazyka C#](functions-reference-csharp.md) , který používá vazba. Funkce Hledat `name` parametr v řetězci dotazu nebo textu požadavku HTTP.

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

Tady je kód jazyka C# skript, který se váže k `HttpRequestMessage`:

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

Můžete vytvořit vazbu k vytvoření vlastního objektu `HttpRequestMessage`. Tento objekt je vytvořen z textu požadavku, analyzovat jako JSON. Podobně lze předat typ výstupní vazby a vrátí jako text odpovědi, spolu s 200 stavový kód odpovědi HTTP.

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

### <a name="trigger---f-example"></a>Aktivační události – příklad F #

Následující příklad ukazuje vazbu aktivační události v *function.json* souboru a [funkce jazyka F #](functions-reference-fsharp.md) , který používá vazba. Funkce Hledat `name` parametr v řetězci dotazu nebo textu požadavku HTTP.

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

Je nutné `project.json` soubor, který používá NuGet tak, aby odkazovaly `FSharp.Interop.Dynamic` a `Dynamitey` sestavení, jak je znázorněno v následujícím příkladu:

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

Následující příklad ukazuje vazbu aktivační události v *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) , který používá vazba. Funkce Hledat `name` parametr v řetězci dotazu nebo textu požadavku HTTP.

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
     
## <a name="trigger---webhook-example"></a>Aktivační události – příklad webhooku

Podívejte se na příklad specifické pro jazyk:

* [C#](#webhook---c-example)
* [C# skript (.csx)](#webhook---c-script-example)
* [F#](#webhook---f-example)
* [JavaScript](#webhook---javascript-example)

### <a name="webhook---c-example"></a>Webhook – příklad v jazyce C#

Následující příklad ukazuje [funkce jazyka C#](functions-dotnet-class-library.md) HTTP 200, která odešle v reakci na žádost o obecný JSON.

```cs
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run([HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    return req.CreateResponse(HttpStatusCode.OK);
}
```

### <a name="webhook---c-script-example"></a>Webhook – příklad skriptu jazyka C#

Následující příklad ukazuje vazby v trigger webhooku *function.json* soubor a [funkce skriptu jazyka C#](functions-reference-csharp.md) , který používá vazba. Funkce protokoly komentářů problém Githubu.

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

[Konfigurace](#trigger---configuration) bodu vysvětluje tyto vlastnosti.

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

### <a name="webhook---f-example"></a>Webhook – F # příklad

Následující příklad ukazuje trigger webhooku vazby ve *function.json* souboru a [funkce jazyka F #](functions-reference-fsharp.md) , který používá vazba. Funkce protokoly komentářů problém Githubu.

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

[Konfigurace](#trigger---configuration) bodu vysvětluje tyto vlastnosti.

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

### <a name="webhook---javascript-example"></a>Webhook – příklad v jazyce JavaScript

Následující příklad ukazuje trigger webhooku vazby ve *function.json* souboru a [funkce jazyka JavaScript](functions-reference-node.md) , který používá vazba. Funkce protokoly komentářů problém Githubu.

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

[Konfigurace](#trigger---configuration) bodu vysvětluje tyto vlastnosti.

Tady je kód jazyka JavaScript:

```javascript
module.exports = function (context, data) {
    context.log('GitHub WebHook triggered!', data.comment.body);
    context.res = { body: 'New GitHub comment: ' + data.comment.body };
    context.done();
};
```

## <a name="trigger---attributes"></a>Aktivační události – atributy

V [knihoven tříd C#](functions-dotnet-class-library.md), použijte [HttpTrigger](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/dev/src/WebJobs.Extensions.Http/HttpTriggerAttribute.cs) atribut.

Můžete nastavit autorizaci úrovně a povolené metody HTTP v parametry konstruktoru atributu a pro šablony typu a tras webhooku vlastností. Další informace o těchto nastaveních najdete v tématu [aktivační události – konfigurace](#trigger---configuration). Tady je `HttpTrigger` atribut v podpisu metody:

```csharp
[FunctionName("HttpTriggerCSharp")]
public static HttpResponseMessage Run(
    [HttpTrigger(AuthorizationLevel.Anonymous, WebHookType = "genericJson")] HttpRequestMessage req)
{
    ...
}
 ```

Kompletní příklad naleznete v tématu [Trigger – C# příklad](#trigger---c-example).

## <a name="trigger---configuration"></a>Aktivační události – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru a `HttpTrigger` atribut.

|Vlastnost Function.JSON | Vlastnost atributu |Popis|
|---------|---------|----------------------|
| **type** | neuvedeno| Povinné – musí být nastavena na `httpTrigger`. |
| **direction** | neuvedeno| Povinné – musí být nastavena na `in`. |
| **Jméno** | neuvedeno| Požadovaná: název této proměnné v kódu funkce žádosti nebo text žádosti. |
| <a name="http-auth"></a>**authLevel** |  **AuthLevel** |Určuje, co klíče, pokud existuje, musí být k dispozici na vyžádání, aby bylo možné vyvolat funkci. Úroveň autorizace může být jedna z následujících hodnot: <ul><li><code>anonymous</code>&mdash;Žádný klíč rozhraní API je povinný.</li><li><code>function</code>&mdash;Klíč rozhraní API specifických funkcí je povinný. Toto je výchozí hodnota, pokud se žádný nezadá.</li><li><code>admin</code>&mdash;Je nezbytný hlavní klíč.</li></ul> Další informace najdete v části [autorizace klíče](#authorization-keys). |
| **Metody** |**Metody** | Pole metody HTTP, na které odpoví funkce. Pokud není zadán, odpovídá funkci pro všechny metody HTTP. Zobrazit [přizpůsobit koncový bod http](#customize-the-http-endpoint). |
| **trasy** | **trasy** | Definuje šablonu trasy, řízení, ke kterému adresy URL vaší funkce jako odpověď vrátí požadavků. Výchozí hodnota, pokud se žádný nezadá je `<functionname>`. Další informace najdete v tématu [přizpůsobit koncový bod http](#customize-the-http-endpoint). |
| **webHookType** | **WebHookType** |Nakonfiguruje tak, aby fungoval jako aktivační událost HTTP [webhooku](https://en.wikipedia.org/wiki/Webhook) příjemce pro zadaného zprostředkovatele. Nemají nastavený `methods` vlastnost Pokud byste tuto vlastnost nastavit. Typ webhooku může být jeden z následujících hodnot:<ul><li><code>genericJson</code>&mdash;Koncový bod webhooku pro obecné účely bez logiku pro konkrétního zprostředkovatele. Toto nastavení omezuje jenom na ty pomocí protokolu HTTP POST a s požadavky `application/json` typ obsahu.</li><li><code>github</code>&mdash;Funkce jsou reaguje na [webhooky Githubu](https://developer.github.com/webhooks/). Nepoužívejte _authLevel_ vlastnost s webhooky Githubu. Další informace najdete v části webhooky Githubu dále v tomto článku.</li><li><code>slack</code>&mdash;Funkce jsou reaguje na [Slack webhooky](https://api.slack.com/outgoing-webhooks). Nepoužívejte _authLevel_ vlastnost s webhooky Slack. Další informace najdete v části Slack webhooky dále v tomto článku.</li></ul>|

## <a name="trigger---usage"></a>Aktivační události – využití

Pro funkce C# a F #, je možné deklarovat typ triggeru zadejte buď `HttpRequestMessage` nebo vlastního typu. Pokud se rozhodnete `HttpRequestMessage`, získáte plný přístup k objektu žádosti. Pro vlastní typ funkce se pokusí analyzovat datovou část JSON žádosti můžete nastavit vlastnosti objektu. 

Pro funkce jazyka JavaScript poskytuje modul runtime služby Functions tělo požadavku místo objekt žádosti. Další informace najdete v tématu [příklad v jazyce JavaScript aktivační událost](#trigger---javascript-example).

### <a name="github-webhooks"></a>Webhooky Githubu

Reagovat na webhooky Githubu, nejprve vytvořte funkci s triggerem HTTP a nastavte **webHookType** vlastnost `github`. Zkopírujte její adresu URL a klíč rozhraní API do **přidat webhook** stránka úložiště GitHub. 

![](./media/functions-bindings-http-webhook/github-add-webhook.png)

Příklad najdete v tématu [Vytvoření funkce aktivované webhookem GitHubu](functions-create-github-webhook-triggered-function.md).

### <a name="slack-webhooks"></a>Slack webhooků

Slack webhooku generuje token pro vás místo, kde můžete určit, proto je nutné nakonfigurovat klíč specifickými funkcemi pomocí tokenu z Slack. Zobrazit [autorizace klíče](#authorization-keys).

### <a name="customize-the-http-endpoint"></a>Upravit koncový bod HTTP

Ve výchozím nastavení při vytváření funkce triggeru HTTP, nebo Webhooku, funkce je adresovatelný trasa ve tvaru:

    http://<yourapp>.azurewebsites.net/api/<funcname> 

Můžete přizpůsobit tuto trasu pomocí volitelného `route` vlastnost na HTTP trigger se uživatelovo zadání vazby. Jako příklad následující *function.json* soubor definuje `route` vlastnost pro aktivační událost HTTP:

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

Pomocí této konfigurace, je nyní adresovatelný pomocí následující postupu namísto původní trasy.

```
http://<yourapp>.azurewebsites.net/api/products/electronics/357
```

To umožňuje kódu funkce, které podporují dva parametry v adrese, _kategorie_ a _id_. Můžete použít libovolnou [omezení trasy webové rozhraní API](https://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2#constraints) s parametry. Následující kód funkce jazyka C# používá oba parametry.

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

Tady je kód funkci Node.js, který používá stejné parametry trasy.

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

Ve výchozím nastavení, jsou všechny funkce trasy s předponou *api*. Můžete také upravit nebo odebrat pomocí předpony `http.routePrefix` vlastnost ve vaší [host.json](functions-host-json.md) soubor. Následující příklad odebere *api* předponu trasy s použitím prázdný řetězec pro předponu ve *host.json* souboru.

```json
{
    "http": {
    "routePrefix": ""
    }
}
```

### <a name="authorization-keys"></a>Autorizace klíče

Aktivace protokolu HTTP umožňují používat klíče pro zvýšení zabezpečení. Standardní triggeru HTTP je použít jako klíč rozhraní API vyžaduje klíč být přítomné v žádosti. Webhook můžete použít klíče k autorizaci požadavků mnoha různými způsoby v závislosti na tom, co zprostředkovatel podporuje.

> [!NOTE]
> Při místním spuštění funkce, bez ohledu na to je zakázáno ověřování `authLevel` nastavit `function.json`. Jakmile publikujete do služby Azure Functions `authLevel` projeví se okamžitě.

Klíče jsou uložené v rámci aplikace function App v Azure a jsou v klidovém stavu zašifrovaná. Chcete-li zobrazit vaše klíče, vytvářet nové, nebo vrátit klíče na nové hodnoty, přejděte na jedno z vašich funkcí na portálu a vyberte "Manage". 

Existují dva typy klíčů:

- **Klíče hostitele**: tyto klíče jsou sdíleny ve všech funkcí v rámci aplikace function app. Když se použije jako klíč rozhraní API, tyto rutiny umožňují přístup k žádné funkce v rámci aplikace function app.
- **Funkční klávesy**: tyto klíče použít pouze na konkrétní funkce, za kterých jsou definovány. Když se použije jako klíč rozhraní API, tyto pouze umožnit přístup k této funkce.

Každý klíč je s názvem pro odkaz a na úrovni funkcí a hostitele je výchozí klíč (s názvem "Výchozí"). Funkční klávesy mají přednost před klíče hostitele. Když dva klíče jsou definovány se stejným názvem, je vždy použít funkční klávesy.

**Hlavní klíč** je výchozí klíč hostitele s názvem "_hlavní", který je definován pro každou aplikaci function app. Tento klíč se nedá odvolat. Poskytuje přístup pro správu rozhraní API modulu runtime. Pomocí `"authLevel": "admin"` ve vazbě JSON vyžaduje tento klíč, které se budou zobrazovat na vyžádání; další klíč má za následek selhání autorizace.

> [!IMPORTANT]  
> Z důvodu zvýšená oprávnění udělená pomocí hlavního klíče by neměly sdílet tento klíč s třetími stranami nebo distribuovat v nativní klientské aplikace. Buďte opatrní při výběru úroveň správce autorizace.

### <a name="api-key-authorization"></a>Autorizace pro klíč k rozhraní API

Ve výchozím nastavení aktivační událost HTTP vyžaduje klíč rozhraní API v požadavku HTTP. Takže požadavku HTTP obvykle vypadá takto:

    https://<yourapp>.azurewebsites.net/api/<function>?code=<ApiKey>

Klíče mohou být součástí proměnné řetězce dotazu s názvem `code`, jak je uvedeno výše, nebo to můžou být součástí `x-functions-key` hlavičky protokolu HTTP. Hodnota klíče může být libovolné klávesy funkce definované pro funkci nebo libovolná klávesa hostitele.

Můžete povolit anonymní požadavky, které nevyžadují žádné klíče. Můžete také vyžadovat, že se použije hlavní klíč. Změnit výchozí úroveň autorizace pomocí `authLevel` vlastnost ve vazbě JSON. Další informace najdete v tématu [aktivační události – konfigurace](#trigger---configuration).

### <a name="keys-and-webhooks"></a>Klíče a webhooky

Webhook autorizace zařizuje služba příjemce komponentu webhooku, část triggeru HTTP a mechanismu, který se liší v závislosti na typu webhooku. Každý mechanismus dělá, ale závisí na klíč. Ve výchozím nastavení se používá klíč funkce s názvem "Výchozí". Pokud chcete použít jiný kód, konfigurace poskytovatele webhooku odeslat název klíče s požadavkem v jednom z následujících způsobů:

- **Řetězec dotazu**: Zprostředkovatel předává název klíče ve `clientid` parametru řetězce dotazu, jako `https://<yourapp>.azurewebsites.net/api/<funcname>?clientid=<keyname>`.
- **Hlavička požadavku**: Zprostředkovatel předává název klíče ve `x-functions-clientid` záhlaví.

## <a name="trigger---limits"></a>Aktivační události – omezení

Délka požadavku HTTP je omezena na 100MB (104,857,600 bajtů) a délka adresy URL je omezena na 4KB (4 096 bajtů). Tato omezení jsou určeny `httpRuntime` element modulu runtime [souboru Web.config](https://github.com/Azure/azure-webjobs-sdk-script/blob/v1.x/src/WebJobs.Script.WebHost/Web.config).

Pokud funkce, která používá triggeru HTTP nemá dokončit během přibližně 2,5 minut, bude časový limit brány a vrátí chybu HTTP 502. Funkce bude pokračovat v běhu, ale nebude možné vrátit odpověď HTTP. Pro dlouho běžící funkce doporučujeme dodržovat asynchronní vzory a vrátí se umístění, kde příkaz ping stav žádosti. Informace o tom, jak dlouho může běžet funkce, najdete v části [škálování a hostování - plánu Consumption](functions-scale.md#consumption-plan). 

## <a name="trigger---hostjson-properties"></a>Aktivační události – vlastnosti host.json

[Host.json](functions-host-json.md) soubor obsahuje nastavení, která řídí chování triggeru HTTP.

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="output"></a>Výstup

Pomocí protokolu HTTP výstupní vazbu reagovat na odesílatel požadavku HTTP. Tato vazba vyžaduje triggeru HTTP a umožňuje přizpůsobit odpovědi přidružený k požadavku triggeru. Pokud výstupní vazbu protokolu HTTP není zadán aktivační událost HTTP s prázdným textem zprávy ve službě Functions vrátí HTTP 200 OK 1.x nebo HTTP 204 žádný obsah s prázdným textem zprávy ve funkcích 2.x.

## <a name="output---configuration"></a>Výstup – konfigurace

Následující tabulka popisuje vlastnosti konfigurace vazby, které jste nastavili v *function.json* souboru. Pro třídy C# jsou knihovny existuje žádný atribut vlastnosti, které odpovídají tyto *function.json* vlastnosti. 

|Vlastnost  |Popis  |
|---------|---------|
| **type** |Musí být nastaveno na `http`. |
| **direction** | Musí být nastaveno na `out`. |
|**Jméno** | Název této proměnné v kódu funkce pro odpověď, nebo `$return` návratovou hodnotu. |

## <a name="output---usage"></a>Výstup – využití

K odeslání odpovědi HTTP, použijte tyto vzory se dají odpovědi standard jazyka. V jazyce C# nebo skript jazyka C#, ujistěte se, návratový typ funkce `HttpResponseMessage` nebo `Task<HttpResponseMessage>`. V jazyce C# návratová hodnota atributu není povinné.

Například odpovědi, zobrazit [příkladu aktivační procedury](#trigger---example) a [webhooku příklad](#trigger---webhook-example).

## <a name="next-steps"></a>Další postup

[Další informace o aktivačních událostech Azure functions a vazby](functions-triggers-bindings.md)
