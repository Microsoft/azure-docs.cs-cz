---
title: "Jednotlivé prvky pro odolná funkce – Azure"
description: "Jak používat jednotlivé prvky v trvanlivý Functons rozšíření pro Azure Functions."
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: ea8b5db946d6b35ea4583d9170ec36e5f95e16cd
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Singleton orchestrators trvanlivý funkcí (Azure Functions)

Pro úlohy na pozadí nebo orchestrations objektu actor stylu, je často potřeba zajistit, že pouze jedna instance najednou spuštěna konkrétní orchestrator. To lze provést [trvanlivý funkce](durable-functions-overview.md) přiřazením konkrétní instanci ID k produktu orchestrator a při jejím vytváření.

## <a name="singleton-example"></a>Příklad singleton

Následující příklad jazyka C# ukazuje funkci triggeru protokolu HTTP, která vytvoří orchestration úlohy pozadí typu singleton. Kód zajistí, že pouze jedna instance existuje pro ID zadané instance.

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    TraceWriter log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.Info($"Started orchestration with ID = '{instanceId}'.");
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

Ve výchozím nastavení instance, které jsou ID náhodně generované identifikátory GUID. Ale v takovém případě je instance ID předán v datech trasy z adresy URL. Volání kódu [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) ke kontrole, pokud je již spuštěna s zadané ID instance. Pokud ne, je vytvořena instance s ID tohoto.

Podrobnosti implementace orchestrator funkce není ve skutečnosti podstatné. Může to být regulární orchestrator funkci, která při spuštění a dokončení, nebo může být ten, který spouští navždy (tedy [Eternal Orchestration](durable-functions-eternal-orchestrations.md)). Důležité je, se někdy jedinou instanci spuštěné současně.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak volat dílčí orchestrations](durable-functions-sub-orchestrations.md)
