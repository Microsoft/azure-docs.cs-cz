---
title: Přehled Durable Functions – Azure
description: Úvod do rozšíření Durable Functions pro Azure Functions
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 08/07/2019
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: a917a823d47d6a072cf5a3ee5d636b432913df9a
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299445"
---
# <a name="what-are-durable-functions"></a>Co jsou Durable Functions?

*Durable Functions* je rozšíření [Azure Functions](../functions-overview.md) , které umožňuje psát stavové funkce v prostředí COMPUTE bez serveru. Toto rozšíření umožňuje definovat stavové pracovní postupy napsáním funkcí a stavových entit nástroje [*Orchestrator*](durable-functions-orchestrations.md) napsáním [*funkcí*](durable-functions-entities.md) jazyka pomocí programovacího modelu Azure Functions. Na pozadí spravuje rozšíření stav, kontrolní body a restarty pro vás, což vám umožní soustředit se na obchodní logiku.

## <a name="language-support"></a>Podporované jazyky

Durable Functions aktuálně podporuje následující jazyky:

* **C#** : [předkompilované knihovny tříd](../functions-dotnet-class-library.md) a [ C# skript](../functions-reference-csharp.md).
* **F#** : předkompilované knihovny tříd a F# skript. F#skript je podporován pouze pro verzi 1. x modulu Azure Functions runtime.
* **JavaScript**: podporuje se jenom pro verzi 2. x Azure Functions runtime. Vyžaduje verzi 1.7.0 rozšíření Durable Functions nebo novější verzi. 

