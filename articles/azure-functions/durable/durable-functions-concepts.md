---
title: Odolné funkce vzory a technických konceptech v Azure Functions
description: Zjistěte, jak rozšíření Durable Functions ve službě Azure Functions poskytuje výhody stavový kód spuštění v cloudu.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: aa9563266f6b43e3bc2f21fbc0b340c86c5895ae
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60862017"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Odolné funkce vzory a technických konceptech (Azure Functions)

Odolná služba Functions je rozšířením [Azure Functions](../functions-overview.md) a [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md). Odolná služba Functions můžete použít k zápisu stavové funkce v prostředí bez serveru. Toto rozšíření za vás spravuje stav, kontrolní body a restartování. 

Tento článek poskytuje podrobné informace o chování rozšíření Durable Functions pro běžné vzory implementace a Azure Functions. Informace můžou pomoct určit, jak vyřešit problémy při vývoji pomocí Durable Functions.

> [!NOTE]
> Odolná služba Functions je pokročilá rozšíření pro službu Azure Functions, která není vhodná pro všechny aplikace. Tento článek předpokládá, že máte silné znalost konceptů v [Azure Functions](../functions-overview.md) a problémů při vývoji aplikace bez serveru.

## <a name="patterns"></a>Vzory

Tato část popisuje některé běžné vzory aplikací, kde odolná služba Functions může být užitečné.

### <a name="chaining"></a>Vzor #1: Řetězení funkcí

Ve funkci řetězení vzor spustí pořadí funkcí v určitém pořadí. V tomto vzoru výstup jedné funkce platí pro vstup jiné funkci.

![Diagram funkce řetězení vzor](./media/durable-functions-concepts/function-chaining.png)

Odolná služba Functions můžete použít k implementaci funkce řetězení vzor stručně a výstižně, jak je znázorněno v následujícím příkladu:

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
> Existují malé rozdíly v psaní předkompilované odolné funkce v C# a zápis předkompilované odolné funkce C# skript, který je znázorněno v příkladu. V C# předkompilované funkce, trvalý parametry musí být doplněny pomocí příslušných atributů. Příkladem je `[OrchestrationTrigger]` atribut pro `DurableOrchestrationContext` parametru. V C# předkompilované odolné funkce, je-li parametry nejsou správně upraveným, modul runtime nelze vložit do funkce proměnné a dojde k chybě. Další příklady najdete v článku [azure-functions-durable-extension ukázky na Githubu](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples).

#### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

V tomto příkladu hodnoty `F1`, `F2`, `F3`, a `F4` jsou názvy další funkce do aplikace function App. Tok řízení můžete implementovat pomocí normální imperativní programování konstrukce. Kód spustí shora dolů. Kód může zahrnovat existující sémantiku toku řízení jazyka, jako jsou podmíněné příkazy a smyčky. Můžete vytvořit logiku zpracování chyb v `try` / `catch` / `finally` bloky.

