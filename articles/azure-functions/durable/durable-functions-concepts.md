---
title: Způsoby Durable Functions a technické koncepce v Azure Functions
description: Přečtěte si, jak rozšíření Durable Functions v Azure Functions poskytuje výhody při provádění stavového kódu v cloudu.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: fe3000181ed02e3640e7af48fa492f4a7db55191
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734570"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Vzory Durable Functions a technické koncepce (Azure Functions)

Durable Functions je rozšíření [Azure Functions](../functions-overview.md) a [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md). Můžete použít Durable Functions k zápisu stavových funkcí v prostředí bez serveru. Toto rozšíření za vás spravuje stav, kontrolní body a restartování. 

Tento článek obsahuje podrobné informace o chování rozšíření Durable Functions pro Azure Functions a běžné vzory implementace. Tyto informace vám pomohou určit, jak používat Durable Functions k řešení problémů s vývojem.

> [!NOTE]
> Durable Functions je pokročilá rozšíření pro Azure Functions, která nejsou vhodná pro všechny aplikace. V tomto článku se předpokládá, že máte silnou znalost konceptů v [Azure Functions](../functions-overview.md) a problémy, které se týkají vývoje aplikací bez serveru.

## <a name="patterns"></a>Vzory

Tato část popisuje některé běžné aplikační vzory, kde Durable Functions můžou být užitečné.

### <a name="chaining"></a>#1 vzoru: Řetězení funkcí

Ve vzoru zřetězení funkcí se posloupnost funkcí provádí v určitém pořadí. V tomto modelu je výstup jedné funkce aplikován na vstup jiné funkce.

![Diagram vzoru řetězení funkcí](./media/durable-functions-concepts/function-chaining.png)

Můžete použít Durable Functions k implementaci vzor řetězení funkcí stručně, jak je znázorněno v následujícím příkladu:

#### <a name="precompiled-c"></a>PředkompilovanéC#

