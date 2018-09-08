---
title: Zpracování chyb v Durable Functions – Azure
description: Zjistěte, jak chcete zpracovávat chyby ve rozšíření Durable Functions pro službu Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: azfuncdf
ms.openlocfilehash: 0b19fe7441d3c2c5222095c31d9c3677b8c9cf34
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092713"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Zpracování chyb v Durable Functions (Azure Functions)

Trvalý Orchestrace funkce jsou implementované v kódu a může využívat možnosti zpracování chyb programovacího jazyka. S myslete na to ve skutečnosti nejsou k dispozici žádné nové koncepty, které je potřeba další informace o zpracování chyb a vyrovnání začleňte do vašeho Orchestrace. Existuje však několik chování, které byste měli vědět.

## <a name="errors-in-activity-functions"></a>Chyby v funkce aktivity

Je zařazeno zpět do funkce orchestrátoru a vyvolána jako jakoukoliv výjimku, která je vyvolána výjimka ve funkci aktivity `FunctionFailedException`. Můžete napsat kód pro manipulace a kompenzace chyba, která vyhovuje vašim potřebám, funkce orchestrátoru.

Představte si třeba následující funkce orchestrátoru, který převádí prostředků z jednoho účtu:

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

Pokud volání **CreditAccount** funkce selže u cílového účtu, funkce orchestrátoru to vyrovnává podle kredity prostředků zpátky na zdrojový účet.

## <a name="automatic-retry-on-failure"></a>Automatické opakování při selhání

Při volání funkce aktivity nebo dílčí Orchestrace funkce, můžete určit zásadu automatické opakování. V následujícím příkladu se pokusí zavolat funkci až třikrát a čekat mezi opakováními 5 sekund:

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

`CallActivityWithRetryAsync` Přebírá rozhraní API `RetryOptions` parametru. Suborchestration volá pomocí `CallSubOrchestratorWithRetryAsync` rozhraní API můžete použít tyto stejné zásady opakování.

Existuje několik možností pro přizpůsobení zásady automatické opakování. Jsou to tyto země:

* **Maximální počet pokusů o**: maximální počet opakovaných pokusů.
* **První interval opakování**: dobu čekání před prvním opakováním pokus.
* **Omezení rychlosti koeficient**: koeficient používá k určení míra zvýšení rychlosti. Výchozí hodnota je 1.
* **Maximální interval opakování**: maximální dobu čekání mezi pokusy o opakování.
* **Časového limitu opakování**: opakování maximální množství času na to. Výchozí chování je to chcete zkusit znovu po neomezenou dobu.
* **Vlastní**: uživatelské zpětné volání, je možné zadat, která určuje, zda je třeba opakovat volání funkce.

## <a name="function-timeouts"></a>Vypršení časových limitů – funkce

Můžete chtít opustit volání funkce v rámci funkce orchestrátoru, pokud trvá moc dlouho. Správný způsob, jak to udělat ještě dnes je tak, že vytvoříte [trvalý časovače](durable-functions-timers.md) pomocí `context.CreateTimer` ve spojení s `Task.WhenAny`, jako v následujícím příkladu:

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

> [!NOTE]
> Tento mechanismus skutečně neukončí provádění funkce probíhající aktivity. Místo toho to jednoduše umožňuje funkce orchestrátoru ignorovat výsledek a přechod na. Další informace najdete v tématu [časovače](durable-functions-timers.md#usage-for-timeout) dokumentaci.

## <a name="unhandled-exceptions"></a>Nezpracované výjimky

Pokud selže funkce orchestrátoru s neošetřenou výjimku, podrobnosti o výjimce protokolují a dokončení instance s `Failed` stav.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak diagnostikovat problémy](durable-functions-diagnostics.md)
