---
title: Singletons pro trvalé funkce - Azure
description: Jak používat singletons v rozšíření trvalé funkce pro funkce Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 4eff7c4c91ed664fcf1f4fc7a8be2d43d24e5c6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76262805"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orchestrátory singletonu v odolných funkcích (Funkce Azure)

Pro úlohy na pozadí je často nutné zajistit, aby současně byla spuštěna pouze jedna instance určitého orchestrátoru. Tento druh singleton chování v [trvalé funkce](durable-functions-overview.md) přiřazením konkrétní id instance orchestrátoru při jeho vytváření.

## <a name="singleton-example"></a>Singleton příklad

Následující příklad ukazuje funkci aktivační událost HTTP, která vytváří orchestraci úlohy na pozadí singletonu. Kód zajišťuje, že pro zadané ID instance existuje pouze jedna instance.

# <a name="c"></a>[C #](#tab/csharp)

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

> [!NOTE]
> Předchozí kód Jazyka C# je pro durable functions 2.x. Pro trvalé funkce 1.x `OrchestrationClient` je nutné `DurableClient` použít atribut namísto `DurableOrchestrationClient` atributu a `IDurableOrchestrationClient`místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

**function.json**

```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

**index.js**

```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists.
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance) {
        // An instance with the specified ID doesn't exist, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

---

Ve výchozím nastavení jsou ID instancí náhodně generovaná identifikátory GUID. V předchozím příkladu je však ID instance předáno v datech trasy z adresy URL. Kód volá `GetStatusAsync`(C#) `getStatus` nebo (JavaScript) a zkontroluje, jestli je instance se zadaným ID již spuštěna. Pokud žádná taková instance je spuštěna, je vytvořena nová instance s tímto ID.

> [!NOTE]
> V této ukázce je potenciální spor. Pokud dvě instance **HttpStartSingle** spustit souběžně, obě volání funkce bude hlásit úspěch, ale pouze jedna instance orchestrace bude skutečně spuštěna. V závislosti na vašich požadavcích, To může mít nežádoucí vedlejší účinky. Z tohoto důvodu je důležité zajistit, aby žádné dva požadavky můžete spustit tuto funkci aktivační události současně.

Podrobnosti implementace funkce orchestrator ve skutečnosti nezáleží. Mohlo by to být pravidelné orchestrator funkce, která začíná a dokončí, nebo by to mohlo být ten, který běží navždy (to znamená, [že věčný Orchestrace](durable-functions-eternal-orchestrations.md)). Důležité je, že je vždy pouze jedna instance spuštěna najednou.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Informace o nativních funkcích HTTP orchestrations](durable-functions-http-features.md)
