---
title: Jednotlivé prvky pro Durable Functions – Azure
description: Jak používat jednotlivé prvky v trvalé Functons rozšíření pro službu Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 4832a48489a043493639bdedd6c6adf3c828de11
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53434694"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Jednotlivý prvek orchestrátorů v Durable Functions (Azure Functions)

Pro úlohy na pozadí, který je často potřeba zajistit byla najednou spuštěna této jenom jeden výskyt určitého produktu orchestrator. To můžete udělat [Durable Functions](durable-functions-overview.md) přiřazením konkrétní ID instance pro orchestrátor při jejím vytváření.

## <a name="singleton-example"></a>Příklad typu singleton

Následující C# a JavaScript příklady ukazují funkci triggeru HTTP, která vytvoří Orchestrace úloh na pozadí typu singleton. Kód se zajistí, že existuje pouze jedna instance pro ID zadané instance.

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

### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

```javascript
const df = require("durable-functions");

modules.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionsName;

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

Ve výchozím nastavení instance, ID se náhodně generované identifikátory GUID. Ale v takovém případě je předané instance ID v datech trasy z adresy URL. Kód volá [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) (C#) nebo `getStatus` (JavaScript) na kontrola, zda mají zadané ID instance je už spuštěná. Pokud ne, je vytvořena instance s číslem ID této.

> [!WARNING]
> Při vývoji místně v jazyce JavaScript, budete muset nastavit proměnnou prostředí `WEBSITE_HOSTNAME` k `localhost:<port>`, např. `localhost:7071` použití metod na `DurableOrchestrationClient`. Další informace o tomto požadavku najdete v tématu [problém Githubu](https://github.com/Azure/azure-functions-durable-js/issues/28).

> [!NOTE]
> V této ukázce je potenciální konflikt časování. Pokud se dvě instance **HttpStartSingle** být prováděna současně, výsledkem by bylo možné dva různé vytvořit instance typu singleton, jednu, která přepíše druhé. V závislosti na požadavcích může to mít nežádoucí vedlejší účinky. Z tohoto důvodu je důležité zajistit, že žádné dva požadavky mohou být tato funkce pro aktivaci prováděna současně.

Podrobnosti implementace funkce orchestrátoru není ve skutečnosti důležitá. Může to být funkce regulární orchestrátoru, který při spuštění a dokončení, nebo to může být ten, který spouští navždy (to znamená, [externí Orchestrace](durable-functions-eternal-orchestrations.md)). Důležité je, že existuje pouze někdy jedna instance spuštění najednou.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak volat dílčí Orchestrace](durable-functions-sub-orchestrations.md)
