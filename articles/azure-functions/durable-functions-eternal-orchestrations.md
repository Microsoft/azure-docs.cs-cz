---
title: Eternal orchestrations trvanlivý funkcí – Azure
description: Zjistěte, jak implementovat eternal orchestrations pomocí rozšíření trvanlivý funkce pro Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: f42526430599e47e673d359433e91b4687cbeb9e
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="eternal-orchestrations-in-durable-functions-azure-functions"></a>Eternal orchestrations trvanlivý funkcí (Azure Functions)

*Eternal orchestrations* jsou orchestrator funkce, které nikdy ukončení. Jsou užitečné v případě, že chcete použít [trvanlivý funkce](durable-functions-overview.md) agregátorů a všechny scénáře, který vyžaduje nekonečné smyčce.

## <a name="orchestration-history"></a>Historie Orchestration

Jak je popsáno v [vytváření kontrolních bodů a opětovného přehrání](durable-functions-checkpointing-and-replay.md), rozhraní trvanlivý úloh uchovává informace o historii každé funkce orchestration. Tato historie zvětšování nepřetržitě, pokud funkci orchestrator nadále naplánovat nové práci. Pokud funkci orchestrator přejde do nekonečné smyčce a průběžně plánuje práci, může tato historie růst zásadní velké a způsobit problémy s výkonem významné. *Eternal orchestration* koncept slouží ke snížení tyto druhy problémů pro aplikace, které potřebují nekonečné smyčky.

## <a name="resetting-and-restarting"></a>Resetování a restartování

Místo použití nekonečné smyčky, funkce orchestrator resetovat jejich stavu voláním [ContinueAsNew](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_ContinueAsNew_) metoda. Tato metoda přebírá jediný parametr serializovatelný JSON, který se stane nový vstup pro další funkce generování orchestrator.

Když `ContinueAsNew` nazývá enqueues instance zprávu na sebe sama předtím, než ho ukončí. Zpráva restartuje instanci s novou vstupní hodnotu. Stejné ID instance je zachován, ale funkce orchestrator historie efektivně se zkrátí.

> [!NOTE]
> Trvanlivý Framework úkolů udržuje stejné ID instance ale interně vytvoří novou *ID spuštění* pro funkci orchestrator, který získá resetováno `ContinueAsNew`. Toto ID spuštění nebude vystavena obecně externě, ale může být užitečné při ladění orchestration provádění vědět o.

> [!NOTE]
> `ContinueAsNew` Metoda dosud nejsou k dispozici v jazyce JavaScript.

## <a name="periodic-work-example"></a>Příklad pracovní periodické

Jeden případ použití pro eternal orchestrations je kód, který je potřeba udělat pracovní periodické po neomezenou dobu.

```csharp
[FunctionName("Periodic_Cleanup_Loop")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("DoCleanup");

    // sleep for one hour between cleanups
    DateTime nextCleanup = context.CurrentUtcDateTime.AddHours(1);
    await context.CreateTimer(nextCleanup, CancellationToken.None);

    context.ContinueAsNew(null);
}
```

Rozdíl mezi tento příklad a spustí časovač funkce je čištění aktivační událost časy zde nejsou podle plánu. Například CRON plánu, který spouští funkci každou hodinu, budou spuštěny ho v 1:00, 2:00, 3:00 atd. a může potenciálně spustit do překrytí problémy. V tomto příkladu, ale pokud vyčištění trvat 30 minut, potom naplánuje v 1:00, 2:30, atd. 4:00 a neexistuje možnost překrytí.

## <a name="counter-example"></a>Příklad čítače

Tady je příklad zjednodušené *čítač* funkce, která čeká na *přírůstek* a *snížení* eternally události.

```csharp
[FunctionName("SimpleCounter")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    int counterState = context.GetInput<int>();

    string operation = await context.WaitForExternalEvent<string>("operation");

    if (operation == "incr")
    {
        counterState++;
    }
    else if (operation == "decr")
    {
        counterState--;
    }
    
    context.ContinueAsNew(counterState);
}
```

## <a name="exit-from-an-eternal-orchestration"></a>Ukončit eternal orchestration

Pokud funkce orchestrator musí být ještě dokončena, pak všechny musíte udělat je *není* volání `ContinueAsNew` a nechat funkce ukončete.

Pokud funkce orchestrator v nekonečné smyčce a musí být zastaven, použijte [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_) metoda zastavte ji. Další informace najdete v tématu [Správa instancí](durable-functions-instance-management.md).

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak implementovat singleton orchestrations](durable-functions-singletons.md)