Můžete použít `context` parametr [DurableOrchestrationContext] \(.NET\) a `context.df` volání další funkce podle názvu, předat parametry a vrátí funkce objekt (JavaScript) výstup. Pokaždé, když kód volá `await` (C#) nebo `yield` (JavaScript), kontrolní body framework Durable Functions průběh aktuální instance funkce. Pokud proces nebo virtuální počítač recykluje polovině provádění, obnoví instanci funkce z předchozího `await` nebo `yield` volání. Další informace najdete v další části se vzor č. 2: Ventilátor out/podporuje.

> [!NOTE]
> `context` Objekt v jazyce JavaScript představuje celé [funkce kontextu](../functions-reference-node.md#context-object), nejen [DurableOrchestrationContext] parametru.

### <a name="fan-in-out"></a>Vzor #2: Ventilátor out/podporuje

Ventilátor out/ventilátor ve vzorku, paralelně spustit více funkcí a potom počkejte na dokončení všech funkcí. Úkony agregace se často provádí na výsledky, které jsou vráceny z funkce.

![Diagram ventilátor out/ventilátor vzor](./media/durable-functions-concepts/fan-out-fan-in.png)

U běžných funkcí můžete se škálovatelnou tím, že funkce Odeslat více zpráv do fronty. Zpět v ventilační je mnohem složitější. Chcete sloučit, v normální funkce psaní kódu můžete sledovat, kdy funkce aktivované protokolem front end a potom úložiště fungovat výstupy. 

Rozšíření Durable Functions zpracovává tento model s poměrně jednoduchý kód:

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

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

Větveného úkony distribuované na více instancí `F2` funkce. Práce se sleduje pomocí dynamického seznamu úkolů. .NET `Task.WhenAll` rozhraní API nebo JavaScript `context.df.Task.all` rozhraní API volat, čekat na dokončení všech volané funkce. Pak, bude `F2` funkce výstupů se agregují ze seznamu dynamického úloh a předat `F3` funkce.

Automatické vytváření kontrolních bodů, ke které dochází na `await` nebo `yield` volat `Task.WhenAll` nebo `context.df.Task.all` zajistí, že potenciální uprostřed při selhání nebo restartování nevyžaduje restartování již dokončeného úkolu.

### <a name="async-http"></a>Vzor #3: Async HTTP APIs

Asynchronní vzor rozhraní API HTTP, řeší potíže s koordinace stavu dlouhotrvající operace s externími klienty. Běžný způsob, jak tento model implementovat, je tak, že volání triggeru dlouhotrvající akce HTTP. Pak přesměruje klienta na stav koncového bodu, který klient dotazuje další po dokončení operace.

![Diagram vzoru HTTP API](./media/durable-functions-concepts/async-http-api.png)

Odolná služba Functions poskytuje integrované rozhraní API, která zjednodušuje kód, který napíšete pro interakci s dlouhotrvající provádění funkcí. Ukázky quickstart Durable Functions ([ C# ](durable-functions-create-first-csharp.md) a [JavaScript](quickstart-js-vscode.md)) ukazují jednoduchý příkaz REST, které můžete použít ke spuštění nové instance funkce nástroje orchestrator. Po spuštění instance rozšíření zpřístupní webhook, rozhraní API HTTP, který dotaz na stav funkce nástroje orchestrator. 

Následující příklad ukazuje příkazů REST, které začínají orchestrátor a dotaz na její stav. Pro přehlednost jsou vynechány některé podrobnosti jako v příkladu.

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

Vzhledem k tomu, že modul runtime Durable Functions spravuje stavu, není nutné implementovat vlastní mechanismus sledování stavu.

Rozšíření Durable Functions má integrované webhooky, které spravují dlouho běžící Orchestrace. Tento model může také implementovat sami pomocí vlastní aktivační události – funkce (třeba HTTP, fronty nebo Azure Event Hubs) a `orchestrationClient` vazby. Fronty zpráv může například použít k aktivaci ukončení. Nebo můžete použít aktivační událost HTTP, který je chráněný pomocí zásad ověřování Azure Active Directory místo integrované webhooky, který pomocí generovaného klíče pro ověřování.

Tady je několik příkladů, jak používat rozhraní HTTP API vzoru:

#### <a name="c"></a>C#

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

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

> [!WARNING]
> Při vývoji místně v kódu jazyka JavaScript, používají metody `DurableOrchestrationClient`, musíte nastavit proměnnou prostředí `WEBSITE_HOSTNAME` k `localhost:<port>` (například `localhost:7071`). Další informace o tomto požadavku najdete v tématu [problém Githubu 28](https://github.com/Azure/azure-functions-durable-js/issues/28).

V rozhraní .NET [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` parametr je hodnota z `orchestrationClient` výstupní vazby, který je součástí rozšíření Durable Functions. V jazyce JavaScript tento objekt je vrácen voláním `df.getClient(context)`. Tyto objekty poskytují metody, které můžete použít pro spuštění, odesílání událostí do, ukončení a dotaz pro instancemi nástroje orchestrator nové nebo existující funkce.

V předchozích ukázkách přijímá funkci aktivovanou protokolem HTTP `functionName` hodnotu z adresy URL příchozích a předává hodnotu [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) vazba rozhraní API potom vrátí odpověď, která obsahuje `Location` záhlaví a další informace o instanci. Informace můžete použít později k vyhledání stav instance spuštěna nebo k ukončení instance.

### <a name="monitoring"></a>Vzor #4: Monitorování

Vzor monitorování odkazuje na flexibilní, opakované zpracování v pracovním postupu. Příkladem je interval dotazování, dokud jsou splněny konkrétní podmínky. Můžete použít běžný [trigger časovače](../functions-bindings-timer.md) k adresování základní scénáře, jako je například úloha pravidelné čištění, ale jeho interval je statická a správu životnosti instance stane složité. Odolná služba Functions můžete vytvářet intervaly opakování flexibilní, spravovat úlohy životnosti a vytvořte více monitorování procesů z jednoho Orchestrace.

Příkladem vzoru monitorování je vrátit starší scénář asynchronní rozhraní API protokolu HTTP. Místo zveřejňování koncový bod pro externí klienty monitorovat dlouhotrvající operace, dlouhotrvající monitorování spotřebovává externí koncový bod a potom čeká změnu stavu.

![Diagram vzoru monitorování](./media/durable-functions-concepts/monitor.png)

V několika řádků kódu můžete použít Durable Functions k vytvoření více monitorů respektujících libovolného koncových bodů. Monitorování můžete ukončit provádění, když je splněna podmínka, nebo [DurableOrchestrationClient](durable-functions-instance-management.md) lze ukončit monitorování. Můžete změnit monitorování `wait` interval na základě konkrétní podmínky (například exponenciální regresí.) 

Následující kód implementuje základní monitorování:

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

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

Při přijetí požadavku pro ID tohoto. úloze je vytvořena nová instance Orchestrace Instance dotazuje ve stavu, dokud je podmínka splněna a smyčky je byl ukončen. Trvalý časovače Určuje interval dotazování. Potom více práce lze provést, nebo můžete ukončit orchestraci. Když `context.CurrentUtcDateTime` (.NET) nebo `context.df.currentUtcDateTime` překračuje (JavaScript) `expiryTime` hodnota konce monitorování.

### <a name="human"></a>Vzor #5: Lidská interakce

Mnoho automatizovaných procesů, které zahrnují nějaký druh lidské interakce. Zahrnující lidí v automatizovaného procesu je velmi obtížné, protože uživatelé nejsou jako vysoce dostupný a jako responzivní jako cloudové služby. Automatizovaný proces může povolit pro tuto pomocí logiky vypršení časových limitů a kompenzace.

Schvalovací proces je příkladem obchodní proces, který zahrnuje lidské interakce. Schválení od správce může být nezbytný pro překročí určitou dobu dolar sestavy výdajů. Pokud správce neudělí schválení vyúčtování během 72 hodin (možná manager přešel na dovolenou), eskalace proces aktivuje, chcete-li získat schválení od jiného uživatele (možná manažer tohoto manažera).

![Diagram vzoru lidská interakce](./media/durable-functions-concepts/approval.png)

Vzor v tomto příkladu můžete implementovat pomocí funkce orchestrátoru. Orchestrator používá [trvalý časovače](durable-functions-timers.md) k žádosti o schválení. Pokud dojde k vypršení časového limitu proto ho postoupí produktu orchestrator. Orchestrátor čeká [externí událost](durable-functions-external-events.md), jako jsou oznámení, který je generován zásahem ze strany.

Tyto příklady vytvoření schvalovací proces k předvedení zásahem ze strany vzoru:

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

#### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

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

Chcete-li vytvořit trvalý časovače, zavolejte `context.CreateTimer` (.NET) nebo `context.df.createTimer` (JavaScript). Se přijal oznámení `context.WaitForExternalEvent` (.NET) nebo `context.df.waitForExternalEvent` (JavaScript). Potom `Task.WhenAny` (.NET) nebo `context.df.Task.any` můžete rozhodnout, jestli ke zvýšení úrovně se nazývá (JavaScript) (časový limit nastane dřív) nebo zpracovávat schválení (schválení přijetí před vypršením časového limitu).

Externího klienta můžete pomocí doručování oznámení o události do funkce orchestrátoru čekání [integrovaná rozhraní API HTTP](durable-functions-http-api.md#raise-event) nebo pomocí [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) rozhraní API z Další funkce:

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
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

## <a name="the-technology"></a>Tato technologie

Na pozadí rozšíření Durable Functions je postavený na [trvalý Framework úloh](https://github.com/Azure/durabletask), knihovny open source na Githubu, který se používá k vytváření orchestrací trvalý úloh. Jako Azure Functions bez serveru vývoj Azure WebJobs, je Durable Functions bez serveru vývoj odolné architektury úloh. Microsoft a jiných společností pomocí rozhraní trvalý úlohy výrazně automatizovat klíčové procesy. Je přirozeně vhodná pro prostředí bez serveru Azure Functions.

### <a name="event-sourcing-checkpointing-and-replay"></a>Model Event sourcing, vytváření kontrolních bodů a opětovného přehrání

Funkce nástroje Orchestrator spolehlivě Udržovat stav jejich provedení pomocí [modelu event sourcing](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) vzoru návrhu. Místo uložení přímo aktuální stav Orchestrace, rozšíření Durable Functions používá úložiště nabízí jen možnost připojovat pro záznam úplné posloupnosti akcí, které přijímá Orchestrace funkce. Úložiště nabízí jen možnost připojovat má mnoho výhod oproti "vypsání" úplné běhový stav. Mezi výhody patří vyšší výkon, škálovatelnost a rychlost odezvy. Získáte také konečné konzistence transakčních dat a úplné záznamy pro audit a historii. Záznamy pro audit podporují spolehlivé provádět kompenzační akce.

Odolná služba Functions používá modelu event sourcing transparentně. Na pozadí `await` (C#) nebo `yield` – operátor (JavaScript) v funkce orchestrátoru vrací řízení vlákna orchestrator zpět do dispečera trvalý Framework úloh. Dispečer pak potvrdí všechny nové akce, které funkce orchestrátoru naplánované (třeba volání jedné nebo více podřízených funkcí nebo plánování trvalý časovače) do úložiště. Akce transparentní potvrzení se připojí k historii spuštění instance Orchestrace. Historie je uložena v tabulce úložiště. Potvrzení akce poté přidá zprávy do fronty k naplánování samotnou práci. Funkce orchestrátoru v tomto okamžiku může být uvolněna z paměti. 

Fakturace za funkce orchestrátoru zastaví, pokud používáte plán consumption Azure Functions. Když existuje více práce uděláte, se restartuje – funkce a jeho stav je znovu vytvořena.

Když orchestraci funkce dostane další práci (například doručení zprávy do odpovědi nebo trvalý vyprší), orchestrator probudí a znovu spustí funkci celou od začátku znovu sestavit místní stavu. 

Během opětovného přehrání, pokud kód se pokusí zavolat funkci (nebo proveďte jiné asynchronní práce), trvalý Framework úloh consults historie provádění aktuální Orchestrace. Pokud se zjistí, že [funkce aktivitu](durable-functions-types-features-overview.md#activity-functions) již spuštěn a vrátil výsledek, přehrává výsledku této funkce a kód orchestrator nadále běží. Opakování bude pokračovat, dokud se nedokončí kód funkce, nebo dokud je jeho naplánováno nový asynchronní práce.

### <a name="orchestrator-code-constraints"></a>Omezení kódu produktu Orchestrator

Chování opakování orchestrator kódu vytvoří omezení na typu kódu, který píšete v funkce orchestrátoru. Orchestrator kód například musí být deterministický, protože budou přehrány více než jednou a jeho musí mít stejný výsledek pokaždé, když. Úplný seznam omezení, najdete v části [omezení kódu Orchestrator](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints).

## <a name="monitoring-and-diagnostics"></a>Monitorování a diagnostika

Rozšíření Durable Functions automaticky generuje sledování strukturovaná data do [Application Insights](../functions-monitoring.md) Pokud nastavíte aplikaci function app s instrumentačním klíčem Azure Application Insights. Data sledování můžete použít k monitorování akcí a probíhá vaše Orchestrace.

Tady je příklad Durable Functions sledování událostí vypadat na portálu Application Insights při použití [Application Insights Analytics](../../application-insights/app-insights-analytics.md):

![Výsledky dotazu Application Insights](./media/durable-functions-concepts/app-insights-1.png)

Můžete najít užitečné strukturovaných dat ve službě `customDimensions` pole v každé položky protokolu. Tady je příklad, který je úplně rozbalit položky:

![CustomDimensions pole v dotazu Application Insights](./media/durable-functions-concepts/app-insights-2.png)

Z důvodu chování opakování dispečer trvalý Framework úloh můžete očekávat zobrazíte položky redundantní protokolu pro přehraná akce. Položky protokolu redundantní pomůže vám porozumět chování opakování modulu jádra. [Diagnostiky](durable-functions-diagnostics.md) článek ukazuje ukázkové dotazy, které odfiltrovat opětovného přehrání protokoly, abyste viděli právě "v reálném čase" protokoly.

## <a name="storage-and-scalability"></a>Úložiště a škálovatelnost

Rozšíření Durable Functions používá front, tabulek a objektů BLOB ve službě Azure Storage k zachování spuštění historie stavu a aktivační události spuštění funkce. Můžete použít výchozí účet úložiště pro aplikaci function app, nebo můžete nakonfigurovat samostatný účet úložiště. Samostatný účet na základě limitů propustnosti úložiště může být vhodné. Kód produktu orchestrator, který napíšete nepracuje s entitami v těchto účtech úložiště. Trvalý rozhraní úloha spravuje entity přímo jako podrobnost implementace.

Funkce nástroje Orchestrator naplánovat funkce aktivity a zobrazí jejich odpovědi prostřednictvím vnitřní fronty zpráv. Při spuštění aplikace function app v plánu consumption Azure Functions, [řadič škálování Azure Functions](../functions-scale.md#how-the-consumption-and-premium-plans-work) monitoruje tyto fronty. Podle potřeby se přidají nové výpočetních instancí. Horizontální navýšení kapacity na několik virtuálních počítačů, může být spuštění funkce orchestrátoru na jeden virtuální počítač, při funkce aktivity, které volání funkcí nástroje orchestrator může spustit na několika různých virtuálních počítačů. Další informace o chování škálování Durable Functions najdete v tématu [výkon a škálování](durable-functions-perf-and-scale.md).

Historie provádění pro účty orchestrator je uložen v úložišti tabulek. Pokaždé, když se instance rehydrates na konkrétní virtuální počítač, orchestrátoru načte historii jejího spouštění ze služby table storage, takže ho můžete znovu sestavit stavu místní. Je vhodné aspekt máte k dispozici v úložišti tabulek v historii, které můžete použít nástroje, jako je [Průzkumníka služby Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md) zobrazíte historii vašich Orchestrace.

Úložiště objektů blob se používají především jako mechanismus "pronájmu" ke koordinaci škálování Orchestrace instancí napříč několika virtuálními počítači. Úložiště objektů BLOB uchovávání dat pro velké zprávy, které nemůže být uložen přímo do tabulky nebo fronty.

![Snímek obrazovky Průzkumníka služby Azure Storage](./media/durable-functions-concepts/storage-explorer.png)

> [!WARNING]
> I když je snadné a pohodlné zobrazit historii provádění ve službě table storage, neprovádějte žádné závislosti pro tuto tabulku. Tabulka může změnit, protože rozšíření Durable Functions vyvíjí.

## <a name="known-issues"></a>Známé problémy

Všechny známé problémy, které mají být sledovány v [problémy Githubu](https://github.com/Azure/azure-functions-durable-extension/issues) seznamu. Pokud narazíte na problém a nejde najít problému v Githubu otevře nový problém. Zahrnout podrobný popis problému.

## <a name="next-steps"></a>Další postup

Další informace o Durable Functions najdete v tématu [Durable Functions funkce, typy a funkce](durable-functions-types-features-overview.md). 

Jak začít:

> [!div class="nextstepaction"]
> [Vytvoření první funkce trvalý](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html
