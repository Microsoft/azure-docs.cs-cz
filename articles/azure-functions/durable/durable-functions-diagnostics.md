---
title: Diagnostika v trvanlivých funkcích – Azure
description: Zjistěte, jak diagnostikovat problémy s rozšířením Durable Functions pro funkce Azure.
author: cgillum
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 4cb832f8fe11ac2581e97d9cdcc777eaff702ee9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278191"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnostika v Durable Functions v Azure

Existuje několik možností pro diagnostiku problémů s [trvalé funkce](durable-functions-overview.md). Některé z těchto možností jsou stejné pro normální funkce a některé jsou jedinečné pro Durable Functions.

## <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) je doporučený způsob diagnostiky a monitorování v Azure Functions. Totéž platí pro trvanlivé funkce. Přehled využití přehledu aplikací ve vaší aplikaci funkcí najdete v tématu [Sledování funkcí Azure](../functions-monitoring.md).

Azure Functions Durable Extension také vydává *sledování událostí,* které umožňují sledovat spuštění od konce orchestraci. Tyto události sledování najdete a dotazujete se pomocí nástroje [Application Insights Analytics](../../azure-monitor/app/analytics.md) na webu Azure Portal.

### <a name="tracking-data"></a>Údaje o sledování

Každá událost životního cyklu instance orchestrace způsobí, že událost sledování se zapisuje do kolekce **trasování** v Application Insights. Tato událost obsahuje datovou část **customDimensions** s několika poli.  Názvy polí jsou `prop__`předřazené .

* **hubName**: Název centra úloh, ve kterém jsou spuštěny orchestrations.
* **appName**: Název aplikace funkce. Toto pole je užitečné, pokud máte více aplikací funkcí, které sdílejí stejnou instanci Application Insights.
* **slot:** [Slot pro nasazení,](../functions-deployment-slots.md) ve kterém je spuštěna aplikace aktuální funkce. Toto pole je užitečné, když využíváte sloty nasazení k verzi orchestrations.
* **functionName**: Název orchestrátoru nebo funkce aktivity.
* **functionType**: Typ funkce, například **Orchestrator** nebo **Activity**.
* **instanceId**: Jedinečné ID instance orchestrace.
* **stav**: Stav spuštění životního cyklu instance. Mezi platné hodnoty patří:
  * **Naplánováno**: Funkce byla naplánována k provedení, ale ještě nebyla spuštěna.
  * **Spuštěno**: Funkce byla spuštěna, ale ještě nebyla dokončena nebo dokončena.
  * **Čekal**: Orchestrátor naplánoval nějakou práci a čeká na její dokončení.
  * **Naslouchání**: Orchestrator naslouchá externímu oznámení o události.
  * **Dokončeno**: Funkce byla úspěšně dokončena.
  * **Nezdařilo**se: Funkce se nezdařila s chybou.
* **důvod**: Další údaje spojené s událostí sledování. Pokud například instance čeká na oznámení externí události, toto pole označuje název události, na kterou čeká. Pokud funkce selhala, bude toto pole obsahovat podrobnosti o chybě.
* **isReplay**: Logická hodnota označující, zda je událost sledování pro přehrané spuštění.
* **extensionVersion**: Verze rozšíření durable task. Informace o verzi jsou obzvláště důležité údaje při hlášení možných chyb v rozšíření. Dlouhotrvající instance může hlásit více verzí, pokud dojde k aktualizaci, když je spuštěna.
* **sequenceNumber**: Spuštění pořadové číslo pro událost. V kombinaci s časovým razítkem pomáhá pořadí událostí podle doby spuštění. *Všimněte si, že toto číslo bude nastavena na nulu, pokud se hostitel restartuje, zatímco instance je spuštěna, takže je důležité vždy seřadit podle časového razítka první, pak sequenceNumber.*

Podrobnost sledování dat vysílaných application insights lze nakonfigurovat v `logger` části `logging` `host.json` (Funkce 1.x) nebo (Funkce 2.0).

#### <a name="functions-10"></a>Funkce 1.0

