---
title: Zpracování chyb v Durable Functions – Azure
description: Naučte se zpracovávat chyby v rozšíření Durable Functions pro Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 7b357189a9ce67f27952985b78dd3134517ffba5
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734307"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Zpracování chyb v Durable Functions (Azure Functions)

Trvalé orchestrace funkcí je implementována v kódu a může používat funkce pro zpracování chyb v programovacím jazyce. V takovém případě nemusíte mít žádné nové koncepty, které byste se měli seznámit s zahrnutím zpracování chyb a kompenzací do orchestrace. Existuje však několik chování, o kterých byste měli vědět.

## <a name="errors-in-activity-functions"></a>Chyby v aktivitách funkcí

Veškerá výjimka, která je vyvolána ve funkci aktivity, je zařazena zpět do funkce Orchestrator a vyvolána jako `FunctionFailedException`. Můžete zapsat zpracování chyb a kód kompenzace, který vyhovuje vašim potřebám ve funkci Orchestrator.

Zvažte například následující funkci Orchestrator, která přenáší prostředky z jednoho účtu na jiný:

### <a name="precompiled-c"></a>PředkompilovanéC#

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
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

### <a name="c-script"></a>C#Pravidel

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

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

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

Pokud se volání funkce **CreditAccount** u cílového účtu nepovede, funkce Orchestrator tuto náhradu kompenzuje tím, že prostředky přepíše na účet zpět do zdrojového účtu.

## <a name="automatic-retry-on-failure"></a>Automaticky opakovat při selhání

Při volání funkcí aktivity nebo funkcí dílčí orchestrace můžete zadat zásady automatického opakování. Následující příklad se pokusí zavolat funkci až třikrát a počkat 5 sekund mezi jednotlivými pokusy:

### <a name="precompiled-c"></a>PředkompilovanéC#

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

### <a name="c-script"></a>C#Pravidel

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

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const retryOptions = new df.RetryOptions(5000, 3);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

Rozhraní API `callActivityWithRetry` (.NET`RetryOptions` ) nebo (JavaScript) přebírá parametr. `CallActivityWithRetryAsync` Pomocí rozhraní API (.NET) `CallSubOrchestratorWithRetryAsync` nebo `callSubOrchestratorWithRetry` (JavaScript) můžete stejné zásady opakování použít k podorchestraci.

K dispozici je několik možností přizpůsobení zásady automatického opakování. Jsou to tyto země:

* **Maximální počet pokusů**: Maximální počet opakovaných pokusů.
* **Interval prvního opakování**: Doba, po kterou se má čekat před prvním pokusem.
* **Omezení rychlosti koeficient**: Koeficient použitý k určení míry zvýšení omezení rychlosti. Výchozí hodnota je 1.
* **Maximální interval opakování**: Maximální doba, po kterou se má čekat mezi opakovanými pokusy
* **Časový limit opakování**: Maximální doba, po kterou se stráví opakované pokusy. Výchozí chování je opakovat po neomezenou dobu.
* **Popisovač**: Je možné zadat uživatelem definované zpětné volání, které určuje, zda má být volání funkce opakováno.

## <a name="function-timeouts"></a>Vypršení časových limitů funkcí

Je možné, že budete chtít opustit volání funkce ve funkci Orchestrator, pokud dokončení trvá příliš dlouho. Vhodným způsobem, jak to provést dnes, je vytvoření [trvalého časovače](durable-functions-timers.md) pomocí `context.CreateTimer` (.NET `context.df.createTimer` ) nebo (JavaScript) ve `Task.WhenAny` spojení s (.NET `context.df.Task.any` ) nebo (JavaScript), jako v následujícím příkladu:

### <a name="precompiled-c"></a>PředkompilovanéC#

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] DurableOrchestrationContext context)
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

### <a name="c-script"></a>C#Pravidel

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

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

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
> Tento mechanismus ve skutečnosti neukončí probíhající zpracování funkce aktivity. Místo toho jednoduše umožňuje, aby funkce Orchestrator ignorovala výsledek a přesunula se na. Další informace najdete v dokumentaci k [časovačům](durable-functions-timers.md#usage-for-timeout) .

## <a name="unhandled-exceptions"></a>Neošetřené výjimky

Pokud funkce Orchestrator dojde k chybě s neošetřenou výjimkou, budou zaprotokolovány podrobnosti o výjimce a instance se dokončí se `Failed` stavem.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak diagnostikovat problémy.](durable-functions-diagnostics.md)