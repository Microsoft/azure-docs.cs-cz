---
title: Časovače v odolných funkcích – Azure
description: Zjistěte, jak implementovat trvalé časovače v rozšíření Trvalé funkce pro funkce Azure.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 0565cc149a36baf31d8516fffcf48b194c465760
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261479"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Časovače v odolných funkcích (funkce Azure)

[Trvalé funkce](durable-functions-overview.md) poskytuje *trvalé časovače* pro použití ve funkcích orchestrator implementovat zpoždění nebo nastavit časové to-outy na asynchronní akce. Trvalé časovače by měly být použity v funkcích orchestrator namísto `Thread.Sleep` a `Task.Delay` (C#), nebo `setTimeout()` (JavaScript). `setInterval()`

Trvalý časovač vytvoříte voláním metody `CreateTimer` (.NET) nebo `createTimer` (JavaScript) [vazby aktivační události orchestrace](durable-functions-bindings.md#orchestration-trigger). Metoda vrátí úkol, který se dokončí k zadanému datu a času.

## <a name="timer-limitations"></a>Omezení časovače

Když vytvoříte časovač, který vyprší v 16:30, základní architektura trvalé úlohy zařadí do fronty zprávu, která se zobrazí pouze v 16:30. Při spuštění v plánu Azure Functions Consumption, nově viditelná zpráva časovače zajistí, že aplikace funkce se aktivuje na příslušném virtuálním počítači.

> [!NOTE]
> * Odolné časovače jsou v současné době omezeny na 7 dní. Pokud jsou zapotřebí delší zpoždění, mohou být simulovány pomocí nastavení API časovače ve `while` smyčce.
> * Při `CurrentUtcDateTime` výpočtu `DateTime.UtcNow` doby požáru `Date.now` pro `Date.UTC` trvalé časovače vždy používejte místo v rozhraní .NET nebo `currentUtcDateTime` namísto javascriptu. Další informace naleznete v článku [omezení kódu funkce orchestratoru.](durable-functions-code-constraints.md)

## <a name="usage-for-delay"></a>Použití pro zpoždění

Následující příklad ukazuje, jak používat trvalé časovače pro zpoždění spuštění. Příkladem je vydávání oznámení o fakturaci každý den po dobu 10 dnů.

# <a name="c"></a>[C #](#tab/csharp)

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
> Předchozí příklad jazyka C# cílí na trvalé funkce 2.x. Pro trvalé funkce 1.x, `DurableOrchestrationContext` musíte `IDurableOrchestrationContext`použít místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

> [!WARNING]
> Vyhněte se nekonečné smyčky v orchestrátoru funkcí. Informace o tom, jak bezpečně a efektivně implementovat scénáře nekonečné smyčky, naleznete [v tématu Eternal Orchestrations](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Použití časového omezení

Tento příklad ukazuje, jak používat trvalé časovače k implementaci časových opovenek.

# <a name="c"></a>[C #](#tab/csharp)

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
> Předchozí příklad jazyka C# cílí na trvalé funkce 2.x. Pro trvalé funkce 1.x, `DurableOrchestrationContext` musíte `IDurableOrchestrationContext`použít místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

> [!WARNING]
> Použijte `CancellationTokenSource` (.NET) nebo `cancel()` volání `TimerTask` na vrácené (JavaScript) zrušit trvalý časovač, pokud váš kód nebude čekat na jeho dokončení. Rámec trvalých úloh nezmění stav orchestrace na "dokončeno", dokud nebudou dokončeny nebo zrušeny všechny zbývající úkoly.

Tento mechanismus zrušení neukončí probíhající funkce aktivity nebo provádění dílčí orchestrace. Spíše jednoduše umožňuje funkci orchestrator ignorovat výsledek a jít dál. Pokud vaše aplikace funkcí používá plán Spotřeba, bude se vám stále účtovat za čas a paměť spotřebovanou funkcí opuštěné aktivity. Ve výchozím nastavení mají funkce spuštěné v plánu Spotřeba časový limit pět minut. Pokud je tento limit překročen, hostitel Azure Functions se recykluje, aby se zastavilo veškeré spuštění a zabránilo se situaci s runaway fakturace. [Časový čas funkce je konfigurovatelný](../functions-host-json.md#functiontimeout).

Podrobnější příklad implementace časových lhůt ve funkcích orchestrátoru naleznete v článku Časové & [lidské interakce – ověření telefonu.](durable-functions-phone-verification.md)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Naučte se, jak vyvolat a zpracovat externí události](durable-functions-external-events.md)
