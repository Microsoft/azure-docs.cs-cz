---
title: Externí orchestrace v Durable Functions – Azure
description: Naučte se implementovat orchestraci externí pomocí rozšíření Durable Functions pro Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: a04221a61ae057185ddd8ad37bbba2d387ee5eda
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097977"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Orchestrace externí v Durable Functions (Azure Functions)

*Orchestrace externí* jsou funkce nástroje Orchestrator, které nikdy nekončí. Jsou užitečné, pokud chcete použít [Durable Functions](durable-functions-overview.md) pro agregátory a jakýkoli scénář, který vyžaduje nekonečné smyčky.

## <a name="orchestration-history"></a>Historie orchestrace

Jak je vysvětleno v tématu vytvoření [kontrolního bodu a přehrání](durable-functions-checkpointing-and-replay.md), trvalá architektura úlohy sleduje historii každé orchestrace funkcí. Tato historie se průběžně rozroste, dokud funkce Orchestrator nadále plánuje novou práci. Pokud se funkce Orchestrator dostane do nekonečné smyčky a průběžně plánuje práci, může tato historie značně růst a způsobovat významné problémy s výkonem. Koncept *orchestrace externí* je navržený tak, aby zmírnil tyto druhy problémů pro aplikace, které potřebují nekonečné smyčky.

## <a name="resetting-and-restarting"></a>Resetování a restartování

Namísto použití nekonečné smyčky, funkce Orchestrator obnoví stav tím, že zavolá metodu [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) . Tato metoda přijímá jeden parametr s možností serializace JSON, který se stal novým vstupem pro další generování funkce Orchestrator.

Když `ContinueAsNew` je volána, instance před ukončením zachová zprávy do sebe samé. Zpráva restartuje instanci s novou vstupní hodnotou. Stejné ID instance je zachované, ale historie funkce Orchestrator je ve skutečnosti zkrácená.

> [!NOTE]
> Prostředí trvalého zpracování úloh udržuje stejné ID instance, ale interně vytvoří nové *ID spuštění* pro funkci Orchestrator, která resetuje `ContinueAsNew`. Toto ID spuštění většinou není vystaveno externě, ale může být užitečné, abyste měli informace o spuštění orchestrace ladění.

## <a name="periodic-work-example"></a>Příklad periodické práce

Jeden případ použití pro orchestraci externí je kód, který potřebuje provést pravidelnou práci po neomezenou dobu.

### <a name="c"></a>C#

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("DoCleanup");

    // sleep for one hour between cleanups
    const nextCleanup = moment.utc(context.df.currentUtcDateTime).add(1, "h");
    yield context.df.createTimer(nextCleanup.toDate());

    context.df.continueAsNew(undefined);
});
```

Rozdíl mezi tímto příkladem a funkcí aktivovanou časovačem je, že doba triggeru vyčištění tady není založena na plánu. Například plán CRON, který spouští funkci každou hodinu, se spustí v 1:00, 2:00, 3:00 atd. a může potenciálně vést k překrývání problémů. V tomto příkladu, pokud vyčištění trvá 30 minut, pak bude naplánováno na 1:00, 2:30, 4:00 atd. a neexistuje možnost překrytí.

## <a name="starting-an-eternal-orchestration"></a>Spuštění orchestrace externí
Pomocí metody [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_) spustíte orchestraci externí. To se neliší od aktivace jakékoli jiné funkce orchestrace.  

> [!NOTE]
> Pokud potřebujete zajistit, aby orchestrace typu Singleton externí běžela, je důležité při spouštění orchestrace zachovat stejnou `id` instanci. Další informace najdete v tématu [Správa instancí](durable-functions-instance-management.md).

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [OrchestrationClient] DurableOrchestrationClientBase client)
{
    string instanceId = "StaticId";
    // Null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.StartNewAsync("Periodic_Cleanup_Loop"), instanceId, null); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

## <a name="exit-from-an-eternal-orchestration"></a>Ukončení orchestrace externí

Pokud je nutné, aby funkce Orchestrator mohla být nakonec dokončena, Nevolejte `ContinueAsNew` vše, co je potřeba udělat, a nechejte funkci ukončit.

Pokud je funkce Orchestrator v nekonečné smyčce a je nutné ji zastavit, použijte k zastavení metodu [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) . Další informace najdete v tématu [Správa instancí](durable-functions-instance-management.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Naučte se implementovat orchestrace singleton.](durable-functions-singletons.md)
