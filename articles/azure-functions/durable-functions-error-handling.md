---
title: Zpracování chyb v trvanlivý funkce – Azure
description: Zjistěte, jak se budou zpracovávat chyby v rozšíření trvanlivý funkce pro Azure Functions.
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
ms.openlocfilehash: 108c6020956b398effb8ba9dd4471190362359d6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Zpracování chyb v trvanlivý funkce (Azure Functions)

Trvanlivý orchestrations funkce jsou implementované v kódu a může využívat možnosti zpracování chyb programovacího jazyka. Myslete na to ve skutečnosti nejsou k dispozici žádné nových konceptů, které potřebujete další informace o zapojení do vaší orchestrations zpracování chyb a honoráře. Existuje však několik chování, které byste měli vědět.

## <a name="errors-in-activity-functions"></a>Chyby ve funkcích aktivity

Jakékoli výjimky vyvolané v funkci aktivity je zařazeno zpět na funkce produktu orchestrator a vyvolána jako `FunctionFailedException`. Můžete napsat kód chyby zpracování a kompenzace, která vyhovuje vašim potřebám, funkce produktu orchestrator.

Zvažte například následující funkci orchestrator, která převádí prostředků z jednoho účtu:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(DurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        { 
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",         
            new
            { 
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",         
            new
            { 
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

Pokud volání **CreditAccount** funkce selže u cílového účtu, funkce orchestrator kompenzuje to podle kredity prostředků zpátky na zdrojový účet.

## <a name="automatic-retry-on-failure"></a>Automatické opakování při selhání

Při volání funkce aktivity nebo dílčí orchestration funkce můžete určit automatické zásady opakování. V následujícím příkladu se pokusí volání funkce až 3 x a čeká 5 sekund mezi každou opakování:

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);
    
    // ...
}
```

`CallActivityWithRetryAsync` Trvá rozhraní API `RetryOptions` parametr. Dílčí orchestration volá pomocí `CallSubOrchestratorWithRetryAsync` rozhraní API můžete použít tyto stejné zásady opakování.

Existuje několik možností pro přizpůsobení zásady automatické opakování. Jsou to tyto země:

* **Maximální počet pokusů o**: maximální počet opakovaných pokusů.
* **První pokus**: časového intervalu čekání před první opakovat pokus.
* **Koeficient omezení rychlosti**: koeficient používá k určení míra zvýšení omezení rychlosti. Výchozí hodnota je 1.
* **Maximální interval opakování**: maximální dobu čekání mezi pokusy o opakování.
* **Opakujte časový limit**: maximální množství času na takovém opakování. Výchozí chování je opakovat po neomezenou dobu.
* **Vlastní**: uživatelské zpětné volání lze, která určuje, zda je třeba opakovat volání funkce.

## <a name="function-timeouts"></a>Časové limity – funkce

Můžete chtít abandon volání funkce v rámci orchestrator funkce, pokud to trvá příliš dlouho dokončení. Je správný způsob k tomu ještě dnes tak, že vytvoříte [trvanlivý časovače](durable-functions-timers.md) pomocí `context.CreateTimer` ve spojení s `Task.WhenAny`, jako v následujícím příkladu:

```csharp
public static async Task<bool> Run(DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

## <a name="unhandled-exceptions"></a>Nezpracované výjimky

Pokud funkce orchestrator selže s nezpracovanou výjimku, se zaznamenávají podrobnosti o výjimce a instance dokončí s `Failed` stavu.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak diagnostikovat problémy](durable-functions-diagnostics.md)
