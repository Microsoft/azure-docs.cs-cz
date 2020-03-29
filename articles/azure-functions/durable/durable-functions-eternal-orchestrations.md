---
title: Věčné orchestrace v trvalých funkcích – Azure
description: Zjistěte, jak implementovat věčné orchestrace pomocí rozšíření Trvalé funkce pro funkce Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 572fec4d6e47efd734bc84a40dc974c79bd619fb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76262975"
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Věčné orchestrace v trvalých funkcích (funkce Azure)

*Věčné orchestrace* jsou orchestrátorské funkce, které nikdy nekončí. Jsou užitečné, pokud chcete použít [trvalé funkce](durable-functions-overview.md) pro agregátory a jakýkoli scénář, který vyžaduje nekonečnou smyčku.

## <a name="orchestration-history"></a>Historie orchestrace

Jak je vysvětleno v tématu [historie orchestrace,](durable-functions-orchestrations.md#orchestration-history) rámec trvanlivých úloh sleduje historii orchestrace jednotlivých funkcí. Tato historie neustále roste tak dlouho, dokud funkce orchestrator pokračuje v plánování nové práce. Pokud funkce orchestrator přejde do nekonečné smyčky a nepřetržitě plánuje práci, tato historie může růst kriticky velké a způsobit významné problémy s výkonem. Koncept *věčné orchestrace* byl navržen tak, aby zmírnit tyto druhy problémů pro aplikace, které potřebují nekonečné smyčky.

## <a name="resetting-and-restarting"></a>Resetování a restartování

Namísto použití nekonečné smyčky, funkce orchestrator obnovit `ContinueAsNew` jejich stav `continueAsNew` voláním (.NET) nebo (JavaScript) metoda [orchestrace aktivační vazby](durable-functions-bindings.md#orchestration-trigger). Tato metoda trvá jeden parametr JSON serializable, který se stane novým vstupem pro další generaci funkce orchestratoru.

Při `ContinueAsNew` volání instance zařadí zprávu do samo před ukončením. Zpráva restartuje instanci s novou vstupní hodnotou. Stejné ID instance je zachována, ale historie funkce orchestrator je účinně zkrácena.

> [!NOTE]
> Architektura trvalé úlohy udržuje stejné ID instance, ale interně vytvoří nové *ID* `ContinueAsNew`spuštění pro funkci orchestrator, která získá reset ování . Toto ID spuštění obecně není vystavena externě, ale může být užitečné vědět o provádění ladění orchestrace.

## <a name="periodic-work-example"></a>Příklad periodické práce

Jeden případ použití pro věčné orchestrace je kód, který musí provádět periodické práce neomezeně dlouho.

# <a name="c"></a>[C #](#tab/csharp)

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
> Předchozí příklad Jazyka C# je pro trvalé funkce 2.x. Pro trvalé funkce 1.x, `DurableOrchestrationContext` musíte `IDurableOrchestrationContext`použít místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

Rozdíl mezi tímto příkladem a funkcí spouštěnou časovačem spočívá v tom, že aktivační časy vyčištění zde nejsou založeny na plánu. Například plán CRON, který každou hodinu spustí funkci, ji spustí v 1:00, 2:00, 3:00 atd. V tomto příkladu však pokud vyčištění trvá 30 minut, pak bude naplánováno na 1:00, 2:30, 4:00 atd.

## <a name="starting-an-eternal-orchestration"></a>Zahájení věčné orchestrace

Pomocí `StartNewAsync` metody (.NET) `startNew` nebo (JavaScript) můžete spustit věčnou orchestraci, stejně jako jakoukoli jinou funkci orchestrace.  

> [!NOTE]
> Pokud potřebujete zajistit, že je spuštěna trvalá orchestrace singletonu, `id` je důležité zachovat stejnou instanci při spuštění orchestrace. Další informace naleznete [v tématu Správa instancí](durable-functions-instance-management.md).

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("Trigger_Eternal_Orchestration")]
public static async Task<HttpResponseMessage> OrchestrationTrigger(
    [HttpTrigger(AuthorizationLevel.Function, "post", Route = null)] HttpRequestMessage request,
    [DurableClient] IDurableOrchestrationClient client)
{
    string instanceId = "StaticId";
    // Null is used as the input, since there is no input in "Periodic_Cleanup_Loop".
    await client.StartNewAsync("Periodic_Cleanup_Loop", instanceId, null); 
    return client.CreateCheckStatusResponse(request, instanceId);
}
```

> [!NOTE]
> Předchozí kód je pro trvalé funkce 2.x. Pro trvalé funkce 1.x `OrchestrationClient` je nutné `DurableClient` použít atribut namísto `DurableOrchestrationClient` atributu a `IDurableOrchestrationClient`místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

## <a name="exit-from-an-eternal-orchestration"></a>Odchod z věčné orchestrace

Pokud orchestrator funkce potřebuje nakonec dokončit, pak vše, co `ContinueAsNew` musíte *udělat,* není volání a nechat funkci ukončit.

Pokud orchestrator funkce je v nekonečné smyčce a `TerminateAsync` je třeba zastavit, použijte (.NET) nebo `terminate` (JavaScript) metoda [orchestrace vazby klienta](durable-functions-bindings.md#orchestration-client) zastavit. Další informace naleznete [v tématu Správa instancí](durable-functions-instance-management.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Naučte se implementovat orchestrace singletonu](durable-functions-singletons.md)
