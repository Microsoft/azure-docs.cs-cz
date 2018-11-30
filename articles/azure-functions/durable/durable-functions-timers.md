---
title: Časovače v Durable Functions – Azure
description: Zjistěte, jak implementovat trvalý časovače v rozšíření Durable Functions pro službu Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: azfuncdf
ms.openlocfilehash: ad6ddacad322e4c2f952591be786d46cbcb95a21
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52642687"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Časovače v Durable Functions (Azure Functions)

[Odolná služba Functions](durable-functions-overview.md) poskytuje *trvalý časovače* pro použití ve funkcích produktu orchestrator k implementaci zpoždění nebo nastavit vypršení časového limitu na asynchronní akce. Trvalý časovače byste měli použít ve funkce orchestrátoru místo `Thread.Sleep` a `Task.Delay` (C#), nebo `setTimeout()` a `setInterval()` (JavaScript).

Vytvořte odolné časovače voláním [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) metoda [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html). Metoda vrátí úlohu, která obnoví k zadanému datu a času.

## <a name="timer-limitations"></a>Omezení časovače

Při vytváření časovače, jejíž platnost vyprší v 16:30:00, základní trvalý Framework úloha zařadí zprávu, která se zobrazí pouze v 16:30:00. Při spuštění v plánu consumption Azure Functions, zajistí zpráva nově viditelný časovače, získá aktivovaná aplikace function app na příslušný virtuální počítač.

> [!NOTE]
> * Trvalý časovače nelze trvají déle než 7 dní. z důvodu omezení ve službě Azure Storage. Na kterých pracujeme [funkce žádost o rozšíření časovače se po uplynutí 7 dnů](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * Vždy používejte [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) místo `DateTime.UtcNow` jak je znázorněno v příkladu níže při výpočtu relativní konečný termín trvalý časovače.

## <a name="usage-for-delay"></a>Využití pro zpoždění

Následující příklad ukazuje, jak používat trvalý časovače pro odkládá se provedení. V příkladu vydává fakturační oznámení každý den po dobu deseti dnů.

#### <a name="c"></a>C#

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

#### <a name="javascript"></a>JavaScript

```js
const df = require("durable-functions");
const moment = require("moment-js");

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
> Vyhněte se nekonečné smyčky ve funkcích produktu orchestrator. Informace o tom, jak bezpečně a efektivně implementovat nekonečná smyčka scénáře najdete v tématu [externí Orchestrace](durable-functions-eternal-orchestrations.md). 

## <a name="usage-for-timeout"></a>Využití časového limitu

Tento příklad ukazuje, jak používat trvalý časovače k implementaci vypršení časového limitu.

#### <a name="c"></a>C#

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

#### <a name="javascript"></a>JavaScript

```js
const df = require("durable-functions");
const moment = require("moment-js");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline);

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
> Použití `CancellationTokenSource` zrušit trvalý časovače (C#) nebo volání `cancel()` na vrácený `TimerTask` (JavaScript), pokud váš kód nebude čekat na dokončení. Trvalý Framework úlohy nedojde ke změně stavu orchestraci "Dokončit", dokud se všechny zbývající úkoly dokončena nebo zrušena.

Tento mechanismus skutečně neukončí provádění funkce probíhající aktivity. Místo toho to jednoduše umožňuje funkce orchestrátoru ignorovat výsledek a přechod na. Pokud vaši aplikaci function app používáte plán Consumption, se můžete stále účtovat pro jakékoli času a paměti používané funkce opuštěné aktivitu. Funkce běžící v plánu Consumption mají ve výchozím nastavení časového limitu 5 minut. Pokud je tento limit překročen, recykluje všechny zastavíte a zabránit vyčerpává dlouho běžící fakturační situace hostitele Azure Functions. [Funkce vypršení časového limitu je možné konfigurovat](../functions-host-json.md#functiontimeout).

Podrobnější příklad toho, jak implementovat vypršení časových limitů ve funkcích produktu orchestrator, najdete v článku [zásahem ze strany & vypršení časového limitu – ověření pomocí telefonu](durable-functions-phone-verification.md) návodu.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Zjistěte, jak vyvolat a zpracovat externí události](durable-functions-external-events.md)

