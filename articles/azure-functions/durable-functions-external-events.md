---
title: "Zpracování externích událostí trvanlivý funkce – Azure"
description: "Naučte se zpracování externí událostí v rozšíření trvanlivý funkce pro Azure Functions."
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
ms.openlocfilehash: 5ffbe6a7d74f0be2193d711d304f19e62ab08741
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Zpracování externích událostí trvanlivý funkce (Azure Functions)

Funkce produktu Orchestrator mají možnost počkejte a naslouchání událostem externí. Tato funkce [trvanlivý funkce](durable-functions-overview.md) je často užitečné pro zpracování zásahem ze strany nebo jiné externí aktivační události.

## <a name="wait-for-events"></a>Počkejte události

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) metoda umožňuje funkce orchestrator asynchronně počkejte a naslouchat externí události. Deklaruje naslouchání funkce orchestrator *název* události a *tvar dat* očekává příjem.

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

V předchozím příkladu naslouchá konkrétní jedna událost a provede akci, když je obdržena.

Můžete naslouchat více událostí souběžně, jako v následujícím příkladu, který čeká na jednu tři možné události oznámení.

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

Čeká na předchozí příklad *žádné* více událostí. Je také možné počkejte *všechny* události.

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) čeká na dobu neurčitou některé vstup.  Funkce aplikace může být bezpečně zrušeno při čekání. Pokud události dorazí pro tuto instanci orchestration, automaticky se probudí a okamžitě zpracovává událost.

> [!NOTE]
> Pokud aplikace funkce používá spotřeba plánování, žádné poplatky se vám neúčtují při funkce orchestrator čekající úlohy ze `WaitForExternalEvent`, bez ohledu na to, jak dlouho čekat.

Pokud datová část události nelze převést na očekávaný typ `T`, je vyvolána výjimka.

## <a name="send-events"></a>Odesílání událostí

[RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) metodu [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) třída odesílá události, který `WaitForExternalEvent` čeká.  `RaiseEventAsync` Metoda trvá *eventName* a *eventData* jako parametry. Data události musí být serializovatelný JSON.

Níže je příklad fronty aktivované funkce, odešle událost "Schválení" na instanci funkce produktu orchestrator. Orchestration instance ID pochází z tělo zprávy ve frontě.

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

Interně `RaiseEventAsync` enqueues zprávu, která se získá zachyceny orchestrator funkcí čekání.

> [!WARNING]
> Pokud není žádná instance orchestration se zadaným *instance ID* nebo pokud není instance čekání na zadaný *název události*, zpráva o události se zahodí. Další informace o tomto chování najdete v tématu [potíže Githubu](https://github.com/Azure/azure-functions-durable-extension/issues/29).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak nastavit eternal orchestrations](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Spustit ukázku, která čeká na externí události](durable-functions-phone-verification.md)

> [!div class="nextstepaction"]
> [Spustit ukázku, která čeká na zásahem ze strany](durable-functions-phone-verification.md)

