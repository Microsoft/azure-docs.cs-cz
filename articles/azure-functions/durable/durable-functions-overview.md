---
title: Přehled trvalých funkcí – Azure
description: Úvod k rozšíření Trvalé funkce pro funkce Azure.
author: cgillum
ms.topic: overview
ms.date: 08/07/2019
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: 5d454aefaba89bef9dc9009ff442fa5543dae2ef
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241343"
---
# <a name="what-are-durable-functions"></a>Co je Durable Functions?

*Durable Functions* je rozšíření [funkcí Azure,](../functions-overview.md) které umožňuje psát stavové funkce v výpočetním prostředí bez serveru. Rozšíření umožňuje definovat stavové pracovní postupy zápisem [*funkcí orchestratoru*](durable-functions-orchestrations.md) a stavových entit zápisem [*funkcí entity*](durable-functions-entities.md) pomocí programovacího modelu Azure Functions. Na pozadí rozšíření spravuje stav, kontrolní body a restartuje za vás, což vám umožní soustředit se na obchodní logiku.

## <a name="supported-languages"></a><a name="language-support"></a>Podporované jazyky

Trvalé funkce v současné době podporuje následující jazyky:

* **C#**: [předkompilované knihovny tříd](../functions-dotnet-class-library.md) i [skript jazyka C#](../functions-reference-csharp.md).
* **JavaScript:** podporováno pouze pro verzi 2.x modulu runtime Azure Functions. Vyžaduje verzi 1.7.0 rozšíření Durable Functions nebo novější verzi. 
* **F#**: předkompilované knihovny tříd a skript F#. Skript Jazyka F# je podporován pouze pro verzi 1.x modulu runtime Azure Functions.

