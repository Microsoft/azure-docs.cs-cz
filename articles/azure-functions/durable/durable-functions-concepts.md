---
title: Odolné funkce vzory a technických konceptech – Azure
description: Obsahuje podrobné informace o fungování odolná služba Functions v Azure umožňuje výhody stavový kód spuštění v cloudu.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: cecf7f7fb79b6d7ebeed051b018a1e18375d68b2
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54045760"
---
# <a name="durable-functions-patterns-and-technical-concepts"></a>Odolné funkce vzory a technické koncepty

*Odolná služba Functions* je rozšířením [Azure Functions](../functions-overview.md) a [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) , který umožňuje zapisovat stavové funkce v prostředí bez serveru. Rozšíření spravuje stav, kontrolní body a restartuje za vás. Tento článek poskytuje podrobnější informace o chování rozšíření Durable Functions pro Azure Functions a běžné vzory implementace.

> [!NOTE]
> Odolná služba Functions je pokročilá rozšíření pro Azure Functions, která není vhodná pro všechny aplikace. Zbývající část tohoto článku se předpokládá, že máte silné znalost [Azure Functions](../functions-overview.md) koncepty a problémů účastní vývoje aplikací bez serveru.

## <a name="patterns"></a>Vzory

Tato část popisuje některé vzory Typická aplikace, které může přinést Durable Functions.

### <a name="chaining"></a>Vzor #1: Řetězení funkcí

*Funkce řetězení* odkazuje na model provedení pořadí funkcí v určitém pořadí. Často výstup jedné funkce je potřeba použít se vstupem jiné funkce.

![Řetězení diagram – funkce](./media/durable-functions-concepts/function-chaining.png)

Odolná služba Functions umožňuje stručně a výstižně implementaci tohoto modelu v kódu.

#### <a name="c-script"></a>Skript jazyka C#

```cs
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
        // error handling/compensation goes here
    }
}
```

> [!NOTE]
> Při zápisu předkompilované odolné funkce v sadě Visual Studio C# C# ukázkový skript ukázalo dřív jsou drobné rozdíly. Funkce jazyka C# předkompilované by vyžadovaly trvalý parametry se mají být dekorován příslušnými atributy. Příkladem je `[OrchestrationTrigger]` atributu `DurableOrchestrationContext` parametru. Pokud parametry nejsou upravená správně, modul runtime se budou moci vložení proměnné do funkce a získáte chyba. Navštivte prosím [ukázka](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples) Další příklady.

#### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

