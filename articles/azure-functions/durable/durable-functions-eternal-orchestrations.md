---
title: Externí orchestrace v Durable Functions – Azure
description: Naučte se implementovat orchestraci externí pomocí rozšíření Durable Functions pro Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d55e08fecbd1338284607ac59fe354c6fa8cb1ea
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80478817"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Orchestrace externí v Durable Functions (Azure Functions)

*Orchestrace externí* jsou funkce nástroje Orchestrator, které nikdy nekončí. Jsou užitečné, pokud chcete použít [Durable Functions](durable-functions-overview.md) pro agregátory a jakýkoli scénář, který vyžaduje nekonečné smyčky.

## <a name="orchestration-history"></a>Historie orchestrace

Jak je vysvětleno v tématu [Historie orchestrace](durable-functions-orchestrations.md#orchestration-history) , trvalá architektura pro úlohy sleduje historii každé orchestrace funkcí. Tato historie se průběžně rozroste, dokud funkce Orchestrator nadále plánuje novou práci. Pokud se funkce Orchestrator dostane do nekonečné smyčky a průběžně plánuje práci, může tato historie značně růst a způsobovat významné problémy s výkonem. Koncept *orchestrace externí* je navržený tak, aby zmírnil tyto druhy problémů pro aplikace, které potřebují nekonečné smyčky.

## <a name="resetting-and-restarting"></a>Resetování a restartování

Namísto použití nekonečné smyčky, funkce Orchestrator obnoví jejich stav voláním metody `ContinueAsNew` (.NET) nebo `continueAsNew` (JavaScript) [aktivační vazby orchestration](durable-functions-bindings.md#orchestration-trigger). Tato metoda přijímá jeden parametr s možností serializace JSON, který se stal novým vstupem pro další generování funkce Orchestrator.

Když `ContinueAsNew` je volána, instance před ukončením zachová zprávy do sebe samé. Zpráva restartuje instanci s novou vstupní hodnotou. Stejné ID instance je zachované, ale historie funkce Orchestrator je ve skutečnosti zkrácená.

> [!NOTE]
> Prostředí trvalého zpracování úloh udržuje stejné ID instance, ale interně vytvoří nové *ID spuštění* pro funkci Orchestrator, která resetuje `ContinueAsNew`. Toto ID spuštění většinou není vystaveno externě, ale může být užitečné, abyste měli informace o spuštění orchestrace ladění.

## <a name="periodic-work-example"></a>Příklad periodické práce

Jeden případ použití pro orchestraci externí je kód, který potřebuje provést pravidelnou práci po neomezenou dobu.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup", null);

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

> [!NOTE]
> Předchozí příklad v jazyce C# je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationContext` místo. `IDurableOrchestrationContext` Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

---

Rozdíl mezi tímto příkladem a funkcí aktivovanou časovačem je, že doba triggeru vyčištění tady není založena na plánu. Například plán CRON, který spouští funkci každou hodinu, se spustí v 1:00, 2:00, 3:00 atd. a může potenciálně vést k překrývání problémů. V tomto příkladu, pokud vyčištění trvá 30 minut, pak bude naplánováno na 1:00, 2:30, 4:00 atd. a neexistuje možnost překrytí.

## <a name="starting-an-eternal-orchestration"></a>Spuštění orchestrace externí

Použijte metodu `StartNewAsync` (.NET) nebo `startNew` (JavaScript) k zahájení orchestrace externí, stejně jako u jakékoli jiné funkce orchestrace.  

> [!NOTE]
> Pokud potřebujete zajistit, aby orchestrace typu Singleton externí běžela, je důležité při spouštění orchestrace zachovat stejnou `id` instanci. Další informace najdete v tématu [Správa instancí](durable-functions-instance-management.md).

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";

    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> Předchozí kód je pro Durable Functions 2. x. Pro Durable Functions `OrchestrationClient` 1. x je nutné použít atribut namísto `DurableClient` atributu a místo toho musíte použít typ `DurableOrchestrationClient` parametru. `IDurableOrchestrationClient` Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);
    const instanceId = "StaticId";
    
    // null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.startNew("Periodic_Cleanup_Loop", instanceId, null);

    context.log(`Started orchestration with ID = '${instanceId}'.`);
    return client.createCheckStatusResponse(context.bindingData.req, instanceId);
};
```

---

## <a name="exit-from-an-eternal-orchestration"></a>Ukončení orchestrace externí

Pokud je nutné, aby funkce Orchestrator mohla být nakonec dokončena, *Nevolejte* `ContinueAsNew` vše, co je potřeba udělat, a nechejte funkci ukončit.

Pokud je funkce Orchestrator v nekonečné smyčce a je potřeba ji zastavit, použijte k zastavení `TerminateAsync` [vazby klienta Orchestration](durable-functions-bindings.md#orchestration-client) metodu `terminate` (.NET) nebo (JavaScript). Další informace najdete v tématu [Správa instancí](durable-functions-instance-management.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Naučte se implementovat orchestrace singleton.](durable-functions-singletons.md)
