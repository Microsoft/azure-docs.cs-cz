---
title: Přehled funkce trvanlivý – Azure
description: Úvod do rozšíření trvanlivý funkce pro Azure Functions.
services: functions
author: cgillum
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/30/2018
ms.author: azfuncdf
ms.openlocfilehash: d253562e0ecb0d53739a4cdc5f9747e33d7e1171
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33764396"
---
# <a name="durable-functions-overview"></a>Trvanlivý přehled funkcí

*Trvanlivý funkce* je rozšířením [Azure Functions](functions-overview.md) a [Azure WebJobs](../app-service/web-sites-create-web-jobs.md) který umožňuje zapisovat stavová funkce v prostředí bez serveru. Rozšíření spravuje stav, kontrolní body a restartování za vás.

Rozšíření umožňuje definovat stavová pracovních postupů v nový typ volaná funkce *orchestrator funkce*. Tady jsou některé z výhod funkce orchestrator:

* Pracovní postupy definují v kódu. Žádná schémata JSON nebo návrháři, je potřeba.
* Další funkce můžou volat synchronně a asynchronně. Výstup z vyvolání funkce lze uložit na místní proměnné.
* Budou automaticky kontrolní bod jejich probíhá vždy, když funkce čeká. Místní stavu dojde ke ztrátě nikdy, pokud proces recykluje nebo virtuální počítač se restartuje.

> [!NOTE]
> Trvanlivý funkce je rozšíření rozšířené pro funkce Azure, která není vhodná pro všechny aplikace. Zbývající část tohoto článku předpokládá, že máte silné znalost [Azure Functions](functions-overview.md) koncepty a problémů součástí vývoj aplikací bez serveru.

Případem primárního použití pro funkce trvanlivý je zjednodušení spolupráce komplexní, stavová problémy s aplikacemi bez serveru. Následující části popisují některé vzory Typická aplikace, které můžete využít trvanlivý funkce.

## <a name="pattern-1-function-chaining"></a>Vzor #1: Funkce řetězení

*Funkce řetězení* odkazuje na vzor provádění pořadí funkcí v určitém pořadí. Výstup jednu funkci často potřebuje má být použita pro vstup jinou funkci.

![Diagram řetězení – funkce](media/durable-functions-overview/function-chaining.png)

Trvanlivý funkce umožňuje implementovat tento vzor výstižně v kódu.

#### <a name="c"></a>C#

```cs
public static async Task<object> Run(DurableOrchestrationContext ctx)
{
    try
    {
        var x = await ctx.CallActivityAsync<object>("F1");
        var y = await ctx.CallActivityAsync<object>("F2", x);
        var z = await ctx.CallActivityAsync<object>("F3", y);
        return  await ctx.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // error handling/compensation goes here
    }
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (pouze funkce v2)

```js
const df = require("durable-functions");

module.exports = df(function*(ctx) {
    const x = yield ctx.df.callActivityAsync("F1");
    const y = yield ctx.df.callActivityAsync("F2", x);
    const z = yield ctx.df.callActivityAsync("F3", y);
    return yield ctx.df.callActivityAsync("F4", z);
});
```

Hodnoty "F1", "F2", "F3" a "F4" jsou názvy jiných funkcí v aplikaci funkce. Tok řízení je implementovaná pomocí normální imperativní kódování konstrukce. To znamená kód provede shora dolů a může zahrnovat existující sémantiku toku řízení jazyk, jako jsou podmínky a smyčky.  Chyba při zpracování logiky můžou být součástí try/catch/finally – bloky.

`ctx` Parametr ([DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html)) poskytuje metody pro vyvolání jiných funkcí podle názvu, předávání parametrů a vrátí výstup funkce. Pokaždé, když kód zavolá metodu `await`, rozhraní trvanlivý funkce *kontrolní body* průběh aktuální instance funkce. Pokud proces nebo virtuální počítač recykluje v polovině prostřednictvím provádění, instance funkce obnoví z předchozí `await` volání. Další informace o toto chování restartovat později.

## <a name="pattern-2-fan-outfan-in"></a>Vzor #2: Fan-odesílacího/fan-v

*FAN odesílacího/fan v* odkazuje na vzor provádění více funkcí paralelně a pak čeká na dokončení všech.  Některé pracovní agregace se často provádí na výsledky vrácené funkcí.

![Diagram FAN odesílacího/fan v](media/durable-functions-overview/fan-out-fan-in.png)

S normální funkce ventilační lze provést tak, že funkce Odeslat více zpráv do fronty. Zpět v ventilační je však mnohem víc náročné. Nutné napsat kód můžete sledovat, kdy funkce aktivované protokolem fronty ukončení a uložení výstupy funkce. Trvanlivý funkce rozšíření zpracovává tento vzor kódem poměrně jednoduché.

#### <a name="c"></a>C#

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    var parallelTasks = new List<Task<int>>();
 
    // get a list of N work items to process in parallel
    object[] workBatch = await ctx.CallActivityAsync<object[]>("F1");
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = ctx.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }
 
    await Task.WhenAll(parallelTasks);
 
    // aggregate all N outputs and send result to F3
    int sum = parallelTasks.Sum(t => t.Result);
    await ctx.CallActivityAsync("F3", sum);
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (pouze funkce v2)

```js
const df = require("durable-functions");