```js
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

Hodnoty "F1", "F2", "F3" a "F4" jsou názvy další funkce do aplikace function App. Tok řízení je implementováno použití běžné konstrukce kódu. To znamená, že kód spustí shora dolů a může zahrnovat existující sémantiku toku řízení jazyka, jako jsou podmíněné příkazy a smyčky.  Konstrukce try/catch/finally bloky mohou být součástí logiku zpracování chyb.

`context` Parametr [DurableOrchestrationContext] \(.NET\) a `context.df` – objekt (JavaScript) poskytují metody pro volání další funkce podle názvu, předávání parametrů, a vrácení funkce výstup. Pokaždé, když kód volá `await` (C#) nebo `yield` (JavaScript), rozhraní Durable Functions *kontrolní body* průběh aktuální instance funkce. Pokud proces nebo virtuální počítač recykluje polovině provádění, obnoví instanci funkce z předchozího `await` nebo `yield` volání. Další informace o toto chování restartovat později.

> [!NOTE]
> `context` Objekt v jazyce JavaScript představuje [funkce kontextu](../functions-reference-node.md#context-object) jako celá, není [DurableOrchestrationContext].

### <a name="fan-in-out"></a>Vzor #2: Výstupní/vstupní větvení

*FAN odesílací/fan v* odkazuje na vzor paralelně prováděných více funkcí a potom čeká na dokončení všech.  Úkony agregace se často provádí na výsledky vrácené z funkce.

![FAN odesílací/fan v diagramu](./media/durable-functions-concepts/fan-out-fan-in.png)

U běžných funkcí ventilační navýšení kapacity můžete provést tak, že funkce Odeslat více zpráv do fronty. Ventilační zpět v je však mnohem složitější. Je třeba napsat kód pro sledování při ukončení funkce aktivované protokolem fronty a ukládání výstupů funkce. Rozšíření Durable Functions zpracovává tento model kódem poměrně jednoduché.

#### <a name="c-script"></a>Skript jazyka C#

```cs
public static async Task Run(DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // get a list of N work items to process in parallel
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // aggregate all N outputs and send result to F3
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

```js
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // get a list of N work items to process in parallel
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // aggregate all N outputs and send result to F3
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

Větveného úkony distribuované na více instancí funkce `F2`, a práce je sledovaný pomocí dynamického seznamu úkolů. .NET `Task.WhenAll` rozhraní API nebo JavaScript `context.df.Task.all` voláním rozhraní API se počkat na dokončení volané funkce. Pak bude `F2` funkce výstupů se agregují ze seznamu dynamického úloh a na předán `F3` funkce.

Automatické vytváření kontrolních bodů, ke které dochází na `await` nebo `yield` volat `Task.WhenAll` nebo `context.df.Task.all` zajišťuje jakékoli selhání nebo restartování polovině nevyžaduje restartování žádné již dokončených úloh.

### <a name="async-http"></a>Vzor #3: Asynchronní rozhraní API HTTP

Třetí vzor se točí kolem problém koordinace stavu dlouhotrvající operace s externími klienty. Běžný způsob, jak tento model implementovat spočívá v použití dlouhotrvající akce aktivuje volání protokolu HTTP a potom přesměrování klienta na stav koncového bodu, který může dotazovat další po dokončení operace.

![Diagram rozhraní HTTP API](./media/durable-functions-concepts/async-http-api.png)

Odolná služba Functions poskytuje integrované rozhraní API, která zjednodušuje kód, který napíšete pro interakci s dlouhotrvající provádění funkcí. Ukázky quickstart ([C#](durable-functions-create-first-csharp.md), [JavaScript](quickstart-js-vscode.md)) ukazují jednoduchý příkaz REST, který můžete použít ke spuštění nové instance funkce nástroje orchestrator. Po zahájení instance rozšíření zpřístupní webhook, rozhraní API HTTP tento dotaz stav funkce nástroje orchestrator. Následující příklad ukazuje příkazů REST k spuštění orchestrator a dotaz na její stav. Pro přehlednost jsou vynechány některé podrobnosti jako v příkladu.

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

Vzhledem k tomu, že stav je spravován modulem runtime Durable Functions, není nutné implementovat vlastní stav sledování mechanismus.

I v případě, že rozšíření Durable Functions má integrované webhooky pro správu dlouhotrvající Orchestrace, vám může tento model implementovat sami pomocí vlastní aktivační události – funkce (třeba HTTP, fronty nebo Event Hub) a `orchestrationClient` vazby. Můžete například použít zpráv fronty k aktivaci ukončení.  Nebo můžete použít aktivační událost HTTP chráněné službou Azure Active Directory zásad ověřování namísto integrovaných webhooky, který pomocí generovaného klíče pro ověřování.

#### <a name="c"></a>C#

```cs
// HTTP-triggered function to start a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function name comes from the request URL.
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

```javascript
// HTTP-triggered function to start a new orchestrator function instance.
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);

    // Function name comes from the request URL.
    // Function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    return client.createCheckStatusResponse(req, instanceId);
};
```

> [!WARNING]
> Při vývoji místně v jazyce JavaScript, budete muset nastavit proměnnou prostředí `WEBSITE_HOSTNAME` k `localhost:<port>`, např. `localhost:7071` použití metod na `DurableOrchestrationClient`. Další informace o tomto požadavku najdete v tématu [problém Githubu](https://github.com/Azure/azure-functions-durable-js/issues/28).

V rozhraní .NET [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` parametr je hodnota z `orchestrationClient` výstupní vazby, který je součástí rozšíření Durable Functions. V jazyce JavaScript tento objekt je vrácen voláním `df.getClient(context)`. Tyto objekty poskytují metody pro počáteční, odesílání událostí do, ukončení a dotaz instancemi nástroje orchestrator nové nebo existující funkce.

V předchozím příkladu přebírá HTTP aktivuje funkci `functionName` z příchozí adresy URL a předá, které hodnoty [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) vazba rozhraní API potom vrátí odpověď, která obsahuje `Location` záhlaví a další informace o instanci, kterou můžete později použít vás pod rouškou až stavu spuštěna instance nebo ukončit ho.

### <a name="monitoring"></a>Vzor #4: Monitorování

Vzor monitorování odkazuje na flexibilní *opakované* procesu v pracovním postupu – například dotazování, dokud jsou splněny určité podmínky. Běžný [trigger časovače](../functions-bindings-timer.md) můžete adresu jednoduchého scénáře, jako je například úloha pravidelné čištění, ale jeho interval je statická a správu životnosti instance stane složité. Odolná služba Functions umožňuje flexibilní opakování intervaly, Správa životního cyklu úloh a schopnost vytvářet více monitorování procesů z jednoho Orchestrace.

Příklad by stornování starší scénář asynchronní rozhraní API protokolu HTTP. Místo zveřejňování koncový bod pro externí klienty monitorovat dlouhotrvající operace, sledování dlouho běžící využívá externí koncový bod, nějakou změnu stavu čekání.

![Diagram monitorování](./media/durable-functions-concepts/monitor.png)

Pomocí Durable Functions, je možné vytvořit více monitorů respektujících libovolného koncových bodů v několika řádků kódu. Monitorování můžete ukončit provádění, pokud je splněna některá podmínka nebo ukončit [DurableOrchestrationClient](durable-functions-instance-management.md), a jejich intervalu čekání můžete měnit v závislosti na některé podmínky (například exponenciální regresí.) Následující kód implementuje základní monitorování.

#### <a name="c-script"></a>Skript jazyka C#

```cs
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
            // Perform action when condition met
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform further work here, or let the orchestration end
}
```

#### <a name="javascript-functions-2x-only"></a>JavaScript (funguje pouze 2.x)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform action when condition met
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform further work here, or let the orchestration end
});
```

Při přijetí požadavku pro ID tohoto. úloze je vytvořena nová instance Orchestrace Instance dotazuje ve stavu, dokud je podmínka splněna a smyčky je byl ukončen. Trvalý časovač se používá k řízení interval dotazování. Další práce lze pak provést, nebo můžete ukončit orchestraci. Když `context.CurrentUtcDateTime` (.NET) nebo `context.df.currentUtcDateTime` překračuje (JavaScript) `expiryTime`, elementy end monitorování.

### <a name="human"></a>Vzor #5: Lidská interakce

Velký počet procesů zahrnují nějaký druh lidské interakce. To složité zahrnující lidí v automatizovaného procesu je, že lidé nejsou vždy jako vysoce dostupný a responzivní jako cloudové služby. Automatizované procesy musí povolit pro tuto a jsou často to tak, že pomocí logiky vypršení časových limitů a kompenzace.

Jedním z příkladů obchodních procesů, která zahrnuje lidské interakce je proces schvalování. Schválení od správce může být například požadované pro sestavy výdajů, které překročí určitou dobu. Pokud správce během 72 hodin (možná zvládli přechod na dovolenou) neschvaluje, eskalace proces aktivuje, chcete-li získat schválení od jiného uživatele (možná manažer tohoto manažera).

![Diagram lidská interakce](./media/durable-functions-concepts/approval.png)

Tento model je možné implementovat pomocí funkce orchestrátoru. Orchestrátor byste použili [trvalý časovače](durable-functions-timers.md) žádost o schválení a zvýšit v případě vypršení časového limitu. Bude čekat [externí událost](durable-functions-external-events.md), který bude oznámení vygenerovaná některé lidské interakce.

#### <a name="c-script"></a>Skript jazyka C#

```cs
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

```js
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

Trvalý časovač je vytvořen zavoláním `context.CreateTimer` (.NET) nebo `context.df.createTimer` (JavaScript). Se přijal oznámení `context.WaitForExternalEvent` (.NET) nebo `context.df.waitForExternalEvent` (JavaScript). A `Task.WhenAny` (.NET) nebo `context.df.Task.any` můžete rozhodnout, jestli ke zvýšení úrovně se nazývá (JavaScript) (časový limit nastane dřív) nebo zpracovávat schválení (schválení přijetí před vypršením časového limitu).

Externího klienta může poskytovat oznámení události buď pomocí funkce produktu orchestrator čekání [integrovaná rozhraní API HTTP](durable-functions-http-api.md#raise-event) nebo s použitím [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) rozhraní API z Další funkce:

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

Na pozadí rozšíření Durable Functions je postavený na [trvalý Framework úloh](https://github.com/Azure/durabletask), knihovny open source na Githubu pro sestavování Orchestrace úloh trvalý. Podobně jako jak se Azure Functions bez serveru vývoj Azure WebJobs, Durable Functions navazuje na bez serveru odolné architektury úloh. Trvalý Framework úloh umožňuje silně v rámci Microsoftu a mimo také automatizovat klíčové procesy. Je přirozeně vhodná pro prostředí bez serveru Azure Functions.

### <a name="event-sourcing-checkpointing-and-replay"></a>Model Event sourcing, vytváření kontrolních bodů a opětovného přehrání

Funkce nástroje Orchestrator spolehlivě Udržovat stav jejich provedení pomocí vzoru návrhu říká [modelu Event Sourcing](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing). Místo uložení přímo *aktuální* stavu Orchestrace, trvalý rozšíření používá úložiště nabízí jen možnost připojovat k zaznamenání *úplné posloupnosti akcí* provedenou na základě Orchestrace funkce. To přináší řadu výhod, včetně vylepšení výkonu, škálovatelnosti a rychlost odezvy ve srovnání s "vypsání" úplné běhový stav. Mezi další výhody patří poskytování konečné konzistence transakčních dat a zachování úplné záznamy pro audit a historii. Záznamy pro audit, sami povolit spolehlivé provádět kompenzační akce.

Použití modelu Event Sourcing v tomto rozšíření je transparentní. Pod pokličkou `await` (C#) nebo `yield` – operátor (JavaScript) v funkce orchestrátoru vrací řízení vlákna orchestrator zpět do dispečera trvalý Framework úloh. Dispečer pak potvrdí všechny nové akce, které funkce orchestrátoru naplánované (třeba volání jedné nebo více podřízených funkcí nebo plánování trvalý časovače) do úložiště. Tato akce transparentní potvrzení připojí k *historie provádění* instance Orchestrace. Historie je uložena v tabulce úložiště. Potvrzení akce poté přidá zprávy do fronty k naplánování samotnou práci. Funkce orchestrátoru v tomto okamžiku může být uvolněna z paměti. Fakturace se zastaví, pokud používáte plán Consumption funkce Azure.  Při další práci restartování funkce a její stav je znovu vytvořena.

Jakmile funkce protokolem Orchestrace dostane další práci (například doručení zprávy do odpovědi nebo trvalý vyprší), orchestrator probudí znovu a znovu provede celou funkci od samého začátku aby bylo možné znovu sestavit místní stavu. Pokud během tohoto opětovného přehrání kód se pokusí zavolat funkci (nebo provádět jiné asynchronní práce), trvalý Framework úloh consults s *historie provádění* aktuální Orchestrace. Pokud se zjistí, že [funkce aktivitu](durable-functions-types-features-overview.md#activity-functions) má již spuštěné a některé vrátil výsledek, přehrává výsledku této funkce a kód orchestrator bude nadále spuštěn. Tento postup se opakuje dokud funkce kód získá do bodu, kde je dokončeno nebo má nový asynchronní úlohy naplánované děje.

### <a name="orchestrator-code-constraints"></a>Omezení kódu produktu Orchestrator

Chování opakování vytvoří omezení na typu kódu, který je možné psát v funkce orchestrátoru. Orchestrator kód například musí být deterministický, bude znovu přehrát více než jednou a musí mít stejný výsledek pokaždé, když. Úplný seznam omezení najdete v [omezení kódu Orchestrator](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints) část **vytváření kontrolních bodů a restartujte** článku.

## <a name="monitoring-and-diagnostics"></a>Monitorování a diagnostika

Rozšíření Durable Functions automaticky generuje sledování strukturovaná data do [Application Insights](../functions-monitoring.md) při aplikaci function app se nakonfigurují Instrumentační klíč Application Insights. Tato data sledování lze použít ke sledování chování a průběh vašeho Orchestrace.

Tady je příklad vypadat Durable Functions sledování událostí v portálu služby Application Insights pomocí [Application Insights Analytics](../../application-insights/app-insights-analytics.md):

![Výsledky dotazu App Insights](./media/durable-functions-concepts/app-insights-1.png)

Dochází k mnoha užitečné strukturovaná data, obsahuje `customDimensions` pole v každé položky protokolu. Tady je příklad takové položky úplně rozbalit.

![customDimensions pole v dotazu App Insights](./media/durable-functions-concepts/app-insights-2.png)

Z důvodu chování opakování dispečer trvalý Framework úloh můžete očekávat zobrazíte položky redundantní protokolu pro přehraná akce. To může být užitečné k pochopení chování opakování modulu jádra. [Diagnostiky](durable-functions-diagnostics.md) článek ukazuje ukázkové dotazy, které protokoly opětovného přehrání odfiltrovat, abyste viděli právě "v reálném čase" protokoly.

## <a name="storage-and-scalability"></a>Úložiště a škálovatelnost

Rozšíření Durable Functions používá fronty Azure Storage, tabulek a objektů BLOB k uchování provádění historie stavu a aktivační události spuštění funkce. Výchozí účet úložiště pro danou aplikaci funkcí lze použít, nebo můžete nakonfigurovat samostatný účet úložiště. Samostatný účet z důvodu omezení propustnosti úložiště může být vhodné. Orchestrator kód, který napíšete není nutné (a by neměla) spolupracovat s entitami v těchto účtech úložiště. Entity, které se spravují přímo v rámci trvalé rozhraní úlohy jako podrobnost implementace.

Funkce nástroje Orchestrator naplánovat funkce aktivity a zobrazí jejich odpovědi prostřednictvím vnitřní fronty zpráv. Při spuštění aplikace function app v plánu Consumption funkce Azure, tyto fronty jsou monitorovány pomocí [kontroler škálování Azure Functions](../functions-scale.md#how-the-consumption-plan-works) a nový výpočet instance jsou přidány podle potřeby. Horizontální navýšení kapacity na několik virtuálních počítačů se funkce orchestrátoru narazit na jeden virtuální počítač při spuštění funkce aktivity, které volá na několik různých virtuálních počítačů. Další podrobnosti najdete na chování škálování odolná služba Functions v [výkon a škálování](durable-functions-perf-and-scale.md).

Tabulka úložiště slouží k uložení historie provádění pro účty nástroje orchestrator. Pokaždé, když se instance rehydrates na konkrétní virtuální počítač, načte historii jejího spouštění z table storage tak, aby ho můžete znovu sestavit stavu místní. Jednou z vhodné věcí o historii k dispozici ve službě Table storage je, že můžete podívat a zobrazit historii vaší Orchestrace pomocí nástrojů, jako [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md).

Úložiště objektů blob se používají především jako mechanismus "pronájmu" ke koordinaci škálování Orchestrace instancí napříč několika virtuálními počítači. Používají se také pro uchovávání dat pro velké zprávy, které nelze ukládat přímo do tabulky nebo fronty.

![Snímek obrazovky Azure Storage Exploreru](./media/durable-functions-concepts/storage-explorer.png)

> [!WARNING]
> I když je snadné a pohodlné zobrazit historii provádění ve službě table storage, vyhnete se tak všechny závislosti pro tuto tabulku. To může změnit, protože rozšíření Durable Functions vyvíjí.

## <a name="known-issues-and-faq"></a>Známé problémy a nejčastější dotazy

Všechny známé problémy, které mají být sledovány v [problémy Githubu](https://github.com/Azure/azure-functions-durable-extension/issues) seznamu. Pokud narazíte na problém a nejde najít problému v Githubu otevře nový problém a zahrnují podrobný popis problému.

## <a name="next-steps"></a>Další postup

Další informace o Durable Functions najdete v tématu [přehled funkcí pro Durable Functions (Azure Functions) a typy funkce](durable-functions-types-features-overview.md), nebo...

> [!div class="nextstepaction"]
> [Vytvoření první funkce trvalý](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html