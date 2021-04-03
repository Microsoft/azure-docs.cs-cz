---
title: Zpracování chyb v Durable Functions – Azure
description: Naučte se zpracovávat chyby v rozšíření Durable Functions pro Azure Functions.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: 023f9dfcc421935c3f7515e847108925d5e5521e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97673643"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Zpracování chyb v Durable Functions (Azure Functions)

Trvalé orchestrace funkcí je implementována v kódu a může používat integrované funkce pro zpracování chyb v programovacím jazyce. V některých nových konceptech nemusíte se učit, jak přidat zpracování chyb a kompenzace do orchestrace. Existuje však několik chování, o kterých byste měli vědět.

## <a name="errors-in-activity-functions"></a>Chyby v aktivitách funkcí

Veškerá výjimka, která je vyvolána ve funkci aktivity, je zařazena zpět do funkce Orchestrator a vyvolána jako `FunctionFailedException` . Můžete zapsat zpracování chyb a kód kompenzace, který vyhovuje vašim potřebám ve funkci Orchestrator.

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
> Předchozí příklady jazyka C# jsou pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationContext` místo `IDurableOrchestrationContext` . Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

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
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    transfer_details = context.get_input()

    yield context.call_activity('DebitAccount', {
         'account': transfer_details['sourceAccount'],
         'amount' : transfer_details['amount']
    })

    try:
        yield context.call_activity('CreditAccount', {
                'account': transfer_details['destinationAccount'],
                'amount': transfer_details['amount'],
            })
    except:
        yield context.call_activity('CreditAccount', {
            'account': transfer_details['sourceAccount'],
            'amount': transfer_details['amount']
        })

main = df.Orchestrator.create(orchestrator_function)
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
> Předchozí příklady jazyka C# jsou pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationContext` místo `IDurableOrchestrationContext` . Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

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

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    first_retry_interval_in_milliseconds = 5000
    max_number_of_attempts = 3

    retry_options = df.RetryOptions(first_retry_interval_in_milliseconds, max_number_of_attempts)

    yield context.call_activity_with_retry('FlakyFunction', retry_options)

main = df.Orchestrator.create(orchestrator_function)
```

---

Volání funkce Activity v předchozím příkladu přijímá parametr pro konfiguraci zásady automatického opakování. K dispozici je několik možností přizpůsobení zásady automatického opakování:

* **Maximální počet pokusů**: maximální počet opakovaných pokusů.
* **Interval prvního opakování**: doba, po kterou se má čekat před prvním pokusem.
* **Omezení rychlostiický koeficient**: koeficient použitý k určení míry zvýšení omezení rychlosti. Výchozí hodnota je 1.
* **Maximální interval opakování**: maximální doba, po kterou se má čekat mezi opakovanými pokusy.
* **Časový limit opakování**: maximální doba, po kterou se stráví opakované pokusy. Výchozí chování je opakovat po neomezenou dobu.
* **Popisovač**: pro určení, zda má být funkce opakována, lze určit uživatelem definované zpětné volání. 

> [!NOTE]
> Uživatelem definovaná zpětná volání nejsou aktuálně podporována Durable Functions v jazyce JavaScript ( `context.df.RetryOptions` ).


## <a name="function-timeouts"></a>Vypršení časových limitů funkcí

Je možné, že budete chtít opustit volání funkce ve funkci Orchestrator, pokud dokončení trvá příliš dlouho. Vhodným způsobem, jak to provést dnes, je vytvoření [trvalého časovače](durable-functions-timers.md) pomocí `context.CreateTimer` (.NET), `context.df.createTimer` (JavaScript) nebo `context.create_timer` (Python) ve spojení s `Task.WhenAny` (.NET), `context.df.Task.any` (JavaScript) nebo `context.task_any` (Python), jak je uvedeno v následujícím příkladu:

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
> Předchozí příklady jazyka C# jsou pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationContext` místo `IDurableOrchestrationContext` . Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

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
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    deadline = context.current_utc_datetime + timedelta(seconds = 30)
    
    activity_task = context.call_activity('FlakyFunction')
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any(activity_task, timeout_task)
    if winner == activity_task:
        timeout_task.cancel()
        return True
    else:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

---

> [!NOTE]
> Tento mechanismus ve skutečnosti neukončí probíhající zpracování funkce aktivity. Místo toho jednoduše umožňuje, aby funkce Orchestrator ignorovala výsledek a přesunula se na. Další informace najdete v dokumentaci k [časovačům](durable-functions-timers.md#usage-for-timeout) .

## <a name="unhandled-exceptions"></a>Neošetřené výjimky

Pokud funkce Orchestrator dojde k chybě s neošetřenou výjimkou, budou zaprotokolovány podrobnosti o výjimce a instance se dokončí se `Failed` stavem.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o orchestraci externí](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Zjistěte, jak diagnostikovat problémy.](durable-functions-diagnostics.md)