Durable Functions má za cíl podporu všech [Azure Functionsch jazyků](../supported-languages.md). Nejnovější stav práce pro podporu dalších jazyků najdete v [seznamu problémů s Durable Functions](https://github.com/Azure/azure-functions-durable-extension/issues) .

Stejně jako Azure Functions, jsou šablony, které vám pomohou vyvíjet Durable Functions pomocí sady [Visual Studio 2019](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md)a [Azure Portal](durable-functions-create-portal.md).

## <a name="application-patterns"></a>Vzory aplikací

Primární případ použití pro Durable Functions zjednodušuje komplexní požadavky na stavovou spolupráci v aplikacích bez serveru. Následující části popisují typické vzory aplikací, které můžou využívat Durable Functions:

* [Řetězení funkcí](#chaining)
* [Ventilátor/ventilátor – v](#fan-in-out)
* [Asynchronní rozhraní HTTP API](#async-http)
* [Monitorování](#monitoring)
* [Lidská interakce](#human)
* [Agregovan](#aggregator)

### <a name="chaining"></a>#1 vzoru: Řetězení funkcí

Ve vzoru zřetězení funkcí se posloupnost funkcí provádí v určitém pořadí. V tomto modelu je výstup jedné funkce aplikován na vstup jiné funkce.

![Diagram vzoru řetězení funkcí](./media/durable-functions-concepts/function-chaining.png)

Můžete použít Durable Functions k implementaci vzor řetězení funkcí stručně, jak je znázorněno v následujícím příkladu:

#### <a name="c"></a>C#

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return    yield context.df.callActivity("F4", z);
});
```

`F1`V tomto příkladu hodnoty `F2` `F3`,, a `F4` jsou názvy dalších funkcí v aplikaci Function App. Tok řízení lze implementovat pomocí normálních imperativních konstrukcí kódování. Kód se spustí shora dolů. Kód může zahrnovat stávající sémantiku toku řízení jazyka, jako jsou podmínky a smyčky. `try` Do blokůmůžete`finally` zahrnout logiku zpracování chyb. / `catch` /

Můžete použít `context` parametr [DurableOrchestrationContext] \(.NET\) a `context.df` Object (JavaScript) k vyvolání dalších funkcí podle názvu, průchodu Parameters a návratového výstupu funkce. Pokaždé, když kód `await` voláC#() `yield` nebo (JavaScript), Durable Functions Framework kontrolní body průběh aktuální instance funkce. Pokud se proces nebo virtuální počítač recykluje v průběhu provádění, instance funkce pokračuje z předchozího `await` nebo `yield` volání. Další informace najdete v další části #2 vzorových informací: Ventilátor nebo ventilátor v.

> [!NOTE]
> Objekt v JavaScriptu představuje celý [kontext funkce](../functions-reference-node.md#context-object), nikoli jenom parametr [DurableOrchestrationContext]. `context`

### <a name="fan-in-out"></a>#2 vzoru: Ventilátor nebo ventilátor v

Ve vzorku se v ventilátoru nebo ventilátoru spouští více funkcí paralelně a pak čekají na dokončení všech funkcí. U výsledků, které se vrátí z funkcí, se často provádí některá agregační práce.

![Diagram vzoru ventilátoru a ventilátoru](./media/durable-functions-concepts/fan-out-fan-in.png)

Díky normálním funkcím se můžete dostat do fronty tak, že funkci odešlete více zpráv. Fanning zpět je mnohem náročnější. Pro ventilátor v nástroji normální funkce napíšete kód, který bude sledován při ukončení funkcí aktivovaných frontou, a následné uložení výstupů funkcí.

Rozšíření Durable Functions zpracovává tento vzor s poměrně jednoduchým kódem:

#### <a name="c"></a>C#

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

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

Práce s ventilátorem je distribuována do více instancí `F2` funkce. Práce je sledována pomocí dynamického seznamu úkolů. Rozhraní .NET `Task.WhenAll` API nebo JavaScript `context.df.Task.all` API se zavolá, aby se čekalo na dokončení všech volaných funkcí. Výstupy `F3` funkcí pak jsou agregovány z dynamického seznamu úkolů a předány funkci. `F2`

Automatické vytváření kontrolních bodů, ke kterým dochází `await` při `yield` volání `Task.WhenAll` `context.df.Task.all` nebo, zajistí, že potenciální funkce pro zhroucení nebo restartování počítače nevyžadují restartování již dokončené úlohy.

> [!NOTE]
> Ve výjimečných případech je možné, že dojde k chybě v okně po dokončení funkce aktivity, ale před tím, než se její dokončení uloží do historie orchestrace. Pokud k tomu dojde, funkce Activity by se po obnovení procesu znovu spouštěla od začátku.

### <a name="async-http"></a>#3 vzoru: Asynchronní rozhraní HTTP API

Vzor asynchronního protokolu HTTP API řeší problém koordinace stavu dlouhotrvajících operací s externími klienty. Běžný způsob implementace tohoto modelu je, že koncový bod HTTP spustí dlouhotrvající akci. Pak přesměrujte klienta na koncový bod stavu, který se klient dotazuje, když se operace dokončí.

![Diagram vzoru HTTP API](./media/durable-functions-concepts/async-http-api.png)

Durable Functions poskytuje **integrovanou podporu** pro tento model, což zjednodušuje nebo odebírá kód, který je potřeba napsat pro interakci s dlouhotrvajícím spouštěním funkcí. Například ukázky pro rychlý Start Durable Functions ([C#](durable-functions-create-first-csharp.md) a [JavaScript](quickstart-js-vscode.md)) znázorňují jednoduchý příkaz REST, který můžete použít ke spuštění nových instancí funkcí nástroje Orchestrator. Po spuštění instance rozšíření zpřístupňuje rozhraní API HTTP Webhooku, která se dotazují na stav funkce nástroje Orchestrator. 

Následující příklad ukazuje příkazy REST, které spouštějí nástroj Orchestrator a dotazují svůj stav. Pro přehlednost jsou některé podrobnosti protokolu z příkladu vynechány.

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

Vzhledem k tomu, že modul runtime Durable Functions spravuje stav za vás, nemusíte implementovat vlastní mechanismus pro sledování stavu.

Rozšíření Durable Functions zpřístupňuje Vestavěná rozhraní API HTTP, která spravují dlouhotrvající orchestrace. Tento vzor můžete případně implementovat sami pomocí triggerů funkcí (například HTTP, front nebo Azure Event Hubs) a [vazby klienta Orchestration](durable-functions-bindings.md#orchestration-client). Můžete například použít zprávu fronty k aktivaci ukončení. Nebo můžete použít Trigger HTTP, který je chráněný pomocí Azure Active Directory zásad ověřování místo integrovaných rozhraní API HTTP, která pro ověřování používají vygenerovaný klíč.

Další informace najdete v článku [funkce protokolu HTTP](durable-functions-http-features.md) , který vysvětluje, jak můžete vystavit asynchronní a dlouhotrvající procesy přes protokol HTTP pomocí rozšíření Durable Functions.

### <a name="monitoring"></a>#4 vzoru: Monitorování

Model monitorování odkazuje na flexibilní a opakovaný proces v pracovním postupu. Příkladem je cyklické dotazování, dokud nebudou splněny určité podmínky. Pomocí pravidelné [aktivační procedury časovače](../functions-bindings-timer.md) můžete vyřešit základní scénář, například úlohu pravidelného čištění, ale jeho interval je statický a Správa životnosti instance je složitá. Pomocí Durable Functions můžete vytvářet flexibilní intervaly opakování, spravovat životnost úloh a vytvářet více procesů monitorování z jedné orchestrace.

Příkladem vzoru monitorování je vrácení dřívějšího scénáře asynchronního protokolu HTTP API. Místo vystavení koncového bodu pro externího klienta za účelem monitorování dlouhotrvající operace používá dlouhotrvající monitor externí koncový bod a poté čeká na změnu stavu.

![Diagram modelu monitoru](./media/durable-functions-concepts/monitor.png)

V několika řádcích kódu můžete pomocí Durable Functions vytvořit více monitorů, které sledují libovolné koncové body. Monitory mohou ukončit provádění, pokud je splněna podmínka, nebo může [DurableOrchestrationClient](durable-functions-instance-management.md) ukončit monitorování. Můžete změnit `wait` interval monitorování na základě konkrétní podmínky (například exponenciální omezení rychlosti.) 

Následující kód implementuje základní monitor:

#### <a name="c"></a>C#

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
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

Po přijetí žádosti se pro ID úlohy vytvoří nová instance Orchestration. Instance se dotazuje na stav, dokud není splněna podmínka a dojde k ukončení smyčky. Interval cyklického dotazování řídí trvalý časovač. Pak je možné provést více práce, nebo orchestrace může skončit. Když rozhraní `context.df.currentUtcDateTime` `expiryTime` (.NET) nebo (JavaScript) překračuje hodnotu, monitor skončí. `context.CurrentUtcDateTime`

### <a name="human"></a>#5 vzoru: Lidská interakce

Řada automatizovaných procesů zahrnuje nějaký druh lidské interakce. Zapojení lidí do automatizovaného procesu je obtížné, protože lidé nejsou jako vysoce dostupné a reagují jako cloudové služby. Automatizovaný proces může pro tuto interakci umožňovat pomocí časových limitů a logiky kompenzace.

Schvalovací proces je příkladem obchodního procesu, který zahrnuje lidskou interakci. Pro sestavu výdajů, která překračuje určitou částku dolaru, může být vyžadováno schválení správcem. Pokud správce neschválí zprávu o výdajích do 72 hodin (možná správce přešel na dovolenou), proces eskalace se zahájí a získá schválení od někoho jiného (možná správce manažera).

![Diagram vzoru lidské interakce](./media/durable-functions-concepts/approval.png)

Vzor v tomto příkladu můžete implementovat pomocí funkce Orchestrator. Nástroj Orchestrator používá k žádosti o schválení [trvalý časovač](durable-functions-timers.md) . Pokud dojde k vypršení časového limitu, produkt Orchestrator postoupí. Orchestrator čeká na [externí událost](durable-functions-external-events.md), jako je například oznámení, které vygenerovala lidská interakce.

Tyto příklady vytvoří proces schvalování, který předvádí vzor lidské interakce:

#### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

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

Chcete-li vytvořit trvalý časovač, `context.CreateTimer` volejte (.NET) `context.df.createTimer` nebo (JavaScript). Oznámení přijímá `context.WaitForExternalEvent` (.NET) nebo `context.df.waitForExternalEvent` (JavaScript). Pak (.NET) nebo `context.df.Task.any` (JavaScript) se zavolá, aby se rozhodlo, jestli se má eskalovat (časový limit nastane jako první), nebo jestli se má schválit schválení (schválení se přijme před časovým limitem). `Task.WhenAny`

Externí klient může doručovat oznámení události do čekající funkce Orchestrator pomocí [integrovaných rozhraní HTTP API](durable-functions-http-api.md#raise-event) nebo pomocí rozhraní API [DurableOrchestrationClient. RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) z jiné funkce:

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

### <a name="aggregator"></a>#6 vzoru: Agregátor (Preview)

Šestým vzorem je informace o agregaci dat událostí v časovém intervalu až na jednu, adresovatelnou *entitu*. V tomto modelu mohou být shromážděná data z více zdrojů, mohou být dodávána v dávkách nebo mohou být rozmístěna za dlouhou dobu. Agregátor může potřebovat provést akci s daty události při jejich doručení a externí klienti budou potřebovat dotaz na agregovaná data.

![Agregátorový diagram](./media/durable-functions-concepts/aggregator.png)

Důvodem, proč se pokusit o implementaci tohoto modelu s normálními a bezstavovým funkcemi, je, že řízení souběžnosti se stává obrovským problémem. Nemusíte si dělat starosti s více vlákny, které mění stejná data současně, musíte se také starat o to, že agregátor běží jenom na jednom virtuálním počítači.

Pomocí [funkce trvalé entity](durable-functions-preview.md#entity-functions)může jeden implementovat tento vzor snadno jako jednu funkci.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += amount;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

Odolné entity lze také modelovat jako třídy .NET. Tento model může být užitečný, pokud je seznam operací pevný a bude velký. Následující příklad je ekvivalentní implementace `Counter` entity pomocí tříd a metod .NET.

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

Klienti mohou zařadit *operace* do fronty (označované také jako "signalizace") entity funkce pomocí [vazby klienta entit](durable-functions-bindings.md#entity-client).

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [OrchestrationClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

Dynamicky generované proxy servery jsou také k dispozici pro signalizaci entit v typově bezpečném způsobu. Kromě signalizace se klienti můžou také dotazovat na stav funkce entity pomocí [typově bezpečných metod](durable-functions-bindings.md#entity-client-usage) na vazbu klienta Orchestration.

> [!NOTE]
> Funkce entit jsou aktuálně k dispozici pouze v rozhraní .NET jako součást [Durable Functions 2,0 Preview](durable-functions-preview.md).

## <a name="the-technology"></a>Technologie

Na pozadí je rozšíření Durable Functions postaveno nad [trvalým prostředím úloh](https://github.com/Azure/durabletask), open source knihovny na GitHubu, která se používá k sestavování pracovních postupů v kódu. Podobně jako Azure Functions je vývoj Azure WebJobs bez serveru, Durable Functions je vývojem trvalého prostředí úloh bez serveru. Microsoft a další organizace často využívají trvalý rámec úloh pro automatizaci nejdůležitějších procesů. Pro prostředí Azure Functions bez serveru je to přirozené.

## <a name="code-constraints"></a>Omezení kódu

Za účelem zajištění spolehlivých a dlouhotrvajících záruk spouštění mají funkce nástroje Orchestrator sadu pravidel pro kódování, která musí být dodržena. Další informace naleznete v článku o [omezeních kódu funkce nástroje Orchestrator](durable-functions-code-constraints.md) .

## <a name="billing"></a>Fakturace

Durable Functions se účtují stejně jako Azure Functions. Další informace najdete v tématu [ceny Azure Functions](https://azure.microsoft.com/pricing/details/functions/). Při provádění funkcí Orchestrator v plánu Azure Functions [spotřeby](../functions-scale.md#consumption-plan)existují některá nastavení fakturace, o kterých je potřeba vědět. Další informace o tomto chování najdete v článku o [fakturaci Durable Functions](durable-functions-billing.md) .

## <a name="jump-right-in"></a>Skok přímo v

Můžete začít s Durable Functions za 10 minut, a to provedením jednoho z těchto kurzů pro rychlý Start pro konkrétní jazyk:

* [C#pomocí sady Visual Studio 2019](durable-functions-create-first-csharp.md)
* [JavaScript pomocí Visual Studio Code](quickstart-js-vscode.md)

V obou rychlých startech můžete místně vytvořit a otestovat funkci "Hello World". Kód funkce potom publikujete do Azure. Funkce, kterou vytváříte, orchestruje a řetězí volání dalších funkcí.

## <a name="learn-more"></a>Víc se uč

Následující video zvýrazní výhody Durable Functions:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Podrobnější diskuzi o Durable Functions a základní technologii najdete v následujícím videu (zaměřuje se na .NET, ale koncepty platí i pro jiné podporované jazyky):

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Vzhledem k tomu, že Durable Functions je pokročilá rozšíření pro [Azure Functions](../functions-overview.md), není vhodné pro všechny aplikace. Porovnání s dalšími technologiemi orchestrace Azure najdete v tématu [porovnání Azure functions a Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Durable Functions typy a funkce funkcí](durable-functions-types-features-overview.md)
