---
title: Singleton pro Durable Functions – Azure
description: Způsob použití typu Singleton v rozšíření Durable Functions pro Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: ba35999d5a7193ba691b14005dc8271120ac2be7
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933232"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orchestrace singleton v Durable Functions (Azure Functions)

Pro úlohy na pozadí často potřebujete zajistit, aby v jednom okamžiku běžela jenom jedna instance konkrétního nástroje Orchestrator. To lze provést v [Durable Functions](durable-functions-overview.md) přiřazením konkrétního ID instance ke Orchestrator při jeho vytváření.

## <a name="singleton-example"></a>Příklad typu Singleton

Následující C# a javascriptové příklady ukazují funkci triggeru http, která vytvoří orchestraci úlohy na pozadí s jedním prvkem. Kód zajišťuje, aby pro zadané ID instance existovala pouze jedna instance.

### <a name="c"></a>C#

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
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

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

Tady je soubor function.json:
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

Tady je kód jazyka JavaScript:
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

Ve výchozím nastavení jsou identifikátory instancí náhodně generované identifikátory GUID. V tomto případě se ale ID instance předává v datech směrování z adresy URL. Kód volá [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) (C#) nebo `getStatus` (JavaScript) pro kontrolu, zda instance se zadaným ID již běží. V takovém případě je vytvořena instance s tímto ID.

> [!NOTE]
> V této ukázce je potenciální podmínka časování. Pokud se dvě instance **HttpStartSingle** spustí souběžně, obě volání funkce budou hlásit úspěch, ale pouze jedna instance orchestrace bude skutečně spuštěna. V závislosti na vašich požadavcích může to mít nežádoucí vedlejší účinky. Z tohoto důvodu je důležité zajistit, aby žádné dvě požadavky nemohly současně spouštět tuto triggerovou funkci.

Podrobnosti implementace funkce Orchestrator nezáleží na tom, jaké jsou ve skutečnosti. Může se jednat o běžnou funkci Orchestrator, která se spustí a dokončí, nebo to může být jedna z nich, která je trvale spuštěná (to znamená [orchestrace externí](durable-functions-eternal-orchestrations.md)). Důležitým bodem je, že v jednu chvíli běží jenom jedna instance.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Seznamte se s nativními funkcemi protokolu HTTP pro orchestraci](durable-functions-http-features.md)
