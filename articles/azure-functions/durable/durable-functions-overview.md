---
title: Přehled Durable Functions – Azure
description: Úvod do rozšíření Durable Functions pro Azure Functions
author: cgillum
ms.topic: overview
ms.date: 12/23/2020
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: 3725970c982c2d060685bf0b99d12a8fc998f20a
ms.sourcegitcommit: 799f0f187f96b45ae561923d002abad40e1eebd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/24/2020
ms.locfileid: "97763570"
---
# <a name="what-are-durable-functions"></a>Co je Durable Functions?

*Durable Functions* je rozšíření [Azure Functions](../functions-overview.md) , které umožňuje psát stavové funkce v prostředí COMPUTE bez serveru. Toto rozšíření umožňuje definovat stavové pracovní postupy napsáním funkcí a stavových entit nástroje [*Orchestrator*](durable-functions-orchestrations.md) napsáním [*funkcí*](durable-functions-entities.md) jazyka pomocí programovacího modelu Azure Functions. Na pozadí spravuje rozšíření stav, kontrolní body a restarty pro vás, což vám umožní soustředit se na obchodní logiku.

## <a name="supported-languages"></a><a name="language-support"></a>Podporované jazyky

Durable Functions aktuálně podporuje následující jazyky:

* **C#**: [knihovny předkompilovaných tříd](../functions-dotnet-class-library.md) a [skript jazyka C#](../functions-reference-csharp.md).
* **JavaScript**: podporuje se jenom pro verzi 2. x Azure Functions runtime. Vyžaduje verzi 1.7.0 rozšíření Durable Functions nebo novější verzi. 
* **Python**: vyžaduje verzi 2.3.1 rozšíření Durable Functions nebo novější verzi. Podpora Durable Functions je v současnosti ve verzi Public Preview.
* **F #**: předkompilované knihovny tříd a skript jazyka F #. Skript F # se podporuje jenom pro verzi 1. x modulu runtime Azure Functions.
* **PowerShell**: podpora pro Durable Functions je aktuálně ve verzi Public Preview. Podporováno pouze pro verzi 3. x modulu Azure Functions runtime a prostředí PowerShell 7. Vyžaduje verzi 2.2.2 rozšíření Durable Functions nebo novější verzi. V současné době jsou podporovány pouze následující vzorce: [řetězení funkcí](#chaining), [ventilátor nebo ventilátor –](#fan-in-out) [asynchronní rozhraní HTTP API](#async-http).

Pro přístup k nejnovějším funkcím a aktualizacím se doporučuje používat nejnovější verze rozšíření Durable Functions a knihovny Durable Functions pro konkrétní jazyk. Přečtěte si další informace o [Durable Functions verzích](durable-functions-versions.md).

Durable Functions má za cíl podporu všech [Azure Functionsch jazyků](../supported-languages.md). Nejnovější stav práce pro podporu dalších jazyků najdete v [seznamu problémů s Durable Functions](https://github.com/Azure/azure-functions-durable-extension/issues) .

Stejně jako Azure Functions, jsou šablony, které vám pomohou vyvíjet Durable Functions pomocí sady [Visual Studio 2019](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md)a [Azure Portal](durable-functions-create-portal.md).

## <a name="application-patterns"></a>Vzory aplikací

Primární případ použití pro Durable Functions zjednodušuje komplexní požadavky na stavovou spolupráci v aplikacích bez serveru. Následující části popisují typické vzory aplikací, které můžou využívat Durable Functions:

* [Řetězení funkcí](#chaining)
* [Výstupní/vstupní větvení](#fan-in-out)
* [Asynchronní rozhraní API HTTP](#async-http)
* [Monitorování](#monitoring)
* [Lidská interakce](#human)
* [Agregátor (stavové entity)](#aggregator)

### <a name="pattern-1-function-chaining"></a><a name="chaining"></a>Vzor #1: řetězení funkcí

Ve vzoru zřetězení funkcí se posloupnost funkcí provádí v určitém pořadí. V tomto modelu je výstup jedné funkce aplikován na vstup jiné funkce.

![Diagram vzoru řetězení funkcí](./media/durable-functions-concepts/function-chaining.png)

Můžete použít Durable Functions k implementaci vzor řetězení funkcí stručně, jak je znázorněno v následujícím příkladu.

V tomto příkladu hodnoty,, `F1` `F2` `F3` a `F4` jsou názvy dalších funkcí ve stejné aplikaci Function App. Tok řízení lze implementovat pomocí normálních imperativních konstrukcí kódování. Kód se spustí shora dolů. Kód může zahrnovat stávající sémantiku toku řízení jazyka, jako jsou podmínky a smyčky. Do bloků můžete zahrnout logiku zpracování chyb `try` / `catch` / `finally` .

# <a name="c"></a>[C#](#tab/csharp)

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

Můžete použít `context` parametr k vyvolání dalších funkcí podle názvu, Pass Parameters a vracet výstup funkce. Pokaždé, když kód volá `await` , Durable Functions Framework vystaví průběh aktuální instance funkce. Pokud se proces nebo virtuální počítač recykluje v průběhu provádění, instance funkce pokračuje z předchozího `await` volání. Další informace najdete v další části vzor #2: ventilátor nebo ventilátor v.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Můžete použít `context.df` objekt k vyvolání dalších funkcí podle názvu, Pass Parameters a vracet výstup funkce. Pokaždé, když kód volá `yield` , Durable Functions Framework vystaví průběh aktuální instance funkce. Pokud se proces nebo virtuální počítač recykluje v průběhu provádění, instance funkce pokračuje z předchozího `yield` volání. Další informace najdete v další části vzor #2: ventilátor nebo ventilátor v.

> [!NOTE]
> `context`Objekt v JavaScriptu představuje celý [kontext funkce](../functions-reference-node.md#context-object). Přístup k Durable Functionsmu kontextu pomocí `df` vlastnosti v hlavním kontextu.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    x = yield context.call_activity("F1", None)
    y = yield context.call_activity("F2", x)
    z = yield context.call_activity("F3", y)
    result = yield context.call_activity("F4", z)
    return result


main = df.Orchestrator.create(orchestrator_function)
```

Můžete použít `context` objekt k vyvolání dalších funkcí podle názvu, Pass Parameters a vracet výstup funkce. Pokaždé, když kód volá `yield` , Durable Functions Framework vystaví průběh aktuální instance funkce. Pokud se proces nebo virtuální počítač recykluje v průběhu provádění, instance funkce pokračuje z předchozího `yield` volání. Další informace najdete v další části vzor #2: ventilátor nebo ventilátor v.

> [!NOTE]
> `context`Objekt v Pythonu představuje kontext orchestrace. Přístup k hlavnímu kontextu Azure Functions pomocí `function_context` vlastnosti v kontextu orchestrace.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
param($Context)

$X = Invoke-ActivityFunction -FunctionName 'F1'
$Y = Invoke-ActivityFunction -FunctionName 'F2' -Input $X
$Z = Invoke-ActivityFunction -FunctionName 'F3' -Input $Y
Invoke-ActivityFunction -FunctionName 'F4' -Input $Z
```

Pomocí `Invoke-ActivityFunction` příkazu můžete vyvolat další funkce podle názvu, Pass Parameters a vracet výstup funkce. Pokaždé, když kód volá `Invoke-ActivityFunction` bez `NoWait` přepínače, Durable Functions Framework kontrolní body průběh aktuální instance funkce. Pokud se proces nebo virtuální počítač recykluje v průběhu provádění, instance funkce pokračuje z předchozího `Invoke-ActivityFunction` volání. Další informace najdete v další části vzor #2: ventilátor nebo ventilátor v.

---

### <a name="pattern-2-fan-outfan-in"></a><a name="fan-in-out"></a>Vzor #2: ventilátor nebo ventilátor v

Ve vzorku se v ventilátoru nebo ventilátoru spouští více funkcí paralelně a pak čekají na dokončení všech funkcí. U výsledků, které se vrátí z funkcí, se často provádí některá agregační práce.

![Diagram vzoru ventilátoru a ventilátoru](./media/durable-functions-concepts/fan-out-fan-in.png)

Díky normálním funkcím se můžete dostat do fronty tak, že funkci odešlete více zpráv. Fanning zpět je mnohem náročnější. Pro ventilátor v nástroji normální funkce napíšete kód, který bude sledován při ukončení funkcí aktivovaných frontou, a následné uložení výstupů funkcí.

Rozšíření Durable Functions zpracovává tento vzor s poměrně jednoduchým kódem:

# <a name="c"></a>[C#](#tab/csharp)

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

Práce s ventilátorem je distribuována do více instancí `F2` funkce. Práce je sledována pomocí dynamického seznamu úkolů. `Task.WhenAll` se volá, aby se čekalo na dokončení všech volaných funkcí. `F2`Výstupy funkcí pak jsou agregovány z dynamického seznamu úkolů a předány `F3` funkci.

Automatické kontrolní body, ke kterým dojde při `await` volání, `Task.WhenAll` zajistí, že potenciální funkce pro zhroucení nebo restartování počítače nevyžadují restartování již dokončené úlohy.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Práce s ventilátorem je distribuována do více instancí `F2` funkce. Práce je sledována pomocí dynamického seznamu úkolů. `context.df.Task.all` Volá se rozhraní API, které čeká na dokončení všech volaných funkcí. `F2`Výstupy funkcí pak jsou agregovány z dynamického seznamu úkolů a předány `F3` funkci.

Automatické kontrolní body, ke kterým dojde při `yield` volání, `context.df.Task.all` zajistí, že potenciální funkce pro zhroucení nebo restartování počítače nevyžadují restartování již dokončené úlohy.

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    # Get a list of N work items to process in parallel.
    work_batch = yield context.call_activity("F1", None)

    parallel_tasks = [ context.call_activity("F2", b) for b in work_batch ]
    
    outputs = yield context.task_all(parallel_tasks)

    # Aggregate all N outputs and send the result to F3.
    total = sum(outputs)
    yield context.call_activity("F3", total)


main = df.Orchestrator.create(orchestrator_function)
```

Práce s ventilátorem je distribuována do více instancí `F2` funkce. Práce je sledována pomocí dynamického seznamu úkolů. `context.task_all` Volá se rozhraní API, které čeká na dokončení všech volaných funkcí. `F2`Výstupy funkcí pak jsou agregovány z dynamického seznamu úkolů a předány `F3` funkci.

Automatické kontrolní body, ke kterým dojde při `yield` volání, `context.task_all` zajistí, že potenciální funkce pro zhroucení nebo restartování počítače nevyžadují restartování již dokončené úlohy.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```PowerShell
param($Context)

# Get a list of work items to process in parallel.
$WorkBatch = Invoke-ActivityFunction -FunctionName 'F1'

$ParallelTasks =
    foreach ($WorkItem in $WorkBatch) {
        Invoke-ActivityFunction -FunctionName 'F2' -Input $WorkItem -NoWait
    }

$Outputs = Wait-ActivityFunction -Task $ParallelTasks

# Aggregate all outputs and send the result to F3.
$Total = ($Outputs | Measure-Object -Sum).Sum
Invoke-ActivityFunction -FunctionName 'F3' -Input $Total
```

Práce s ventilátorem je distribuována do více instancí `F2` funkce. Všimněte si, že použití `NoWait` přepínače u `F2` volání funkce: Tento přepínač umožňuje, aby nástroj Orchestrator pokračoval v volání `F2` bez pro dokončení aktivity. Práce je sledována pomocí dynamického seznamu úkolů. `Wait-ActivityFunction`Příkaz se zavolá, aby čekal na dokončení všech volaných funkcí. `F2`Výstupy funkcí pak jsou agregovány z dynamického seznamu úkolů a předány `F3` funkci.

Automatický kontrolní bod, který se stane při `Wait-ActivityFunction` volání, zajistí, že potenciální chyba nástroje Midway nebo restartování nevyžaduje restartování již dokončené úlohy.

---

> [!NOTE]
> Ve výjimečných případech je možné, že dojde k chybě v okně po dokončení funkce aktivity, ale před tím, než se její dokončení uloží do historie orchestrace. Pokud k tomu dojde, funkce Activity by se po obnovení procesu znovu spouštěla od začátku.

### <a name="pattern-3-async-http-apis"></a><a name="async-http"></a>Vzor #3: asynchronní rozhraní HTTP API

Vzor asynchronního protokolu HTTP API řeší problém koordinace stavu dlouhotrvajících operací s externími klienty. Běžný způsob implementace tohoto modelu je, že koncový bod HTTP spustí dlouhotrvající akci. Pak přesměrujte klienta na koncový bod stavu, který se klient dotazuje, když se operace dokončí.

![Diagram vzoru HTTP API](./media/durable-functions-concepts/async-http-api.png)

Durable Functions poskytuje **integrovanou podporu** pro tento model, což zjednodušuje nebo odebírá kód, který je potřeba napsat pro interakci s dlouhotrvajícím spouštěním funkcí. Například ukázky pro rychlý Start Durable Functions ([C#](durable-functions-create-first-csharp.md) a [JavaScript](quickstart-js-vscode.md)) ukazují jednoduchý příkaz REST, který můžete použít ke spuštění nových instancí funkcí nástroje Orchestrator. Po spuštění instance rozšíření zpřístupňuje rozhraní API HTTP Webhooku, která se dotazují na stav funkce nástroje Orchestrator. 

Následující příklad ukazuje příkazy REST, které spouštějí nástroj Orchestrator a dotazují svůj stav. Pro přehlednost jsou některé podrobnosti protokolu z příkladu vynechány.

```
> curl -X POST https://myfunc.azurewebsites.net/api/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/instances/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

Vzhledem k tomu, že modul runtime Durable Functions spravuje stav za vás, nemusíte implementovat vlastní mechanismus pro sledování stavu.

Rozšíření Durable Functions zpřístupňuje Vestavěná rozhraní API HTTP, která spravují dlouhotrvající orchestrace. Tento vzor můžete případně implementovat sami pomocí triggerů funkcí (například HTTP, front nebo Azure Event Hubs) a [vazby klienta Orchestration](durable-functions-bindings.md#orchestration-client). Můžete například použít zprávu fronty k aktivaci ukončení. Nebo můžete použít Trigger HTTP, který je chráněný pomocí Azure Active Directory zásad ověřování místo integrovaných rozhraní API HTTP, která pro ověřování používají vygenerovaný klíč.

Další informace najdete v článku [funkce protokolu HTTP](durable-functions-http-features.md) , který vysvětluje, jak můžete vystavit asynchronní a dlouhotrvající procesy přes protokol HTTP pomocí rozšíření Durable Functions.

### <a name="pattern-4-monitor"></a><a name="monitoring"></a>Vzor #4: monitorování

Model monitorování odkazuje na flexibilní a opakovaný proces v pracovním postupu. Příkladem je cyklické dotazování, dokud nebudou splněny určité podmínky. Pomocí pravidelné [aktivační procedury časovače](../functions-bindings-timer.md) můžete vyřešit základní scénář, například úlohu pravidelného čištění, ale jeho interval je statický a Správa životnosti instance je složitá. Pomocí Durable Functions můžete vytvářet flexibilní intervaly opakování, spravovat životnost úloh a vytvářet více procesů monitorování z jedné orchestrace.

Příkladem vzoru monitorování je vrácení dřívějšího scénáře asynchronního protokolu HTTP API. Místo vystavení koncového bodu pro externího klienta za účelem monitorování dlouhotrvající operace používá dlouhotrvající monitor externí koncový bod a poté čeká na změnu stavu.

![Diagram modelu monitoru](./media/durable-functions-concepts/monitor.png)

V několika řádcích kódu můžete pomocí Durable Functions vytvořit více monitorů, které sledují libovolné koncové body. Monitory mohou ukončit provádění, pokud je splněna podmínka, nebo jiná funkce může použít trvalého klienta Orchestration k ukončení monitorování. Můžete změnit interval monitorování na `wait` základě konkrétní podmínky (například exponenciální omezení rychlosti.) 

Následující kód implementuje základní monitor:

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df
import json
from datetime import timedelta 


def orchestrator_function(context: df.DurableOrchestrationContext):
    job = json.loads(context.get_input())
    job_id = job["jobId"]
    polling_interval = job["pollingInterval"]
    expiry_time = job["expiryTime"]

    while context.current_utc_datetime < expiry_time:
        job_status = yield context.call_activity("GetJobStatus", job_id)
        if job_status == "Completed":
            # Perform an action when a condition is met.
            yield context.call_activity("SendAlert", job_id)
            break

        # Orchestration sleeps until this time.
        next_check = context.current_utc_datetime + timedelta(seconds=polling_interval)
        yield context.create_timer(next_check)

    # Perform more work here, or let the orchestration end.


main = df.Orchestrator.create(orchestrator_function)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Monitorování se v prostředí PowerShell aktuálně nepodporuje.

---

Po přijetí žádosti se pro ID úlohy vytvoří nová instance Orchestration. Instance se dotazuje na stav, dokud není splněna podmínka a dojde k ukončení smyčky. Interval cyklického dotazování řídí trvalý časovač. Pak je možné provést více práce, nebo orchestrace může skončit. Když `nextCheck` se překročí `expiryTime` , monitor skončí.

### <a name="pattern-5-human-interaction"></a><a name="human"></a>Vzor #5: interakce člověka

Řada automatizovaných procesů zahrnuje nějaký druh lidské interakce. Zapojení lidí do automatizovaného procesu je obtížné, protože lidé nejsou jako vysoce dostupné a reagují jako cloudové služby. Automatizovaný proces může pro tuto interakci umožňovat pomocí časových limitů a logiky kompenzace.

Schvalovací proces je příkladem obchodního procesu, který zahrnuje lidskou interakci. Pro sestavu výdajů, která překračuje určitou částku dolaru, může být vyžadováno schválení správcem. Pokud správce neschválí zprávu o výdajích do 72 hodin (možná správce přešel na dovolenou), proces eskalace se zahájí a získá schválení od někoho jiného (možná správce manažera).

![Diagram vzoru lidské interakce](./media/durable-functions-concepts/approval.png)

Vzor v tomto příkladu můžete implementovat pomocí funkce Orchestrator. Nástroj Orchestrator používá k žádosti o schválení [trvalý časovač](durable-functions-timers.md) . Pokud dojde k vypršení časového limitu, produkt Orchestrator postoupí. Orchestrator čeká na [externí událost](durable-functions-external-events.md), jako je například oznámení, které vygenerovala lidská interakce.

Tyto příklady vytvoří proces schvalování, který předvádí vzor lidské interakce:

# <a name="c"></a>[C#](#tab/csharp)

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

Chcete-li vytvořit trvalý časovač, zavolejte `context.CreateTimer` . Oznámení přijal (a) `context.WaitForExternalEvent` . Pak `Task.WhenAny` se zavolá, aby se rozhodlo, jestli se má eskalovat (časový limit nastane jako první), nebo jestli se má schválit schválení (schválení se přijme před časovým limitem)

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

Chcete-li vytvořit trvalý časovač, zavolejte `context.df.createTimer` . Oznámení přijal (a) `context.df.waitForExternalEvent` . Pak `context.df.Task.any` se zavolá, aby se rozhodlo, jestli se má eskalovat (časový limit nastane jako první), nebo jestli se má schválit schválení (schválení se přijme před časovým limitem)

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df
import json
from datetime import timedelta 


def orchestrator_function(context: df.DurableOrchestrationContext):
    yield context.call_activity("RequestApproval", None)

    due_time = context.current_utc_datetime + timedelta(hours=72)
    durable_timeout_task = context.create_timer(due_time)
    approval_event_task = context.wait_for_external_event("ApprovalEvent")

    winning_task = yield context.task_any([approval_event_task, durable_timeout_task])

    if approval_event_task == winning_task:
        durable_timeout_task.cancel()
        yield context.call_activity("ProcessApproval", approval_event_task.result)
    else:
        yield context.call_activity("Escalate", None)


main = df.Orchestrator.create(orchestrator_function)
```

Chcete-li vytvořit trvalý časovač, zavolejte `context.create_timer` . Oznámení přijal (a) `context.wait_for_external_event` . Pak `context.task_any` se zavolá, aby se rozhodlo, jestli se má eskalovat (časový limit nastane jako první), nebo jestli se má schválit schválení (schválení se přijme před časovým limitem)

# <a name="powershell"></a>[PowerShell](#tab/powershell)

V PowerShellu se v současnosti nepodporuje lidská interakce.

---

Externí klient může doručovat oznámení události do čekající funkce Orchestrator pomocí [integrovaných rozhraní API http](durable-functions-http-api.md#raise-event):

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

Událost se dá taky vyvolat pomocí trvalého klienta Orchestration z jiné funkce ve stejné aplikaci Function App:

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

# <a name="python"></a>[Python](#tab/python)

```python
import azure.durable_functions as df


async def main(client: str):
    durable_client = df.DurableOrchestrationClient(client)
    is_approved = True
    await durable_client.raise_event(instance_id, "ApprovalEvent", is_approved)
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

V PowerShellu se v současnosti nepodporuje lidská interakce.

---

### <a name="pattern-6-aggregator-stateful-entities"></a><a name="aggregator"></a>Vzor #6: agregátor (stavové entity)

Šestým vzorem je informace o agregaci dat událostí v časovém intervalu až na jednu, adresovatelnou *entitu*. V tomto modelu mohou být shromážděná data z více zdrojů, mohou být dodávána v dávkách nebo mohou být rozmístěna za dlouhou dobu. Agregátor může potřebovat provést akci s daty události při jejich doručení a externí klienti budou potřebovat dotaz na agregovaná data.

![Agregátorový diagram](./media/durable-functions-concepts/aggregator.png)

Důvodem, proč se pokusit o implementaci tohoto modelu s normálními a bezstavovým funkcemi, je, že řízení souběžnosti se stává obrovským problémem. Nemusíte si dělat starosti s více vlákny, které mění stejná data současně, musíte se také starat o to, že agregátor běží jenom na jednom virtuálním počítači.

Můžete použít [trvalé entity](durable-functions-entities.md) k jednoduché implementaci tohoto modelu jako jediné funkce.

# <a name="c"></a>[C#](#tab/csharp)

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

Odolné entity lze také modelovat jako třídy v rozhraní .NET. Tento model může být užitečný, pokud je seznam operací pevný a bude velký. Následující příklad je ekvivalentní implementace `Counter` entity pomocí tříd a metod .NET.

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

V Pythonu se v současnosti nepodporují trvalé entity.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

V PowerShellu se v současnosti nepodporují trvalé entity.

---

Klienti mohou zařadit *operace* do fronty (označované také jako "signalizace") entity funkce pomocí [vazby klienta entit](durable-functions-bindings.md#entity-client).

# <a name="c"></a>[C#](#tab/csharp)

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
> Dynamicky generované proxy servery jsou také k dispozici v rozhraní .NET pro signalizaci entit v typově bezpečném způsobu. Kromě signalizace se klienti můžou také dotazovat na stav funkce entity pomocí [typově bezpečných metod](durable-functions-bindings.md#entity-client-usage) na vazbu klienta Orchestration.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

# <a name="python"></a>[Python](#tab/python)

V Pythonu se v současnosti nepodporují trvalé entity.

# <a name="powershell"></a>[PowerShell](#tab/powershell)

V PowerShellu se v současnosti nepodporují trvalé entity.

---

Funkce entit jsou k dispozici v [Durable Functions 2,0](durable-functions-versions.md) a vyšších pro C# a JavaScript.

## <a name="the-technology"></a>Technologie

Na pozadí je rozšíření Durable Functions postaveno nad [trvalým prostředím úloh](https://github.com/Azure/durabletask), open source knihovny na GitHubu, která se používá k sestavování pracovních postupů v kódu. Podobně jako Azure Functions je vývoj Azure WebJobs bez serveru, Durable Functions je vývojem trvalého prostředí úloh bez serveru. Microsoft a další organizace často využívají trvalý rámec úloh pro automatizaci nejdůležitějších procesů. Pro prostředí Azure Functions bez serveru je to přirozené.

## <a name="code-constraints"></a>Omezení kódu

Za účelem zajištění spolehlivých a dlouhotrvajících záruk spouštění mají funkce nástroje Orchestrator sadu pravidel pro kódování, která musí být dodržena. Další informace naleznete v článku o [omezeních kódu funkce nástroje Orchestrator](durable-functions-code-constraints.md) .

## <a name="billing"></a>Fakturace

Durable Functions se účtují stejně jako Azure Functions. Další informace najdete v tématu [Azure Functions ceny](https://azure.microsoft.com/pricing/details/functions/). Při provádění funkcí Orchestrator v plánu Azure Functions [spotřeby](../functions-scale.md#consumption-plan)existují některá nastavení fakturace, o kterých je potřeba vědět. Další informace o tomto chování najdete v článku o [fakturaci Durable Functions](durable-functions-billing.md) .

## <a name="jump-right-in"></a>Skok přímo v

Můžete začít s Durable Functions za 10 minut, a to provedením jednoho z těchto kurzů pro rychlý Start pro konkrétní jazyk:

* [C# s využitím sady Visual Studio 2019](durable-functions-create-first-csharp.md)
* [JavaScript pomocí Visual Studio Code](quickstart-js-vscode.md)
* [Python s použitím Visual Studio Code](quickstart-python-vscode.md)
* [PowerShell s použitím Visual Studio Code](quickstart-powershell-vscode.md)

V těchto rychlých startech se místně vytváří a testuje trvalá funkce Hello World. Kód funkce potom publikujete do Azure. Funkce, kterou vytváříte, orchestruje a řetězí volání dalších funkcí.

## <a name="learn-more"></a>Další informace

Následující video zvýrazní výhody Durable Functions:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Podrobnější diskuzi o Durable Functions a základní technologii najdete v následujícím videu (zaměřuje se na .NET, ale koncepty platí i pro jiné podporované jazyky):

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Vzhledem k tomu, že Durable Functions je pokročilá rozšíření pro [Azure Functions](../functions-overview.md), není vhodné pro všechny aplikace. Porovnání s dalšími technologiemi orchestrace Azure najdete v tématu [porovnání Azure functions a Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Durable Functions typy a funkce funkcí](durable-functions-types-features-overview.md)