```csharp
public static async Task<object> Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
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

#### <a name="c-script"></a>Skript jazyka C#

```csharp
public static async Task<object> Run(DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
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

> [!NOTE]
> Existuje drobný rozdíl mezi zápisem předkompilované trvalé funkce v C# a zápisem předkompilované trvalé funkce ve C# skriptu. C# V předkompilovaných funkcích musí být odolné parametry dekorované příslušnými atributy. Příkladem je `[OrchestrationTrigger]` atribut `DurableOrchestrationContext` pro parametr. C# V předkompilovaných trvalých funkcích, pokud parametry nejsou správně upraveny, modul runtime nemůže vložit proměnné do funkce a dojde k chybě. Další příklady najdete v [ukázkách Azure-Functions-odolné rozšíření na GitHubu](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples).

#### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

`F1`V tomto příkladu hodnoty `F2` `F3`,, a `F4` jsou názvy dalších funkcí v aplikaci Function App. Tok řízení lze implementovat pomocí normálních imperativních konstrukcí kódování. Kód se spustí shora dolů. Kód může zahrnovat stávající sémantiku toku řízení jazyka, jako jsou podmínky a smyčky. `try` Do blokůmůžete`finally` zahrnout logiku zpracování chyb. / `catch` /

Můžete použít `context` parametr [DurableOrchestrationContext] \(.NET\) a objekt(JavaScript)kvyvolánídalšíchfunkcípodlenázvu,PassParametersavracetvýstupfunkce.`context.df` Pokaždé, když kód `await` voláC#() `yield` nebo (JavaScript), Durable Functions Framework kontrolní body průběh aktuální instance funkce. Pokud se proces nebo virtuální počítač recykluje v průběhu provádění, instance funkce pokračuje z předchozího `await` nebo `yield` volání. Další informace najdete v další části #2 vzorových informací: Ventilátor nebo ventilátor v.

> [!NOTE]
> Objekt v JavaScriptu představuje celý [kontext funkce](../functions-reference-node.md#context-object), nikoli jenom parametr DurableOrchestrationContext. [] `context`

### <a name="fan-in-out"></a>#2 vzoru: Ventilátor nebo ventilátor v

Ve vzorku se v ventilátoru nebo ventilátoru spouští více funkcí paralelně a pak čekají na dokončení všech funkcí. U výsledků, které se vrátí z funkcí, se často provádí některá agregační práce.

![Diagram vzoru ventilátoru a ventilátoru](./media/durable-functions-concepts/fan-out-fan-in.png)

Díky normálním funkcím se můžete dostat do fronty tak, že funkci odešlete více zpráv. Fanning zpět je mnohem náročnější. Pro ventilátor v nástroji normální funkce napíšete kód, který bude sledován při ukončení funkcí aktivovaných frontou, a následné uložení výstupů funkcí. 

Rozšíření Durable Functions zpracovává tento vzor s poměrně jednoduchým kódem:

#### <a name="precompiled-c"></a>PředkompilovanéC#

```csharp
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
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

#### <a name="c-script"></a>Skript jazyka C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
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

### <a name="async-http"></a>#3 vzoru: Asynchronní rozhraní HTTP API

Vzor asynchronních rozhraní HTTP API řeší problém koordinace stavu dlouhotrvajících operací s externími klienty. Běžný způsob, jak tento model implementovat, je, že volání HTTP aktivuje dlouhotrvající akci. Pak přesměrujte klienta na koncový bod stavu, který se klient dotazuje, když se operace dokončí.

![Diagram vzoru HTTP API](./media/durable-functions-concepts/async-http-api.png)

Durable Functions poskytuje integrovaná rozhraní API, která zjednodušují psaní kódu pro interakci s dlouhodobě běžícími spouštěními funkcí. Ukázky Durable Functions rychlý Start ([C#](durable-functions-create-first-csharp.md) a [JavaScript](quickstart-js-vscode.md)) znázorňují jednoduchý příkaz REST, který můžete použít ke spuštění nových instancí funkcí nástroje Orchestrator. Po spuštění instance rozšíření zpřístupňuje rozhraní API HTTP Webhooku, která se dotazují na stav funkce nástroje Orchestrator. 

Následující příklad ukazuje příkazy REST, které spouštějí nástroj Orchestrator a dotazují svůj stav. Pro přehlednost jsou některé podrobnosti z příkladu vynechány.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

Vzhledem k tomu, že modul runtime Durable Functions spravuje stav, nemusíte implementovat vlastní mechanismus pro sledování stavu.

Rozšíření Durable Functions obsahuje integrované Webhooky, které spravují dlouhotrvající orchestrace. Tento vzor můžete implementovat sami pomocí triggerů funkcí (například http, front nebo Azure Event Hubs) a `orchestrationClient` vazby. Můžete například použít zprávu fronty k aktivaci ukončení. Nebo můžete použít Trigger HTTP, který je chráněný pomocí Azure Active Directory zásad ověřování místo vestavěných webhooků, které pro ověřování používají vygenerovaný klíč.

Tady je několik příkladů použití vzoru HTTP API:

#### <a name="precompiled-c"></a>PředkompilovanéC#

```csharp
// An HTTP-triggered function starts a new orchestrator function instance.
[FunctionName("StartNewOrchestration")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // The function name comes from the request URL.
    // The function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="c-script"></a>Skript jazyka C#

```csharp
// An HTTP-triggered function starts a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // The function name comes from the request URL.
    // The function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

```javascript
// An HTTP-triggered function starts a new orchestrator function instance.
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);

    // The function name comes from the request URL.
    // The function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    return client.createCheckStatusResponse(req, instanceId);
};
```

V rozhraní .NET je parametr [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` hodnotou z `orchestrationClient` výstupní vazby, která je součástí rozšíření Durable Functions. V jazyce JavaScript je tento objekt vrácen voláním `df.getClient(context)`metody. Tyto objekty poskytují metody, které můžete použít ke spuštění, odeslání událostí do, ukončení a dotazování na nové nebo existující instance funkcí Orchestrator.

V předchozích příkladech přebírá `functionName` funkce aktivované protokolem HTTP hodnotu z adresy URL příchozí a předává hodnotu [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). Rozhraní API vazby [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) pak vrátí odpověď `Location` obsahující hlavičku a další informace o instanci. Později můžete informace použít k vyhledání stavu spuštěné instance nebo ukončení instance.

### <a name="monitoring"></a>#4 vzoru: Monitorování

Model monitorování odkazuje na flexibilní a opakovaný proces v pracovním postupu. Příkladem je cyklické dotazování, dokud nebudou splněny určité podmínky. Pomocí pravidelné [aktivační procedury časovače](../functions-bindings-timer.md) můžete vyřešit základní scénář, například úlohu pravidelného čištění, ale jeho interval je statický a Správa životnosti instance je složitá. Pomocí Durable Functions můžete vytvářet flexibilní intervaly opakování, spravovat životnost úloh a vytvářet více procesů monitorování z jedné orchestrace.

Příkladem vzoru monitorování je vrácení dřívějšího scénáře asynchronního protokolu HTTP API. Místo vystavení koncového bodu pro externího klienta za účelem monitorování dlouhotrvající operace používá dlouhotrvající monitor externí koncový bod a poté čeká na změnu stavu.

![Diagram modelu monitoru](./media/durable-functions-concepts/monitor.png)

V několika řádcích kódu můžete pomocí Durable Functions vytvořit více monitorů, které sledují libovolné koncové body. Monitory mohou ukončit provádění, pokud je splněna podmínka, nebo může [DurableOrchestrationClient](durable-functions-instance-management.md) ukončit monitorování. Můžete změnit `wait` interval monitorování na základě konkrétní podmínky (například exponenciální omezení rychlosti.) 

Následující kód implementuje základní monitor:

#### <a name="precompiled-c"></a>PředkompilovanéC#

```csharp
[FunctionName("Orchestrator")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
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

#### <a name="c-script"></a>Skript jazyka C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
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

Řada automatizovaných procesů zahrnuje nějaký druh lidské interakce. Zapojení lidí do automatizovaného procesu je obtížné, protože lidé nejsou jako vysoce dostupné a reagují jako cloudové služby. Automatizovaný proces to může využít při použití časových limitů a logiky kompenzace.

Schvalovací proces je příkladem obchodního procesu, který zahrnuje lidskou interakci. Pro sestavu výdajů, která překračuje určitou částku dolaru, může být vyžadováno schválení správcem. Pokud správce neschválí zprávu o výdajích do 72 hodin (možná správce přešel na dovolenou), proces eskalace se zahájí a získá schválení od někoho jiného (možná správce manažera).

![Diagram vzoru lidské interakce](./media/durable-functions-concepts/approval.png)

Vzor v tomto příkladu můžete implementovat pomocí funkce Orchestrator. Nástroj Orchestrator používá k žádosti o schválení [trvalý časovač](durable-functions-timers.md) . Pokud dojde k vypršení časového limitu, produkt Orchestrator postoupí. Orchestrator čeká na [externí událost](durable-functions-external-events.md), jako je například oznámení, které vygenerovala lidská interakce.

Tyto příklady vytvoří proces schvalování, který předvádí vzor lidské interakce:

#### <a name="precompiled-c"></a>PředkompilovanéC#

```csharp
[FunctionName("Orchestrator")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
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
            await context.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="c-script"></a>Skript jazyka C#

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
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
            await context.CallActivityAsync("Escalate");
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

#### <a name="precompiled-c"></a>PředkompilovanéC#

```csharp
public static async Task Run(
  [HttpTrigger] string instanceId,
  [OrchestrationClient] DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

#### <a name="c-script"></a>C#Pravidel

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

#### <a name="javascript"></a>JavaScript

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
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
            currentValue += operand;
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

Odolné entity lze také modelovat jako třídy .NET. To může být užitečné v případě, že se seznam operací bude velký a je většinou statický. Následující příklad je ekvivalentní implementace `Counter` entity pomocí tříd a metod .NET.

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

Klienti můžou zařadit *operace* do fronty pro (označuje se také jako "signalizace") funkce `orchestrationClient` entity pomocí vazby.

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

Dynamicky generované proxy servery jsou také k dispozici pro signalizaci entit v typově bezpečném způsobu. Kromě signalizace se klienti můžou také dotazovat na stav funkce entity pomocí metod na `orchestrationClient` vazbě.

> [!NOTE]
> Funkce entit jsou aktuálně k dispozici pouze ve [verzi Durable Functions 2,0 Preview](durable-functions-preview.md).

## <a name="the-technology"></a>Technologie

Na pozadí je rozšíření Durable Functions postaveno nad [trvalým prostředím úloh](https://github.com/Azure/durabletask), open source knihovny na GitHubu, která se používá k sestavování trvalých orchestrací úloh. Podobně jako Azure Functions je vývoj Azure WebJobs bez serveru, Durable Functions je vývojem trvalého prostředí úloh bez serveru. Microsoft a další organizace často využívají trvalý rámec úloh pro automatizaci nejdůležitějších procesů. Pro prostředí Azure Functions bez serveru je to přirozené.

### <a name="event-sourcing-checkpointing-and-replay"></a>Zdroje událostí, vytváření kontrolních bodů a přehrávání

Služba Orchestrator Functions spolehlivě udržuje jejich stav spouštění pomocí vzoru návrhu pro vytváření [událostí](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) . Místo přímého ukládání aktuálního stavu orchestrace používá rozšíření Durable Functions k zaznamenání celé řady akcí, které orchestrace funkce provede, úložiště pouze pro připojení. Úložiště jen pro připojení má mnoho výhod v porovnání s "výpisem" celého běhového stavu. Mezi výhody patří vyšší výkon, škálovatelnost a rychlost odezvy. Získáte také konečnou konzistenci pro transakční data a úplné záznamy a historii auditu. Záznamy auditu podporují spolehlivé kompenzační akce.

Durable Functions používá zdrojové události transparentně. Na pozadí má `await` operátor (C#) nebo `yield` (JavaScript) ve funkci Orchestrator kontrolu nad vláknem nástroje Orchestrator zpátky do trvalého dispečeru rozhraní úloh. Dispečer pak potvrdí všechny nové akce, které naplánovala funkce Orchestrator (například volání jedné nebo více podřízených funkcí nebo naplánování trvalého časovače) do úložiště. Transparentní akce potvrzení připojí k historii spuštění instance Orchestration. Historie je uložená v tabulce úložiště. Akce potvrzení pak přidá zprávy do fronty, aby naplánovala skutečnou práci. V tomto okamžiku může být funkce Orchestrator uvolněna z paměti. 

Fakturace funkce Orchestrator se zastaví, pokud používáte plán Azure Functions spotřeby. Pokud je k dispozici více práce, je funkce restartována a její stav je znovu vytvořen.

Když je funkce orchestrace předána více práce (například přijetí zprávy odpovědi nebo vypršení platnosti časovače), nástroj Orchestrator probudí a znovu spustí celou funkci od začátku až po opětovné sestavení místního stavu. 

Při opakovaném přehrání, pokud se kód pokusí zavolat funkci (nebo provést jakoukoli jinou asynchronní práci), bude architektura trvalého úkolu vyzvat k historii spuštění aktuální orchestrace. Pokud zjistí, že [funkce Activity](durable-functions-types-features-overview.md#activity-functions) již byla provedena a způsobila výsledek, přehraje výsledek této funkce a kód nástroje Orchestrator pokračuje v běhu. Opětovné přehrání pokračuje, dokud nebude kód funkce dokončen nebo dokud nebude naplánována nová asynchronní práce.

### <a name="orchestrator-code-constraints"></a>Omezení kódu Orchestrator

Chování při opětovném přehrání kódu Orchestrator vytvoří omezení pro typ kódu, který můžete zapsat do funkce Orchestrator. Například kód Orchestrator musí být deterministický, protože bude opakovaně přehrán vícekrát a musí pokaždé vydávat stejný výsledek. Úplný seznam omezení najdete v tématu věnovaném [omezením kódu Orchestrator](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints).

## <a name="monitoring-and-diagnostics"></a>Monitorování a diagnostika

Rozšíření Durable Functions automaticky posílá data strukturovaného sledování do [Application Insights](../functions-monitoring.md) , pokud nastavíte aplikaci Function App pomocí klíče instrumentace Azure Application Insights. Pomocí sledovacích dat můžete monitorovat akce a průběh orchestrace.

Tady je příklad toho, co Durable Functions události sledování vypadají jako na portálu Application Insights, když používáte [Application Insights Analytics](../../application-insights/app-insights-analytics.md):

![Application Insights výsledků dotazu](./media/durable-functions-concepts/app-insights-1.png)

Užitečná strukturovaná data můžete najít v `customDimensions` poli každé položky protokolu. Tady je příklad položky, která je plně rozbalená:

![Pole customDimensions v dotazu Application Insights](./media/durable-functions-concepts/app-insights-2.png)

Kvůli chování opětovného přehrání dispečera rozhraní pro trvalé úlohy můžete očekávat, že se položky protokolu pro přehrání akcí zobrazí jako nadbytečné. Nadbytečné položky protokolu vám můžou porozumět chování základního motoru při opakovaném přehrávání. V [tomto článku se](durable-functions-diagnostics.md) dozvíte o ukázkových dotazech odfiltrování protokolů opětovného přehrání, abyste viděli jenom protokoly v reálném čase.

## <a name="storage-and-scalability"></a>Úložiště a škálovatelnost

Rozšíření Durable Functions používá fronty, tabulky a objekty BLOB v Azure Storage k uchování stavu historie provádění a triggeru provádění funkce. Pro aplikaci Function App můžete použít výchozí účet úložiště, nebo můžete nakonfigurovat samostatný účet úložiště. Je možné, že budete chtít vytvořit samostatný účet založený na omezeních propustnosti úložiště. Kód Orchestrator, který napíšete, nekomunikuje s entitami v těchto účtech úložiště. Trvalé rozhraní úlohy spravuje entity přímo jako podrobnosti implementace.

Nástroj Orchestrator Functions Activity Functions funkce plánu a přijímá jejich odpovědi prostřednictvím interních zpráv fronty. Když aplikace Function App běží v plánu Azure Functions spotřebu, [kontroler Azure Functions škálování](../functions-scale.md#how-the-consumption-and-premium-plans-work) tyto fronty monitoruje. V případě potřeby jsou přidány nové výpočetní instance. Při horizontálním navýšení kapacity na více virtuálních počítačů může funkce Orchestrator běžet na jednom virtuálním počítači, ale funkce aktivity, které volání funkce Orchestrator může běžet na několika různých virtuálních počítačích. Další informace o chování Durable Functions škálování najdete v tématu [výkon a škálování](durable-functions-perf-and-scale.md).

Historie spouštění pro účty nástroje Orchestrator je uložena v úložišti tabulek. Pokaždé, když se instance rehydratované na konkrétním virtuálním počítači, nástroj Orchestrator načte svou historii spuštění z úložiště tabulek, aby mohl znovu sestavit svůj místní stav. Vhodným aspektem, který má k dispozici historii v úložišti tabulek, je, že k zobrazení historie orchestrací můžete použít nástroje, jako je [Průzkumník služby Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md) .

Objekty blob úložiště se primárně používají jako mechanismus zapůjčení ke koordinaci škálování instancí orchestrace napříč několika virtuálními počítači. Objekty blob úložiště uchovávají data pro velké zprávy, které se nedají uložit přímo v tabulkách nebo frontách.

![Snímek obrazovky s Průzkumník služby Azure Storage](./media/durable-functions-concepts/storage-explorer.png)

> [!NOTE]
> I když je snadné a praktické zobrazit historii spouštění v úložišti tabulek, neprovádějte v této tabulce žádné závislosti. Tabulka se může změnit, protože se vyvíjí rozšíření Durable Functions.

## <a name="known-issues"></a>Známé problémy

Všechny známé problémy by měly být sledovány v seznamu [problémů na GitHubu](https://github.com/Azure/azure-functions-durable-extension/issues) . Pokud narazíte na problém a nemůžete najít problém v GitHubu, otevřete nový problém. Uveďte podrobný popis problému.

## <a name="next-steps"></a>Další kroky

Další informace o Durable Functions najdete v tématu [Durable Functions typy a funkce](durable-functions-types-features-overview.md)funkcí. 

Jak začít:

> [!div class="nextstepaction"]
> [Vytvoření první trvalé funkce](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html