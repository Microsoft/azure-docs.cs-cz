---
title: Singleton pro Durable Functions – Azure
description: Způsob použití typu Singleton v rozšíření Durable Functions pro Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 4eff7c4c91ed664fcf1f4fc7a8be2d43d24e5c6b
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262805"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orchestrace singleton v Durable Functions (Azure Functions)

Pro úlohy na pozadí často potřebujete zajistit, aby v jednom okamžiku běžela jenom jedna instance konkrétního nástroje Orchestrator. Tento druh chování typu Singleton v [Durable Functions](durable-functions-overview.md) můžete zajistit přiřazením konkrétního ID instance ke Orchestrator při jeho vytváření.

## <a name="singleton-example"></a>Příklad typu Singleton

Následující příklad ukazuje funkci triggeru HTTP, která vytvoří orchestraci úlohy na pozadí s jedním prvkem. Kód zajišťuje, aby pro zadané ID instance existovala pouze jedna instance.

# <a name="ctabcsharp"></a>[C#](#tab/csharp)

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
> Předchozí C# kód je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít atribut `OrchestrationClient` namísto atributu `DurableClient` a musíte použít typ parametru `DurableOrchestrationClient` namísto `IDurableOrchestrationClient`. Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

Ve výchozím nastavení jsou identifikátory instancí náhodně generované identifikátory GUID. V předchozím příkladu se ale ID instance předává v datech směrování z adresy URL. Kód volá `GetStatusAsync`(C#) nebo `getStatus` (JavaScript), aby zkontroloval, jestli instance se zadaným ID už je spuštěná. Pokud taková instance není spuštěná, vytvoří se nová instance s tímto ID.

> [!NOTE]
> V této ukázce je potenciální podmínka časování. Pokud se dvě instance **HttpStartSingle** spustí souběžně, obě volání funkce budou hlásit úspěch, ale pouze jedna instance orchestrace bude skutečně spuštěna. V závislosti na vašich požadavcích může to mít nežádoucí vedlejší účinky. Z tohoto důvodu je důležité zajistit, aby žádné dvě požadavky nemohly současně spouštět tuto triggerovou funkci.

Podrobnosti implementace funkce Orchestrator nezáleží na tom. Může se jednat o běžnou funkci Orchestrator, která se spustí a dokončí, nebo to může být jedna z nich, která je trvale spuštěná (to znamená [orchestrace externí](durable-functions-eternal-orchestrations.md)). Důležitým bodem je, že v jednu chvíli běží jenom jedna instance.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Seznamte se s nativními funkcemi protokolu HTTP pro orchestraci](durable-functions-http-features.md)
