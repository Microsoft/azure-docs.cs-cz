---
title: Časovače v Durable Functions – Azure
description: Přečtěte si, jak implementovat trvalé časovače v rozšíření Durable Functions pro Azure Functions.
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: azfuncdf
ms.openlocfilehash: 0226e5141b100aa3fcf89dd1a5cade8f3cd6cf1c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87056234"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Časovače v Durable Functions (Azure Functions)

[Durable Functions](durable-functions-overview.md) poskytuje *odolné časovače* pro použití ve funkcích nástroje Orchestrator pro implementaci zpoždění nebo nastavení časových limitů pro asynchronní akce. Trvalé časovače by se měly používat ve funkcích nástroje Orchestrator namísto `Thread.Sleep` a `Task.Delay` (C#) nebo `setTimeout()` a (JavaScript) `setInterval()` nebo `time.sleep()` (Python).

Můžete vytvořit trvalý časovač voláním `CreateTimer` metody (.NET) nebo `createTimer` metody (JavaScript) [aktivační vazby orchestration](durable-functions-bindings.md#orchestration-trigger). Metoda vrátí úlohu, která se dokončí k zadanému datu a času.

## <a name="timer-limitations"></a>Omezení časovače

Když vytvoříte časovač, jehož platnost vyprší v 4:30. odp., zařadí základní prostředí odolného úlohy do fronty zprávu, která se bude zobrazovat pouze na 4:30 ODP. Při spuštění v plánu Azure Functions spotřeby se nově zobrazená zpráva časovače ujistí, že se aplikace Function App aktivuje na příslušném virtuálním počítači.

> [!NOTE]
> * Trvalé časovače jsou momentálně omezeny na 7 dní. Pokud budete potřebovat delší prodlevy, je možné je simulovat pomocí rozhraní API časovače ve `while` smyčce.
> * Vždy používejte `CurrentUtcDateTime` místo `DateTime.UtcNow` v rozhraní .NET nebo `currentUtcDateTime` místo `Date.now` nebo `Date.UTC` v JavaScriptu při výpočtu doby požáru pro trvalé časovače. Další informace naleznete v článku o [omezeních kódu funkce nástroje Orchestrator](durable-functions-code-constraints.md) .

## <a name="usage-for-delay"></a>Využití pro zpoždění

Následující příklad ukazuje, jak použít trvalá časovače ke zpoždění provádění. Příklad vydává fakturační oznámení každý den po dobu 10 dnů.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> Předchozí příklad cíle jazyka C# Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationContext` místo `IDurableOrchestrationContext` . Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```
# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df
from datetime import datetime, timedelta

def orchestrator_function(context: df.DurableOrchestrationContext):
    for i in range(0, 9):
        deadline = context.current_utc_datetime + timedelta(days=1)
        yield context.create_timer(deadline)
        yield context.call_activity("SendBillingEvent")

main = df.Orchestrator.create(orchestrator_function)
```
---

> [!WARNING]
> Vyhněte se nekonečným smyčkám ve funkcích nástroje Orchestrator. Informace o tom, jak bezpečně a efektivně implementovat scénáře nekonečné smyčky, najdete v tématu [orchestrace externí](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Použití časového limitu

Tento příklad ukazuje, jak použít trvalé časovače k implementaci časových limitů.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
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
> Předchozí příklad cíle jazyka C# Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationContext` místo `IDurableOrchestrationContext` . Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
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
    deadline = context.current_utc_datetime + timedelta(seconds=30)
    activity_task = context.call_activity("GetQuote")
    timeout_task = context.create_timer(deadline)

    winner = yield context.task_any([activity_task, timeout_task])
    if winner == activity_task:
        timeout_task.cancel()
        return True
    elif winner == timeout_task:
        return False

main = df.Orchestrator.create(orchestrator_function)
```

---

> [!WARNING]
> Použijte `CancellationTokenSource` (.NET) nebo zavolejte `cancel()` vrácenou `TimerTask` (JavaScript), chcete-li zrušit trvalý časovač, pokud váš kód nebude čekat na jeho dokončení. Architektura trvalého úkolu nemění stav orchestrace na dokončeno, dokud nebudou dokončeny nebo zrušeny všechny zbývající úlohy.

Tento mechanismus zrušení neukončí probíhající funkci aktivity nebo provádění dílčí orchestrace. Místo toho jednoduše umožňuje, aby funkce Orchestrator ignorovala výsledek a přesunula se na. Pokud vaše aplikace Function App používá plán spotřeby, pořád se vám bude účtovat veškerá doba a paměť spotřebovaná funkcí opuštěná aktivita. Ve výchozím nastavení mají funkce spuštěné v plánu spotřeby časový limit pět minut. V případě překročení tohoto limitu se Azure Functions hostitel recykluje, aby zastavil všechna spuštění a zabránila situaci v fakturaci. [Časový limit funkce lze nakonfigurovat](../functions-host-json.md#functiontimeout).

Podrobnější příklad implementace časových limitů ve funkcích nástroje Orchestrator naleznete v článku o [& časových limitů pro lidské interakce – ověření pro telefon](durable-functions-phone-verification.md) .

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Informace o tom, jak vyvolat a zpracovávat externí události](durable-functions-external-events.md)
