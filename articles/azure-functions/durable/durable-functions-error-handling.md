---
title: Zpracování chyb v Durable Functions – Azure
description: Naučte se zpracovávat chyby v rozšíření Durable Functions pro Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 447b3dcf5040835f5a853beff68bde794ece51f5
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78371910"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Zpracování chyb v Durable Functions (Azure Functions)

Trvalé orchestrace funkcí je implementována v kódu a může používat integrované funkce pro zpracování chyb v programovacím jazyce. V některých nových konceptech nemusíte se učit, jak přidat zpracování chyb a kompenzace do orchestrace. Existuje však několik chování, o kterých byste měli vědět.

## <a name="errors-in-activity-functions"></a>Chyby v aktivitách funkcí

Veškerá výjimka, která je vyvolána ve funkci aktivity, je zařazena zpět do funkce Orchestrator a vyvolána jako `FunctionFailedException`. Můžete zapsat zpracování chyb a kód kompenzace, který vyhovuje vašim potřebám ve funkci Orchestrator.

Zvažte například následující funkci Orchestrator, která přenáší prostředky z jednoho účtu na jiný:

# <a name="c"></a>[C#](#tab/csharp)

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
> Předchozí C# příklady jsou pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationContext` namísto `IDurableOrchestrationContext`. Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Pokud se první volání funkce **CreditAccount** nepovede, funkce Orchestrator se kompenzuje tím, že se prostředky přepíší kreditem zpět na zdrojový účet.

## <a name="automatic-retry-on-failure"></a>Automaticky opakovat při selhání

Při volání funkcí aktivity nebo funkcí dílčí orchestrace můžete zadat zásady automatického opakování. Následující příklad se pokusí zavolat funkci až třikrát a počkat 5 sekund mezi jednotlivými pokusy:

# <a name="c"></a>[C#](#tab/csharp)

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
> Předchozí C# příklady jsou pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationContext` namísto `IDurableOrchestrationContext`. Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Volání funkce Activity v předchozím příkladu přijímá parametr pro konfiguraci zásady automatického opakování. K dispozici je několik možností přizpůsobení zásady automatického opakování:

* **Maximální počet pokusů**: maximální počet opakovaných pokusů.
* **Interval prvního opakování**: doba, po kterou se má čekat před prvním pokusem.
* **Omezení rychlostiický koeficient**: koeficient použitý k určení míry zvýšení omezení rychlosti. Výchozí hodnota je 1.
* **Maximální interval opakování**: maximální doba, po kterou se má čekat mezi opakovanými pokusy.
* **Časový limit opakování**: maximální doba, po kterou se stráví opakované pokusy. Výchozí chování je opakovat po neomezenou dobu.
* **Popisovač**: pro určení, zda má být funkce opakována, lze určit uživatelem definované zpětné volání.

## <a name="function-timeouts"></a>Vypršení časových limitů funkcí

Je možné, že budete chtít opustit volání funkce ve funkci Orchestrator, pokud dokončení trvá příliš dlouho. Vhodným způsobem, jak to provést dnes, je vytvoření [trvalého časovače](durable-functions-timers.md) pomocí `context.CreateTimer` (.NET) nebo `context.df.createTimer` (JavaScript) ve spojení s `Task.WhenAny` (.NET) nebo `context.df.Task.any` (JavaScript), jako v následujícím příkladu:

# <a name="c"></a>[C#](#tab/csharp)

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
> Předchozí C# příklady jsou pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationContext` namísto `IDurableOrchestrationContext`. Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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
> Tento mechanismus ve skutečnosti neukončí probíhající zpracování funkce aktivity. Místo toho jednoduše umožňuje, aby funkce Orchestrator ignorovala výsledek a přesunula se na. Další informace najdete v dokumentaci k [časovačům](durable-functions-timers.md#usage-for-timeout) .

## <a name="unhandled-exceptions"></a>Neošetřené výjimky

Pokud funkce Orchestrator dojde k chybě s neošetřenou výjimkou, budou zaprotokolovány podrobnosti o výjimce a instance se dokončí se stavem `Failed`.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o orchestraci externí](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Zjistěte, jak diagnostikovat problémy.](durable-functions-diagnostics.md)