Trvalé funkce má za cíl podporovat všechny [jazyky Azure Functions](../supported-languages.md). Nejnovější stav práce pro podporu dalších jazyků naleznete v [seznamu Problémy s trvalými funkcemi.](https://github.com/Azure/azure-functions-durable-extension/issues)

Stejně jako Funkce Azure existují šablony, které vám pomůžou vyvíjet trvalé funkce pomocí [Visual Studia 2019](durable-functions-create-first-csharp.md), [kódu Visual Studia](quickstart-js-vscode.md)a [portálu Azure](durable-functions-create-portal.md).

## <a name="application-patterns"></a>Vzory aplikací

Primární případ použití pro trvanlivé funkce zjednodušuje složité, stavové požadavky na koordinaci v aplikacích bez serveru. Následující části popisují typické vzory aplikací, které mohou využívat trvalé funkce:

* [Řetězení funkcí](#chaining)
* [Výstupní/vstupní větvení](#fan-in-out)
* [Asynchronní rozhraní API HTTP](#async-http)
* [Sledování](#monitoring)
* [Lidská interakce](#human)
* [Agregátor (stavové entity)](#aggregator)

### <a name="pattern-1-function-chaining"></a><a name="chaining"></a>vzor #1: Funkce řetězení

Ve vzoru řetězení funkce se provede posloupnost funkcí v určitém pořadí. V tomto vzoru je výstup jedné funkce použit na vstup jiné funkce.

![Schéma vzoru řetězení funkcí](./media/durable-functions-concepts/function-chaining.png)

Trvanlivé funkce můžete použít k implementaci vzor řetězení funkce stručně, jak je znázorněno v následujícím příkladu.

V tomto příkladu `F1` `F2`jsou `F3`hodnoty `F4` , , a jsou názvy dalších funkcí ve stejné aplikaci funkce. Tok řízení můžete implementovat pomocí běžných imperativních kódovacích konstrukcí. Kód se spustí shora dolů. Kód může zahrnovat existující sémantiku toku řízení jazyka, jako jsou podmínky a smyčky. Logiku zpracování chyb `try` / `catch` / `finally` můžete zahrnout do bloků.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1", null);
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

`context` Parametr můžete použít k vyvolání dalších funkcí podle názvu, parametrů průchodu a výstupu funkce vrácení. Pokaždé, když `await`volání kódu , durable functions framework kontrolní body průběh aktuální instance funkce. Pokud proces nebo virtuální počítač recykluje v polovině provádění, instance `await` funkce pokračuje z předchozího volání. Další informace naleznete v další části Vzor #2: Fan out/fan in.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    try {
        const x = yield context.df.callActivity("F1");
        const y = yield context.df.callActivity("F2", x);
        const z = yield context.df.callActivity("F3", y);
        return    yield context.df.callActivity("F4", z);
    } catch (error) {
        // Error handling or compensation goes here.
    }
});
```

Objekt můžete `context.df` použít k vyvolání dalších funkcí podle názvu, parametrů průchodu a výstupu funkce vrácení. Pokaždé, když `yield`volání kódu , durable functions framework kontrolní body průběh aktuální instance funkce. Pokud proces nebo virtuální počítač recykluje v polovině provádění, instance `yield` funkce pokračuje z předchozího volání. Další informace naleznete v další části Vzor #2: Fan out/fan in.

> [!NOTE]
> Objekt `context` v Jazyce JavaScript představuje celý [kontext funkce](../functions-reference-node.md#context-object). Přístup k kontextu trvalé `df` funkce pomocí vlastnosti v hlavním kontextu.

---

### <a name="pattern-2-fan-outfan-in"></a><a name="fan-in-out"></a>vzor #2: Ventilátor ven / ventilátor v

Ve vzoru ventilátoru a ventilátoru provedete více funkcí paralelně a pak počkáte na dokončení všech funkcí. Často některé agregační práce se provádí na výsledky, které jsou vráceny z funkcí.

![Schéma vzoru ventilátoru](./media/durable-functions-concepts/fan-out-fan-in.png)

S normálními funkcemi se můžete rozptýlit tím, že funkce odešle více zpráv do fronty. Fanning zpět je mnohem náročnější. Chcete-li ventilátor v normální funkci, zapíšete kód ke sledování, kdy fronta aktivované funkce konec a pak uložit výstupy funkce.

Rozšíření trvalé funkce zpracovává tento vzor s relativně jednoduchý kód:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1", null);
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

Práce s ventilátorem je distribuována do `F2` více instancí funkce. Práce je sledována pomocí dynamického seznamu úkolů. `Task.WhenAll`je volána čekat na dokončení všech volavané funkce. Poté jsou `F2` výstupy funkce agregovány ze seznamu dynamických úkolů a předány `F3` funkci.

Automatické vytváření kontrolních bodů, ke kterému dojde při `await` volání, `Task.WhenAll` zajišťuje, že potenciální selhání nebo restartování v polovině cesty nevyžaduje restartování již dokončené úlohy.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

Práce s ventilátorem je distribuována do `F2` více instancí funkce. Práce je sledována pomocí dynamického seznamu úkolů. `context.df.Task.all`Rozhraní API je volána čekat na dokončení všech volavých funkcí. Poté jsou `F2` výstupy funkce agregovány ze seznamu dynamických úkolů a předány `F3` funkci.

Automatické vytváření kontrolních bodů, ke kterému dojde při `yield` volání, `context.df.Task.all` zajišťuje, že potenciální selhání nebo restartování v polovině cesty nevyžaduje restartování již dokončené úlohy.

---

> [!NOTE]
> Ve výjimečných případech je možné, že selhání může dojít v okně po dokončení funkce aktivity, ale před jeho dokončení je uložen do historie orchestrace. Pokud k tomu dojde, funkce aktivity by znovu spustit od začátku po obnovení procesu.

### <a name="pattern-3-async-http-apis"></a><a name="async-http"></a>vzorové #3: Asynchronní http api

Asynchronní vzor rozhraní HTTP API řeší problém koordinace stavu dlouhotrvajících operací s externími klienty. Běžným způsobem implementace tohoto vzoru je spuštění dlouhodobé akce koncovýbod PROTOKOLU HTTP. Potom přesměrovat klienta do koncového bodu stavu, který klient dotazuje zjistit po dokončení operace.

![Diagram vzoru rozhraní HTTP API](./media/durable-functions-concepts/async-http-api.png)

Trvalé funkce poskytuje **integrovanou podporu** pro tento vzor, zjednodušení nebo dokonce odebrání kódu, který potřebujete k zápisu pro interakci s dlouhotrvající spuštění funkce. Například ukázky rychlého startu Durable Functions ([C#](durable-functions-create-first-csharp.md) a [JavaScript](quickstart-js-vscode.md)) zobrazují jednoduchý příkaz REST, který můžete použít ke spuštění nových instancí funkce orchestrator. Po spuštění instance rozšíření zpřístupňuje webhooku HTTP API, které dotaz na stav funkce orchestrator. 

Následující příklad ukazuje rest příkazy, které spustí orchestrator a dotaz jeho stav. Pro přehlednost jsou některé podrobnosti protokolu vynechány z příkladu.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

Vzhledem k tomu, že za sebou řídí stav trvanlivé funkce, nemusíte implementovat vlastní mechanismus sledování stavu.

Rozšíření trvalé funkce zpřístupňuje integrované http api, které spravují dlouhotrvající orchestrations. Tento vzor můžete také implementovat sami pomocí vlastních aktivačních událostí (například HTTP, fronty nebo Azure Event Hubs) a [vazby klienta orchestrace](durable-functions-bindings.md#orchestration-client). Můžete například použít zprávu fronty k aktivaci ukončení. Nebo můžete použít aktivační událost HTTP, která je chráněna zásadami ověřování služby Azure Active Directory namísto integrovaných rozhraní API HTTP, která používají generovaný klíč pro ověřování.

Další informace naleznete v článku [funkce protokolu HTTP,](durable-functions-http-features.md) který vysvětluje, jak můžete vystavit asynchronní, dlouhotrvající procesy přes HTTP pomocí rozšíření durable functions.

### <a name="pattern-4-monitor"></a><a name="monitoring"></a>#4 vzoru: Monitor

Vzor monitoru odkazuje na flexibilní, opakující se proces v pracovním postupu. Příkladem je dotazování, dokud nejsou splněny určité podmínky. Aktivační událost pravidelného [časovače](../functions-bindings-timer.md) můžete použít k řešení základního scénáře, jako je například úloha pravidelného čištění, ale její interval je statický a správa životnosti instancí se stává složitým. Trvalé funkce můžete použít k vytvoření flexibilních intervalů opakování, správě životnosti úloh a vytvoření více procesů monitorování z jedné orchestrace.

Příkladem vzoru monitorování je stornovat dřívější asynchronní scénář rozhraní HTTP API. Namísto vystavení koncového bodu pro externího klienta ke sledování dlouhotrvající operace, dlouhotrvající monitor spotřebovává externí koncový bod a pak čeká na změnu stavu.

![Schéma vzoru monitoru](./media/durable-functions-concepts/monitor.png)

V několika řádcích kódu můžete použít trvalé funkce k vytvoření více monitorů, které sledují libovolné koncové body. Monitory můžete ukončit provádění při splnění podmínky nebo jiné funkce můžete použít trvalé orchestrace klienta ukončit monitorování. `wait` Interval monitoru můžete změnit na základě určité podmínky (například exponenciální backoff.) 

Následující kód implementuje základní monitor:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInterval = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

---

Při přijetí požadavku je pro toto ID úlohy vytvořena nová instance orchestrace. Instance dotazování stavu, dokud je splněna podmínka a smyčky je ukončena. Trvalý časovač řídí interval dotazování. Potom lze provést další práci nebo orchestraci může skončit. Pokud `nextCheck` překročí `expiryTime`hodnotu , monitor se ukončí.

### <a name="pattern-5-human-interaction"></a><a name="human"></a>Vzor #5: Lidská interakce

Mnoho automatizovaných procesů zahrnuje nějaký druh lidské interakce. Zapojení lidí do automatizovaného procesu je složité, protože lidé nejsou tak vysoce dostupní a reagují jako cloudové služby. Automatizovaný proces může umožnit tuto interakci pomocí časových časových opovenek a logiky kompenzace.

Schvalovací proces je příkladem obchodního procesu, který zahrnuje interakci s lidmi. Schválení od manažera může být vyžadováno pro vyúčtování výdajů, které přesahuje určitou částku v dolarech. Pokud manažer neschválí vyúčtování výdajů do 72 hodin (možná manažer šel na dovolenou), spustí se proces eskalace, aby získal souhlas od někoho jiného (možná manažera manažera).

![Schéma lidského vzorce interakce](./media/durable-functions-concepts/approval.png)

Můžete implementovat vzor v tomto příkladu pomocí funkce orchestrator. Orchestrator používá [trvalý časovač](durable-functions-timers.md) požádat o schválení. Orchestrátor eskaluje, pokud dojde k časovému ukráteňkat. Orchestrátor čeká na [externí událost](durable-functions-external-events.md), například oznámení, které je generováno interakcí člověka.

Tyto příklady vytvářejí schvalovací proces k předvedení vzoru lidské interakce:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval", null);
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate", null);
        }
    }
}
```

Chcete-li vytvořit trvalý `context.CreateTimer`časovač, volejte . Oznámení obdrží společnost `context.WaitForExternalEvent`. Potom `Task.WhenAny` je volána k rozhodnutí, zda eskalovat (časový čas se stane první) nebo zpracovat schválení (schválení je přijata před časovým časem).

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

Chcete-li vytvořit trvalý `context.df.createTimer`časovač, volejte . Oznámení obdrží společnost `context.df.waitForExternalEvent`. Potom `context.df.Task.any` je volána k rozhodnutí, zda eskalovat (časový čas se stane první) nebo zpracovat schválení (schválení je přijata před časovým časem).

---

Externí klient může doručit oznámení události čekající funkci orchestrátoru pomocí [integrovaných http api](durable-functions-http-api.md#raise-event):

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

Událost může být také vyvolána pomocí trvalého klienta orchestrace z jiné funkce ve stejné aplikaci funkce:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

---

### <a name="pattern-6-aggregator-stateful-entities"></a><a name="aggregator"></a>Vzor #6: Agregátor (stavové entity)

Šestý vzor je o agregaci dat událostí za časové období do jedné adresovatelné *entity*. V tomto vzoru data, která jsou agregována může pocházet z více zdrojů, mohou být dodány v dávkách nebo mohou být rozptýleny po dlouhou dobu. Agregátor může být nutné provést akci na data události, jakmile dorazí a externí klienti mohou potřebovat dotaz na agregovaná data.

![Diagram agregátoru](./media/durable-functions-concepts/aggregator.png)

Choulostivá věc, o snaze implementovat tento vzor s normální, bezstavové funkce je, že souběžnost řízení se stává obrovskou výzvou. Nejen, že se musíte starat o více vláken, které upravují stejná data současně, ale také se musíte starat o to, abyste zajistili, že agregátor běží pouze na jednom virtuálním počítači najednou.

Trvalé [entity](durable-functions-entities.md) můžete snadno implementovat tento vzor jako jednu funkci.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            ctx.SetState(currentValue + amount);
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }
}
```

Trvalé entity lze také modelovat jako třídy v rozhraní .NET. Tento model může být užitečné, pokud je seznam operací pevné a stane se velký. Následující příklad je ekvivalentní implementace `Counter` entity pomocí .NET třídy a metody.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

---

Klienti mohou zařadit operace pro *(označované* také jako "signalizace") funkce entity pomocí [vazby klienta entity](durable-functions-bindings.md#entity-client).

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [DurableClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

> [!NOTE]
> Dynamicky generované proxy servery jsou také k dispozici v rozhraní .NET pro signalizaci entit způsobem, který je bezpečný pro daný typ. A kromě signalizace mohou klienti také dotazovat na stav funkce entity pomocí [metod bezpečného typového](durable-functions-bindings.md#entity-client-usage) připojení na vazbě klienta orchestrace.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

---

Entity funkce jsou k dispozici v [trvalé funkce 2.0](durable-functions-versions.md) a vyšší.

## <a name="the-technology"></a>Technologie

Na pozadí rozšíření trvalé funkce je postavenna nad [rámec tružižních úloh](https://github.com/Azure/durabletask), open source knihovny na GitHubu, který se používá k vytváření pracovních postupů v kódu. Stejně jako Azure Functions je vývoj Azure WebJobs bez serveru, durable functions je vývoj architektury durable task framework bez serveru. Microsoft a další organizace používají rámec durable task framework značně k automatizaci kritických procesů. Je to přirozené přizpůsobení prostředí Azure Functions bez serveru.

## <a name="code-constraints"></a>Omezení kódu

Chcete-li poskytnout spolehlivé a dlouhotrvající záruky provádění, funkce orchestrator mají sadu pravidel kódování, které musí být dodrženy. Další informace naleznete v článku [omezení kódu funkce Orchestrator.](durable-functions-code-constraints.md)

## <a name="billing"></a>Fakturace

Trvalé funkce se účtují stejně jako funkce Azure. Další informace najdete v tématu [Azure Functions pricing](https://azure.microsoft.com/pricing/details/functions/). Při provádění funkcí orchestrator v [plánu Azure](../functions-scale.md#consumption-plan)functions consumption , existují některé chování fakturace být vědomi. Další informace o těchto chování, naleznete v článku [trvanlivé funkce fakturace.](durable-functions-billing.md)

## <a name="jump-right-in"></a>Skoč přímo dovnitř.

S trvalými funkcemi můžete začít za méně než 10 minut vyplněním jednoho z těchto kurzů rychlého startu pro konkrétní jazyk:

* [C# pomocí Visual Studia 2019](durable-functions-create-first-csharp.md)
* [JavaScript pomocí kódu visual studia](quickstart-js-vscode.md)

V obou rychlých startech místně vytvoříte a otestujete trvalou funkci "hello world". Kód funkce potom publikujete do Azure. Funkce, kterou vytvoříte orchestrety a řetězy společně volá další funkce.

## <a name="learn-more"></a>Další informace

Následující video upozorňuje na výhody trvalých funkcí:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Podrobnější diskuse o trvalých funkcích a základní technologii najdete v následujícím videu (je zaměřeno na rozhraní .NET, ale koncepty platí i pro další podporované jazyky):

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Protože durable functions je rozšířené rozšíření pro [funkce Azure](../functions-overview.md), není vhodné pro všechny aplikace. Porovnání s jinými technologiemi orchestrace Azure najdete [v tématu Porovnání funkcí Azure a Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Typy a funkce funkcí durable function](durable-functions-types-features-overview.md)