module.exports = df(function*(ctx) {
    const parallelTasks = [];

    // get a list of N work items to process in parallel
    const workBatch = yield ctx.df.callActivityAsync("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(ctx.df.callActivityAsync("F2", workBatch[i]));
    }

    yield ctx.df.task.all(parallelTasks);

    // aggregate all N outputs and send result to F3
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield ctx.df.callActivityAsync("F3", sum);
});
```

Pracovní fan-out je distribuován do více instancí funkce `F2`, a práce je sledován pomocí dynamického seznamu úloh. .NET `Task.WhenAll` rozhraní API se nazývá čekání na všechny volané funkce ukončíte. Pak se `F2`funkce výstupy se agregují ze seznamu úkolů dynamické a na předaný `F3` funkce.

Automatické vytváření kontrolních bodů, které se odehrává na `await` volání na `Task.WhenAll` zajistí všechny havárie nebo restartování polovině prostřednictvím nevyžaduje restartování všech dokončeno úlohy.

## <a name="pattern-3-async-http-apis"></a>Vzor #3: Rozhraní API HTTP asynchronní

Třetí vzor se točí kolem problém koordinace stav dlouhotrvající operace s externími klienty. Běžný způsob implementace tohoto vzoru spočívá v použití akce dlouho běžící aktivovány volání protokolu HTTP a pak přesměruje klienta na stav koncového bodu, který se může dotazovat na další po dokončení operace.

![Diagram rozhraní API HTTP](media/durable-functions-overview/async-http-api.png)

Trvanlivý funkce poskytuje integrované rozhraní API, která zjednodušit kód, který můžete psát pro interakci s jednotlivými spuštěními funkce dlouho běžící. [Ukázky](durable-functions-install.md) zobrazit jednoduchý příkaz REST, který můžete použít ke spuštění nové instance funkce produktu orchestrator. Po spuštění instance rozšíření zpřístupní webhook rozhraní API HTTP takový dotaz stav funkce produktu orchestrator. Následující příklad ukazuje REST příkazy k spuštění orchestrator a dotaz na stav. Některé podrobnosti jsou pro přehlednost vynechání z příkladu.

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

Vzhledem k tomu, že stav je spravovaná službou trvanlivý funkce modulu runtime, nemáte implementovat vlastní stav sledování mechanismus.

I když rozšíření trvanlivý funkce má integrovanou webhooky pro správu dlouho běžící orchestrations, můžete implementovat tento vzor sami pomocí vlastní funkce aktivace (třeba HTTP, fronty nebo Centrum událostí) a `orchestrationClient` vazby. Můžete například použít zprávu fronty k aktivaci ukončení.  Nebo můžete použít aktivační procedury HTTP chráněné službou Azure Active Directory zásad ověřování namísto integrované webhooků, který pomocí generovaného klíče pro ověřování. 

```cs
// HTTP-triggered function to start a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    TraceWriter log)
{
    // Function name comes from the request URL.
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);
    
    log.Info($"Started orchestration with ID = '{instanceId}'.");
    
    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

[DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` parametr je hodnota z `orchestrationClient` výstup vazby, která je součástí rozšíření trvanlivý funkce. Poskytuje metody pro počáteční, odeslání události, ukončení a dotazuje se na nový nebo existující orchestrator funkce instancí. V předchozím příkladu protokolu HTTP aktivované – funkce přebírá `functionName` hodnotu z příchozí adresy URL a předává, které hodnoty na [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). Toto rozhraní API vazby vrátí odpověď obsahující `Location` hlavičky a další informace o instanci, která můžete později použije pro vyhledávání registrace stavu spuštěna instance nebo ho ukončit.

## <a name="pattern-4-monitoring"></a>Vzor #4: sledování

Vzor monitorování odkazuje flexibilní *opakovaného* procesu v pracovním postupu – například dotazování až do splnění určitých podmínek. Regulární aktivaci časovačem můžete vyřešit jednoduchého scénáře, jako je například úlohu pravidelné čištění, ale jeho interval je statický a správa životnosti instance se změní na komplexní. Trvanlivý funkce umožňuje flexibilní opakování intervalech, správu životního cyklu úloh a schopnost vytvářet více monitorování procesů z jedné orchestration.

Příklad by Prohodit starší scénář rozhraní API HTTP asynchronní. Místo vystavení koncový bod pro externí klienta k monitorování dlouho běžící operace, dlouhotrvajících monitorování spotřebovává externí koncový bod, čekání některé změny stavu.

![Diagram monitorování](media/durable-functions-overview/monitor.png)

Pomocí trvanlivý funkcí, lze vytvořit více monitorů, které sledovat libovolný koncové body v několika řádků kódu. Monitorování můžete ukončit provádění, pokud nějaká podmínka splníte nebo ukončí se [DurableOrchestrationClient](durable-functions-instance-management.md), a jejich interval čekání se dá změnit podle nějaká podmínka (tj. exponenciálního omezení rychlosti.) Následující kód implementuje základní monitorování.

#### <a name="c"></a>C#

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    int jobId = ctx.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();
    
    while (ctx.CurrentUtcDateTime < expiryTime) 
    {
        var jobStatus = await ctx.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform action when condition met
            await ctx.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        var nextCheck = ctx.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await ctx.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform further work here, or let the orchestration end
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (pouze funkce v2)

```js
const df = require("durable-functions");
const df = require("moment");

module.exports = df(function*(ctx) {
    const jobId = ctx.df.getInput();
    const pollingInternal = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(ctx.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield ctx.df.callActivityAsync("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform action when condition met
            yield ctx.df.callActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration will sleep until this time
        const nextCheck = moment.utc(ctx.df.currentUtcDateTime).add(pollingInterval, 's');
        yield ctx.df.createTimer(nextCheck.toDate());
    }

    // Perform further work here, or let the orchestration end
});
```

Odeslaná žádost, je vytvořena nová instance orchestration pro ID tohoto úlohy. Instance dotazuje stavu, dokud je splněna podmínka a smyčky je byl ukončen. Trvanlivý časovač se používá k řízení interval dotazování. Pak lze provádět další práci, nebo můžete ukončit orchestration. Když `ctx.CurrentUtcDateTime` překračuje `expiryTime`, skončení monitorování.

## <a name="pattern-5-human-interaction"></a>Vzor #5: Zásahem ze strany

Velký počet procesů zahrnovat nějaký druh zásahem ze strany obsluhy. Složité věc o zahrnující člověka v automatizovaného procesu je, že uživatelé nejsou vždy jako vysoce dostupný a dobře reagovaly jako cloudové služby. Automatizované procesy musíte povolit pro tuto a jejich často to udělat pomocí časových limitů a kompenzace logiku.

Příkladem obchodní proces, který zahrnuje zásahem ze strany je proces schvalování. Například může být schválení od vedoucího požadované pro sestavu výdajů, které překročí určitou. Pokud správce nebude schvalovat do 72 hodin (možná, že se na dovolenou), eskalace proces se spustí pro získání schválení od jiného uživatele (možná manažera manager).

![Diagram zásahem ze strany](media/durable-functions-overview/approval.png)

Tento vzor můžete implementovat pomocí funkce produktu orchestrator. Orchestrator využije [trvanlivý časovače](durable-functions-timers.md) požádat o schválení a zvýšení v případě vypršení časového limitu. By počkejte [externí událostí](durable-functions-external-events.md), který bude oznámení generované některé zásahem ze strany.

#### <a name="c"></a>C#

```cs
public static async Task Run(DurableOrchestrationContext ctx)
{
    await ctx.CallActivityAsync("RequestApproval");
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = ctx.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = ctx.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = ctx.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await ctx.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await ctx.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (pouze funkce v2)

```js
const df = require("durable-functions");
const moment = require('moment');

module.exports = df(function*(ctx) {
    yield ctx.df.callActivityAsync("RequestApproval");

    const dueTime = moment.utc(ctx.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = ctx.df.createTimer(dueTime.toDate());

    const approvalEvent = ctx.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield ctx.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield ctx.df.callActivityAsync("ProcessApproval", approvalEvent.result);
    } else {
        yield ctx.df.callActivityAsync("Escalate");
    }
});
```

Trvanlivý časovač se vytvoří voláním `ctx.CreateTimer`. Doručení pomocí `ctx.WaitForExternalEvent`. A `Task.WhenAny` nazývá se můžete rozhodnout, jestli chcete-li postoupit (vypršení časového limitu se stane nejprve) nebo zpracovat schválení (schválení je obdrženy předtím, než časový limit).

Externích klientských lze doručit oznámení událostí buď pomocí funkce orchestrator čekání [integrované rozhraní API HTTP](durable-functions-http-api.md#raise-event) nebo pomocí [DurableOrchestrationClient.RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) rozhraní API z Další funkce:

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

## <a name="the-technology"></a>Technologie

Na pozadí rozšíření trvanlivý funkce je postavený na [trvanlivý Framework úloh](https://github.com/Azure/durabletask), knihovny open-source na Githubu pro vytváření orchestrations trvanlivý úloh. Jako jak Azure Functions je bez serveru vývoj Azure WebJobs, mnohem trvanlivý Functions je bez serveru vývoj trvanlivý Framework úloh. Trvanlivý úloh Framework se používá výraznou v rámci společnosti Microsoft a mimo také automatizovat klíčové procesy. Je přirozené přizpůsobit pro bez serveru prostředí Azure Functions.

### <a name="event-sourcing-checkpointing-and-replay"></a>Událost sourcing, vytváření kontrolních bodů a opětovného přehrání

Funkce Orchestrator spolehlivě zachovat jejich stavu spuštění pomocí vzoru návrhu cloudu známé jako [Sourcing událostí](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing). Místo ukládání přímo *aktuální* stav orchestration, trvanlivý rozšíření používá k zaznamenání úložišti připojovacím *úplné sérii akcí* provedenou funkce orchestration. To má mnoho výhod, jako třeba vylepšení výkonu, škálovatelnosti a odezvy ve srovnání s "vypsání" stav úplné modulu runtime. Mezi další výhody patří konzistence typu případné poskytuje pro transakční data a údržbu úplných a historie. Záznamy auditu, sami povolení spolehlivé zušlechtěných akcí.

Použití událostí Sourcing toto rozšíření je transparentní. V pozadí `await` operátor v funkci orchestrator vypočítá kontrolu nad vlákno orchestrator zpět do dispečera trvanlivý Framework úloh. Dispečer pak potvrdí všechny nové akce, které funkce orchestrator naplánované (například volání funkcí jeden nebo více podřízených nebo plánování trvanlivý časovač) do úložiště. Tato akce transparentní potvrzení připojí k *historie provádění* instance orchestration. Historie je uložena v tabulce úložiště. Akce zápisu pak přidá zprávy do fronty při plánování samotnou práci. V tomto okamžiku funkce orchestrator může být uvolněn z paměti. Fakturace pro něj zastaví, pokud používáte plánování využívání funkce Azure.  Pokud existuje další práci udělat, je funkce restartování a její stav je znovu vytvořena.

Jakmile funkce orchestration je zadána více práce uděláte (například je přijatá zpráva odpovědi nebo trvanlivý vyprší), orchestrator probudí se znovu a aby bylo možné znovu sestavit místní stav znovu spustí celý funkce od začátku. Pokud během tohoto opětovného přehrání kód pokusí volat funkci (nebo provést jiné asynchronní pracovní), rozhraní trvanlivý úloh zajímají s *historie provádění* aktuální Orchestrace. Pokud zjistí, že už provedena funkce aktivitu a poskytuje některé výsledek, replays výsledku této funkce a kód orchestrator běžet dál. Tento stav bude trvat děje, dokud funkce kód získá do bodu, kde buď dokončení nebo má naplánovanou práci nový asynchronní.

### <a name="orchestrator-code-constraints"></a>Omezení kód produktu Orchestrator

Chování opětovného přehrání vytvoří omezení na typu kód, který může být napsán v funkce produktu orchestrator. Kód produktu orchestrator například musí být deterministický, jak budou přehrány několikrát a musí mít stejný výsledek pokaždé, když. Úplný seznam omezení lze nalézt v [Orchestrator kód omezení](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints) části **vytváření kontrolních bodů a restartujte** článku.

## <a name="language-support"></a>Podpora jazyků

Aktuálně C# (funkce v1 a v2) a JavaScript (pouze funkce v2) jsou pouze podporované jazyky pro odolná funkce. To zahrnuje orchestrator funkce a funkce aktivity. V budoucnu přidáme podpora pro všechny jazyky, které podporuje Azure Functions. Azure Functions najdete v části [seznam problémů úložiště GitHub](https://github.com/Azure/azure-functions-durable-extension/issues) chcete zobrazit nejnovější stav naše další jazyková podpora práce.

## <a name="monitoring-and-diagnostics"></a>Monitorování a diagnostika

Trvanlivý funkce rozšíření automaticky vysílá sledování strukturovaných dat [Application Insights](functions-monitoring.md) když je funkce aplikace nakonfigurovaná s klíčem instrumentace Application Insights. Tato data sledování můžete použít ke sledování chování a průběh vaší orchestrations.

Tady je příklad vzhled trvanlivý funkce sledování událostí v portálu Application Insights pomocí [Application Insights Analytics](https://docs.microsoft.com/azure/application-insights/app-insights-analytics):

![Výsledky dotazu statistiky aplikace](media/durable-functions-overview/app-insights-1.png)

Existuje mnoho užitečné strukturovaná data zabalit do `customDimensions` pole v každé položky protokolu. Tady je příklad takové položky úplně rozbalit.

![pole customDimensions v aplikaci Statistika dotazu](media/durable-functions-overview/app-insights-2.png)

Z důvodu opětovného přehrání chování dispečera trvanlivý Framework úloh lze očekávat zobrazíte položky redundantní protokolu pro přehraná akce. To může být užitečné pochopit chování opětovného přehrání modulu jádra. [Diagnostiky](durable-functions-diagnostics.md) článek ukazuje ukázkové dotazy, které protokoly opětovného přehrání filtrovat, abyste viděli pouze "v reálném čase" protokoly.

## <a name="storage-and-scalability"></a>Úložiště a škálovatelnost

Rozšíření trvanlivý funkce používá fronty Azure Storage, tabulky a objekty BLOB se zachovat provádění historie stavu a aktivační události funkce provádění. Můžete použít výchozí účet úložiště pro funkce aplikace, nebo můžete nakonfigurovat účet samostatného úložiště. Můžete chtít samostatný účet z důvodu omezení propustnosti úložiště. Orchestrator kód, který můžete psát nemusí (a neměli) komunikovat s entity v tyto účty úložiště. Entity, které jsou spravovány přímo trvanlivý Framework úloh jako podrobností implementace.

Funkce Orchestrator plán aktivita funkce a jejich odpovědi prostřednictvím zprávy vnitřní fronty přijímat. Když funkce aplikace běží v plánu spotřeby funkce Azure, tyto fronty jsou monitorovány pomocí [Azure funkce škálování řadiče](functions-scale.md#how-the-consumption-plan-works) a nový výpočet instance přidány podle potřeby. Při škálovat na více systémů pro víc virtuálních počítačů, funkce orchestrator může spustit v jeden virtuální počítač při spuštění funkce aktivity, který volá na několik různých virtuálních počítačích. Další informace naleznete na chování škálování trvanlivý funkcí v [výkonu a možností škálování](durable-functions-perf-and-scale.md).

Úložiště Table se používá k ukládání historie provádění pro orchestrator účty. Vždy, když instance rehydrates na konkrétním virtuálním počítači, ho načte jeho historie provádění z úložiště tabulek tak, aby ji můžete znovu sestavit stav místní. Jednou z věcí pohodlný o nutnosti historie k dispozici ve službě Table storage je, můžete si je můžete prohlédnout a najdete v historii vaší orchestrations pomocí nástrojů, jako [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Snímek Azure Storage Explorer obrazovky](media/durable-functions-overview/storage-explorer.png)

> [!WARNING]
> I když je snadný a pohodlný zobrazíte historii spouštění ve službě table storage, neberte některé závislé na této tabulce. Se může změnit při zpracovaní rozšíření trvanlivý funkce.

## <a name="known-issues-and-faq"></a>Známé problémy a nejčastější dotazy

Všechny známé problémy, které mají být sledovány v [Githubu problémy](https://github.com/Azure/azure-functions-durable-extension/issues) seznamu. Pokud dojde k potížím a nelze najít problém v Githubu, otevřete nový problém a obsahovat podrobný popis problému.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Pokračujte ve čtení trvanlivý funkce dokumentace](durable-functions-bindings.md)

> [!div class="nextstepaction"]
> [Nainstalujte rozšíření trvanlivý funkce a ukázky](durable-functions-install.md)