```json
{
    "logger": {
        "categoryFilter": {
            "categoryLevels": {
                "Host.Triggers.DurableTask": "Information"
            }
        }
    }
}
```

#### <a name="functions-20"></a>Funkce 2.0

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Ve výchozím nastavení jsou vyzařovány všechny události sledování bez přehrání. Objem dat lze snížit `Host.Triggers.DurableTask` nastavením `"Warning"` `"Error"` nebo v takovém případě sledování událostí bude emitován pouze pro výjimečné situace.

Chcete-li povolit vyzařování podrobných `LogReplayEvents` událostí přehrání orchestrace, lze nastavit na `true` v souboru `host.json` pod, `durableTask` jak je znázorněno:

#### <a name="functions-10"></a>Funkce 1.0

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-20"></a>Funkce 2.0

```javascript
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> Ve výchozím nastavení application insights telemetrie je vzorkován v Azure Functions runtime, aby se zabránilo vyzařování dat příliš často. To může způsobit ztrátu informací o sledování, pokud dojde k mnoha událostem životního cyklu v krátkém časovém období. [Článek monitorování funkcí Azure](../functions-monitoring.md#configure-sampling) vysvětluje, jak nakonfigurovat toto chování.

### <a name="single-instance-query"></a>Dotaz na jednu instanci

Následující dotaz zobrazuje historická data sledování pro jednu instanci orchestrace funkce [Hello Sequence.](durable-functions-sequence.md) Je napsán pomocí [application insights dotazovacího jazyka (AIQL)](https://aka.ms/LogAnalyticsLanguageReference). Filtruje spuštění přehrání tak, aby byla zobrazena pouze *cesta logického* spuštění. Události lze seřadit `timestamp` podle `sequenceNumber` a jak je znázorněno v níže uvedeném dotazu:

```AIQL
let targetInstanceId = "ddd1aaa685034059b545eb004b15d4eb";
let start = datetime(2018-03-25T09:20:00);
traces
| where timestamp > start and timestamp < start + 30m
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = customDimensions["prop__functionName"]
| extend instanceId = customDimensions["prop__instanceId"]
| extend state = customDimensions["prop__state"]
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend sequenceNumber = tolong(customDimensions["prop__sequenceNumber"])
| where isReplay != true
| where instanceId == targetInstanceId
| sort by timestamp asc, sequenceNumber asc
| project timestamp, functionName, state, instanceId, sequenceNumber, appName = cloud_RoleName
```

Výsledkem je seznam událostí sledování, který zobrazuje cestu spuštění orchestraci, včetně všech funkcí aktivit seřazené podle doby provádění ve vzestupném pořadí.

![Dotaz Přehledy aplikací](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Souhrnný dotaz instance

Následující dotaz zobrazuje stav všech instancí orchestrace, které byly spuštěny v zadaném časovém rozsahu.

```AIQL
let start = datetime(2017-09-30T04:30:00);
traces
| where timestamp > start and timestamp < start + 1h
| where customDimensions.Category == "Host.Triggers.DurableTask"
| extend functionName = tostring(customDimensions["prop__functionName"])
| extend instanceId = tostring(customDimensions["prop__instanceId"])
| extend state = tostring(customDimensions["prop__state"])
| extend isReplay = tobool(tolower(customDimensions["prop__isReplay"]))
| extend output = tostring(customDimensions["prop__output"])
| where isReplay != true
| summarize arg_max(timestamp, *) by instanceId
| project timestamp, instanceId, functionName, state, output, appName = cloud_RoleName
| order by timestamp asc
```

Výsledkem je seznam ID instancí a jejich aktuální stav za běhu.

![Dotaz Přehledy aplikací](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>protokolování

Je důležité mít na paměti chování replay orchestrator při psaní protokolů přímo z funkce orchestrator. Zvažte například následující funkci orchestrator:

### <a name="precompiled-c"></a>Předkompilované C #

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="c-script"></a>Skript jazyka C#

```csharp
public static async Task Run(
    IDurableOrchestrationContext context,
    ILogger log)
{
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

### <a name="javascript-functions-20-only"></a>JavaScript (pouze funkce 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivity("F1");
    context.log("Calling F2.");
    yield context.df.callActivity("F2");
    context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

Výsledná data protokolu bude vypadat podobně jako následující příklad výstupu:

```txt
Calling F1.
Calling F1.
Calling F2.
Calling F1.
Calling F2.
Calling F3.
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Nezapomeňte, že zatímco protokoly tvrdí, že volá F1, F2 a F3, tyto funkce jsou *ve skutečnosti* volány pouze při prvním výskytu. Následné volání, ke kterým dojde během přehrávání jsou přeskočeny a výstupy jsou přehrány do logiky orchestrator.

Pokud se chcete přihlásit pouze k provádění bez přehrání, můžete `IsReplaying` `false`napsat podmíněný výraz do protokolu pouze v případě, že je . Vezměme si výše uvedený příklad, ale tentokrát s kontrolami přehrání.

#### <a name="precompiled-c"></a>Předkompilované C #

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="c"></a>C#

```cs
public static async Task Run(
    IDurableOrchestrationContext context,
    ILogger log)
{
    if (!context.IsReplaying) log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    if (!context.IsReplaying) log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    if (!context.IsReplaying) log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (pouze funkce 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    if (!context.df.isReplaying) context.log("Calling F1.");
    yield context.df.callActivity("F1");
    if (!context.df.isReplaying) context.log("Calling F2.");
    yield context.df.callActivity("F2");
    if (!context.df.isReplaying) context.log("Calling F3.");
    yield context.df.callActivity("F3");
    context.log("Done!");
});
```

Spuštění v trvanlivé funkce 2.0, .NET orchestrátor funkce mají také možnost vytvořit, `ILogger` který automaticky filtruje příkazy protokolu během přehrávání. Toto automatické filtrování se `IDurableOrchestrationContext.CreateReplaySafeLogger(ILogger)` provádí pomocí rozhraní API.

```csharp
[FunctionName("FunctionChain")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context,
    ILogger log)
{
    log = context.CreateReplaySafeLogger(log);
    log.LogInformation("Calling F1.");
    await context.CallActivityAsync("F1");
    log.LogInformation("Calling F2.");
    await context.CallActivityAsync("F2");
    log.LogInformation("Calling F3");
    await context.CallActivityAsync("F3");
    log.LogInformation("Done!");
}
```

S výše uvedenými změnami je výstup protokolu následující:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> Předchozí příklady jazyka C# jsou pro trvalé funkce 2.x. Pro trvalé funkce 1.x, `DurableOrchestrationContext` musíte `IDurableOrchestrationContext`použít místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

## <a name="custom-status"></a>Vlastní stav

Vlastní stav orchestrace umožňuje nastavit vlastní hodnotu stavu pro funkci orchestratoru. Tento stav je k dispozici prostřednictvím `IDurableOrchestrationClient.GetStatusAsync` rozhraní API dotazu stavu HTTP nebo rozhraní API. Vlastní stav orchestrace umožňuje bohatší monitorování funkcí orchestratoru. Například kód funkce orchestrator `IDurableOrchestrationContext.SetCustomStatus` může obsahovat volání k aktualizaci průběhu pro dlouhotrvající operaci. Klient, například webová stránka nebo jiný externí systém, by pak mohl pravidelně dotazovat na pole API dotazu http dotazu na bohatší informace o průběhu. Vzorek s `IDurableOrchestrationContext.SetCustomStatus` použitím je uveden níže:

### <a name="precompiled-c"></a>Předkompilované C #

```csharp
[FunctionName("SetStatusTest")]
public static async Task SetStatusTest([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

> [!NOTE]
> Předchozí příklad Jazyka C# je pro trvalé funkce 2.x. Pro trvalé funkce 1.x, `DurableOrchestrationContext` musíte `IDurableOrchestrationContext`použít místo . Další informace o rozdílech mezi verzemi naleznete v článku [verze durable functions.](durable-functions-versions.md)

### <a name="javascript-functions-20-only"></a>JavaScript (pouze funkce 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    const customStatus = { completionPercentage: 90.0, status: "Updating database records", };
    context.df.setCustomStatus(customStatus);

    // ...do more work...
});
```

Zatímco je orchestrace spuštěna, externí klienti mohou načíst tento vlastní stav:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Klienti obdrží následující odpověď:

```http
{
  "runtimeStatus": "Running",
  "input": null,
  "customStatus": { "completionPercentage": 90.0, "status": "Updating database records" },
  "output": null,
  "createdTime": "2017-10-06T18:30:24Z",
  "lastUpdatedTime": "2017-10-06T19:40:30Z"
}
```

> [!WARNING]
> Vlastní datová část stavu je omezena na 16 KB textu UTF-16 JSON, protože musí být možné vejít do sloupce Azure Table Storage. Externí úložiště můžete použít, pokud potřebujete větší datovou část.

## <a name="debugging"></a>Ladění

Azure Functions podporuje kód funkce ladění přímo a stejná podpora přenáší do trvalé funkce, ať už běží v Azure nebo místně. Existuje však několik chování, které je třeba znát při ladění:

* **Replay**: Orchestrator funkce pravidelně [přehrávat](durable-functions-orchestrations.md#reliability) při přijetí nových vstupů. Toto chování znamená, že jedno *logické* spuštění funkce orchestratoru může mít za následek vícenásobné stisknutí stejné zarážky, zejména pokud je nastaveno v rané fázi kódu funkce.
* **Await**: `await` Kdykoli je zjištěna funkce orchestrator, poskytuje řízení zpět k dispečer durable task framework. Pokud je poprvé zjištěna určitá, `await` přidružený úkol je *nikdy* obnovena. Vzhledem k tomu, že úloha nikdy pokračuje, *krokování přes* await (F10 v sadě Visual Studio) není možné. Krokování funguje pouze v případě, že je úloha přehrána.
* **Časové výčasové zprávy zasílání zpráv**: Trvalé funkce interně používá zprávy fronty řídit provádění orchestrator, aktivity a entity funkce. V prostředí s více virtuálními virtuálními aplikacemi může rozdělení ladění na delší dobu způsobit, že zprávu vyzvedne jiný virtuální virtuální soud, což povede k duplicitnímu spuštění. Toto chování existuje také pro pravidelné funkce aktivační události fronty, ale je důležité zdůraznit v tomto kontextu, protože fronty jsou podrobnosti implementace.
* **Zastavení a spuštění**: Zprávy v trvalé funkce přetrvávají mezi relacemi ladění. Pokud zastavíte ladění a ukončíte proces místního hostitele při provádění trvalé funkce, může se tato funkce automaticky spustit v budoucí relaci ladění. Toto chování může být matoucí, pokud není očekáván. Vymazání všech zpráv z [vnitřních front úložiště](durable-functions-perf-and-scale.md#internal-queue-triggers) mezi ladicími relacemi je jedna technika, jak se tomuto chování vyhnout.

> [!TIP]
> Při nastavování zarážek ve funkcích orchestratoru, pokud chcete přerušit pouze při provádění bez přehrání, můžete nastavit podmíněnou zarážku, která se přeruší pouze v případě, že `IsReplaying` je `false`.

## <a name="storage"></a>Úložiště

Ve výchozím nastavení trvalé funkce ukládá stav ve službě Azure Storage. Toto chování znamená, že můžete zkontrolovat stav orchestrations pomocí nástrojů, jako je [Například Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Snímek obrazovky Průzkumníka úložiště Azure](./media/durable-functions-diagnostics/storage-explorer.png)

To je užitečné pro ladění, protože přesně uvidíte, jaký stav může být orchestrace. Zprávy ve frontách lze také prozkoumat zjistit, jaké práce čeká na vyřízení (nebo zaseknutý v některých případech).

> [!WARNING]
> I když je vhodné zobrazit historii spuštění v úložišti tabulky, vyhněte se jakékoli závislosti na této tabulce. Může se změnit s tím, jak se vyvíjí rozšíření Durable Functions.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o monitorování ve službě Azure Functions](../functions-monitoring.md)
