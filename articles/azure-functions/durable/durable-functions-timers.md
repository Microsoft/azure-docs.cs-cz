---
title: Časovače v Durable Functions – Azure
description: Přečtěte si, jak implementovat trvalé časovače v rozšíření Durable Functions pro Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: azfuncdf
ms.openlocfilehash: 11edfc11fc1e54684a99774c21517d4c322348b1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087048"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Časovače v Durable Functions (Azure Functions)

[Durable Functions](durable-functions-overview.md) poskytuje *odolné časovače* pro použití ve funkcích nástroje Orchestrator pro implementaci zpoždění nebo nastavení časových limitů pro asynchronní akce. Trvalé časovače by se měly používat ve funkcích nástroje `Thread.Sleep` Orchestrator `Task.Delay` namístoC#a () `setTimeout()` , `setInterval()` nebo a (JavaScript).

Můžete vytvořit trvalý časovač voláním metody [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) v rozhraní .NET nebo `createTimer` metody `DurableOrchestrationContext` v jazyce JavaScript. Metoda vrátí úlohu, která obnoví zadané datum a čas.

## <a name="timer-limitations"></a>Omezení časovače

Když vytvoříte časovač, jehož platnost vyprší v 4:30./odp., zařadí základní rámec úloh do fronty zprávu, která se bude zobrazovat pouze v 4:30 ODP. Při spuštění v plánu Azure Functions spotřeby se nově zobrazená zpráva časovače ujistí, že se aplikace Function App aktivuje na příslušném virtuálním počítači.

> [!NOTE]
> * Trvalá časovače nemohou být poslední déle než 7 dní z důvodu omezení v Azure Storage. Pracujeme na [žádosti o funkce pro rozšiřování časovačů po dobu 7 dnů](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * Vždy používejte [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) místo `DateTime.UtcNow` v `currentUtcDateTime` `Date.UTC` rozhraní .NET a místo nebovjazyceJavaScript,jakjeznázorněnovnížeuvedenýchpříkladech,přivýpočturelativníhotermínutrvaléhočasovače.`Date.now`

## <a name="usage-for-delay"></a>Využití pro zpoždění

Následující příklad ukazuje, jak použít trvalá časovače ke zpoždění provádění. Příklad vydává fakturační oznámení každý den po dobu deseti dní.

### <a name="c"></a>C#

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const dayOfMonth = context.df.currentUtcDateTime.getDate();
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

> [!WARNING]
> Vyhněte se nekonečným smyčkám ve funkcích nástroje Orchestrator. Informace o tom, jak bezpečně a efektivně implementovat scénáře nekonečné smyčky, najdete v tématu [orchestrace externí](durable-functions-eternal-orchestrations.md).

## <a name="usage-for-timeout"></a>Použití časového limitu

Tento příklad ukazuje, jak použít trvalé časovače k implementaci časových limitů.

### <a name="c"></a>C#

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

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

> [!WARNING]
> Použijte k zrušení trvalého časovače (C#) nebo volání `cancel()` vráceného `TimerTask` (JavaScript), pokud kód nebude čekat na jeho dokončení. `CancellationTokenSource` Architektura trvalého úkolu nemění stav orchestrace na dokončeno, dokud nebudou dokončeny nebo zrušeny všechny zbývající úlohy.

Tento mechanismus ve skutečnosti neukončí probíhající zpracování funkce aktivity. Místo toho jednoduše umožňuje, aby funkce Orchestrator ignorovala výsledek a přesunula se na. Pokud vaše aplikace Function App používá plán spotřeby, bude se vám stále účtovat veškerá doba a paměť spotřebovaná funkcí opuštěné aktivity. Ve výchozím nastavení mají funkce spuštěné v plánu spotřeby časový limit pět minut. V případě překročení tohoto limitu se Azure Functions hostitel recykluje, aby zastavil všechna spuštění a zabránila situaci v fakturaci. [Časový limit funkce lze nakonfigurovat](../functions-host-json.md#functiontimeout).

Podrobnější příklad implementace časových limitů ve funkcích nástroje Orchestrator naleznete v článku Průvodce [časovými limity lidské interakce & – telefon ověřování](durable-functions-phone-verification.md) .

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Informace o tom, jak vyvolat a zpracovávat externí události](durable-functions-external-events.md)
