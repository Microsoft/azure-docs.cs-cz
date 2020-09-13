---
title: Singleton pro Durable Functions – Azure
description: Způsob použití typu Singleton v rozšíření Durable Functions pro Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: azfuncdf
ms.openlocfilehash: 954b322536c5430608597c2e67c474fefeb5fb25
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "90004931"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Orchestrace singleton v Durable Functions (Azure Functions)

Pro úlohy na pozadí často potřebujete zajistit, aby v jednom okamžiku běžela jenom jedna instance konkrétního nástroje Orchestrator. Tento druh chování typu Singleton v [Durable Functions](durable-functions-overview.md) můžete zajistit přiřazením konkrétního ID instance ke Orchestrator při jeho vytváření.

## <a name="singleton-example"></a>Příklad typu Singleton

Následující příklad ukazuje funkci triggeru HTTP, která vytvoří orchestraci úlohy na pozadí s jedním prvkem. Kód zajišťuje, aby pro zadané ID instance existovala pouze jedna instance.

# <a name="c"></a>[C#](#tab/csharp)

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
        return new HttpResponseMessage(HttpStatusCode.Conflict)
        {
            Content = new StringContent($"An instance with ID '{instanceId}' already exists."),
        };
    }
}
```

> [!NOTE]
> Předchozí kód jazyka C# je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `OrchestrationClient` atribut namísto `DurableClient` atributu a místo toho musíte použít `DurableOrchestrationClient` typ parametru `IDurableOrchestrationClient` . Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

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

**__init__. py**

```python
import logging
import azure.functions as func
import azure.durable_functions as df

async def main(req: func.HttpRequest, starter: str) -> func.HttpResponse:
    client = df.DurableOrchestrationClient(starter)
    instance_id = req.route_params['instanceId']
    function_name = req.route_params['functionName']

    existing_instance = await client.get_status(instance_id)

    if existing_instance != None:
        event_data = req.get_body()
        instance_id = await client.start_new(function_name, instance_id, event_data)
        logging.info(f"Started orchestration with ID = '{instance_id}'.")
        return client.create_check_status_response(req, instance_id)
    else:
        return {
            'status': 409,
            'body': f"An instance with ID '${instance_id}' already exists"
        }

```

---

Ve výchozím nastavení jsou identifikátory instancí náhodně generované identifikátory GUID. V předchozím příkladu se ale ID instance předává v datech směrování z adresy URL. Kód volá `GetStatusAsync` (C#), `getStatus` (JavaScript) nebo `get_status` (Python), aby zkontroloval, jestli instance se zadaným ID už běží. Pokud taková instance není spuštěná, vytvoří se nová instance s tímto ID.

> [!NOTE]
> V této ukázce je potenciální podmínka časování. Pokud se dvě instance **HttpStartSingle** spustí souběžně, obě volání funkce budou hlásit úspěch, ale pouze jedna instance orchestrace bude skutečně spuštěna. V závislosti na vašich požadavcích může to mít nežádoucí vedlejší účinky. Z tohoto důvodu je důležité zajistit, aby žádné dvě požadavky nemohly současně spouštět tuto triggerovou funkci.

Podrobnosti implementace funkce Orchestrator nezáleží na tom. Může se jednat o běžnou funkci Orchestrator, která se spustí a dokončí, nebo to může být jedna z nich, která je trvale spuštěná (to znamená [orchestrace externí](durable-functions-eternal-orchestrations.md)). Důležitým bodem je, že v jednu chvíli běží jenom jedna instance.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Seznamte se s nativními funkcemi protokolu HTTP pro orchestraci](durable-functions-http-features.md)
