---
title: Singleton pro Durable Functions – Azure
description: Způsob použití typu Singleton v rozšíření Durable Functions pro Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: d9bf9687f60e649fee98869ef263117177ad5efd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097937"
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

> [!WARNING]
> Při vývoji místně v JavaScriptu budete muset nastavit proměnnou `WEBSITE_HOSTNAME` prostředí na `localhost:<port>`, ex. `localhost:7071`pro použití metod v `DurableOrchestrationClient`. Další informace o tomto požadavku najdete v tématu [problém GitHubu](https://github.com/Azure/azure-functions-durable-js/issues/28).

> [!NOTE]
> V této ukázce je potenciální podmínka časování. Pokud se dvě instance **HttpStartSingle** spustí souběžně, obě volání funkce budou hlásit úspěch, ale pouze jedna instance orchestrace bude skutečně spuštěna. V závislosti na vašich požadavcích může to mít nežádoucí vedlejší účinky. Z tohoto důvodu je důležité zajistit, aby žádné dvě požadavky nemohly současně spouštět tuto triggerovou funkci.

Podrobnosti implementace funkce Orchestrator nezáleží na tom, jaké jsou ve skutečnosti. Může se jednat o běžnou funkci Orchestrator, která se spustí a dokončí, nebo to může být jedna z nich, která je trvale spuštěná (to znamená [orchestrace externí](durable-functions-eternal-orchestrations.md)). Důležitým bodem je, že v jednu chvíli běží jenom jedna instance.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Naučte se volat dílčí orchestrace.](durable-functions-sub-orchestrations.md)
