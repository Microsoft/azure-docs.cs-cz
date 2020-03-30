---
title: Zpracování chyb v trvanlivých funkcích – Azure
description: Zjistěte, jak zpracovat chyby v rozšíření trvalé funkce pro funkce Azure.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 447b3dcf5040835f5a853beff68bde794ece51f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277853"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Zpracování chyb v odolných funkcích (Funkce Azure)

Orchestrace trvalých funkcí jsou implementovány v kódu a mohou používat integrované funkce zpracování chyb programovacího jazyka. Opravdu nejsou žádné nové koncepty, které byste se měli naučit přidávat zpracování chyb a kompenzaci do orchestrations. Existuje však několik chování, které byste měli být vědomi.

## <a name="errors-in-activity-functions"></a>Chyby ve funkcích aktivity

Každá výjimka, která je vyvolána ve funkci aktivity je zařazen zpět `FunctionFailedException`do funkce orchestrator a vyvolána jako . Můžete napsat kód pro zpracování chyb a kompenzační kód, který vyhovuje vašim potřebám ve funkci orchestrator.

Zvažte například následující funkci orchestrator, která převádí prostředky z jednoho účtu na druhý:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = context.GetInput<TransferOperation>();

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

> [!NOTE]
> Předchozí příklady jazyka C# jsou pro trvalé funkce 2.x. Pro trvalé funkce 1.x, `DurableOrchestrationContext` musíte `IDurableOrchestrationContext`použít místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

Pokud první volání funkce **CreditAccount** selže, funkce orchestrator kompenzuje připsáním prostředků zpět na zdrojový účet.

## <a name="automatic-retry-on-failure"></a>Automatické opakování při poruše

Při volání funkce aktivity nebo funkce dílčí orchestrace, můžete zadat zásady automatickéopakování. Následující příklad se pokusí volat funkci až třikrát a čeká 5 sekund mezi jednotlivými opakování:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await context.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> Předchozí příklady jazyka C# jsou pro trvalé funkce 2.x. Pro trvalé funkce 1.x, `DurableOrchestrationContext` musíte `IDurableOrchestrationContext`použít místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

---

Volání funkce aktivity v předchozím příkladu přebírá parametr pro konfiguraci zásad automatického opakování. Zásady automatického opakování lze přizpůsobit několika možnostmi:

* **Maximální počet pokusů**: Maximální počet pokusů o opakování.
* **Interval prvního opakování**: Doba čekání před prvním pokusem o opakování.
* **Koeficient zpětného vypnutí**: Koeficient použitý ke stanovení rychlosti zvýšení backoff. Výchozí hodnota je 1.
* **Maximální interval opakování**: Maximální doba čekání mezi pokusy o opakování.
* **Časový limit opakování**: Maximální doba, kterou můžete strávit opakováním. Výchozí chování je opakovat neomezeně dlouho.
* **Popisovač**: Uživatelem definované zpětné volání lze zadat k určení, zda má být funkce opakována.

## <a name="function-timeouts"></a>Časové opozí funkce

Můžete chtít opustit volání funkce v rámci funkce orchestrator, pokud trvá příliš dlouho na dokončení. Správný způsob, jak to udělat dnes je [vytvořením trvalý časovač](durable-functions-timers.md) pomocí `context.CreateTimer` (.NET) nebo `context.df.createTimer` (JavaScript) ve spojení s `Task.WhenAny` (.NET) nebo `context.df.Task.any` (JavaScript), jako v následujícím příkladu:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
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
> Předchozí příklady jazyka C# jsou pro trvalé funkce 2.x. Pro trvalé funkce 1.x, `DurableOrchestrationContext` musíte `IDurableOrchestrationContext`použít místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

> [!NOTE]
> Tento mechanismus ve skutečnosti neukončí provádění funkce probíhající aktivity. Spíše jednoduše umožňuje funkci orchestrator ignorovat výsledek a jít dál. Další informace naleznete v dokumentaci [časovače.](durable-functions-timers.md#usage-for-timeout)

## <a name="unhandled-exceptions"></a>Neošetřené výjimky

Pokud funkce orchestrator selže s neošetřenou výjimkou, podrobnosti o výjimce `Failed` jsou zaznamenány a instance dokončí stav.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o věčných orchestracích](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Naučte se diagnostikovat problémy](durable-functions-diagnostics.md)
