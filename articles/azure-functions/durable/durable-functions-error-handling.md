---
title: Zpracování chyb v Durable Functions – Azure
description: Zjistěte, jak chcete zpracovávat chyby ve rozšíření Durable Functions pro službu Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 7a55e28f34f36cd02b67e56c6262b9e1f06dde8f
ms.sourcegitcommit: edacc2024b78d9c7450aaf7c50095807acf25fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/13/2018
ms.locfileid: "53338188"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Zpracování chyb v Durable Functions (Azure Functions)

Trvalý Orchestrace funkce jsou implementované v kódu a může využívat možnosti zpracování chyb programovacího jazyka. S myslete na to ve skutečnosti nejsou k dispozici žádné nové koncepty, které je potřeba další informace o zpracování chyb a vyrovnání začleňte do vašeho Orchestrace. Existuje však několik chování, které byste měli vědět.

## <a name="errors-in-activity-functions"></a>Chyby v funkce aktivity

Je zařazeno zpět do funkce orchestrátoru a vyvolána jako jakoukoliv výjimku, která je vyvolána výjimka ve funkci aktivity `FunctionFailedException`. Můžete napsat kód pro manipulace a kompenzace chyba, která vyhovuje vašim potřebám, funkce orchestrátoru.

Představte si třeba následující funkce orchestrátoru, který převádí prostředků z jednoho účtu:

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```

Pokud volání **CreditAccount** funkce selže u cílového účtu, funkce orchestrátoru to vyrovnává podle kredity prostředků zpátky na zdrojový účet.

## <a name="automatic-retry-on-failure"></a>Automatické opakování při selhání

Při volání funkce aktivity nebo dílčí Orchestrace funkce, můžete určit zásadu automatické opakování. V následujícím příkladu se pokusí zavolat funkci až třikrát a čekat mezi opakováními 5 sekund:

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const retryOptions = new df.RetryOptions(5000, 3);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

`CallActivityWithRetryAsync` (.NET) nebo `callActivityWithRetry` přebírá (JavaScript) rozhraní API `RetryOptions` parametru. Suborchestration volá pomocí `CallSubOrchestratorWithRetryAsync` (.NET) nebo `callSubOrchestratorWithRetry` (JavaScript) rozhraní API můžete použít tyto stejné zásady opakování.

Existuje několik možností pro přizpůsobení zásady automatické opakování. Jsou to tyto země:

* **Maximální počet pokusů o**: Maximální počet opakovaných pokusů.
* **První interval opakování**: Množství času se má čekat před první pokus o opakování.
* **Omezení rychlosti koeficient**: Koeficient používá k určení míra zvýšení rychlosti. Výchozí hodnota je 1.
* **Maximální interval opakování**: Maximální množství času čekat mezi opakovanými pokusy.
* **Časového limitu opakování**: Počet opakování maximální množství času na to. Výchozí chování je to chcete zkusit znovu po neomezenou dobu.
* **Zpracování**: Uživatelské zpětné volání lze určit, který určuje, zda je třeba opakovat volání funkce.

## <a name="function-timeouts"></a>Vypršení časových limitů – funkce

Můžete chtít opustit volání funkce v rámci funkce orchestrátoru, pokud trvá moc dlouho. Správný způsob, jak to udělat ještě dnes je tak, že vytvoříte [trvalý časovače](durable-functions-timers.md) pomocí `context.CreateTimer` (.NET) nebo `context.df.createTimer` (JavaScript) ve spojení s `Task.WhenAny` (.NET) nebo `context.df.Task.any` (JavaScript), jako v následujícím příkladu:

### <a name="c"></a>C#

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

### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
        // timeout case
        return false;
    }
});
```

> [!NOTE]
> Tento mechanismus skutečně neukončí provádění funkce probíhající aktivity. Místo toho to jednoduše umožňuje funkce orchestrátoru ignorovat výsledek a přechod na. Další informace najdete v tématu [časovače](durable-functions-timers.md#usage-for-timeout) dokumentaci.

## <a name="unhandled-exceptions"></a>Neošetřené výjimky

Pokud selže funkce orchestrátoru s neošetřenou výjimku, podrobnosti o výjimce protokolují a dokončení instance s `Failed` stav.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak diagnostikovat problémy](durable-functions-diagnostics.md)
