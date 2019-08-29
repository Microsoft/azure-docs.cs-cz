---
title: Diagnostika v Durable Functions – Azure
description: Naučte se diagnostikovat problémy s rozšířením Durable Functions pro Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 2cc60ee2c73aa6858f68d6b13a895a0188bb5735
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70098133"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnostika v Durable Functions v Azure

K dispozici je několik možností pro diagnostiku problémů s [Durable Functions](durable-functions-overview.md). Některé z těchto možností jsou stejné pro normální funkce a některé jsou jedinečné pro Durable Functions.

## <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) je doporučený způsob, jak diagnostikovat a monitorovat v Azure Functions. Totéž platí pro Durable Functions. Přehled toho, jak ve vaší aplikaci Function App využít Application Insights, najdete v tématu [monitorování Azure Functions](../functions-monitoring.md).

Azure Functions trvalá přípona také generuje *sledovací události* , které umožňují sledovat komplexní provádění orchestrace. Ty najdete a dotazují se pomocí nástroje [Application Insights Analytics](../../azure-monitor/app/analytics.md) v Azure Portal.

### <a name="tracking-data"></a>Sledování dat

Každá událost životního cyklu instance orchestrace způsobí, že se do kolekce Traces v Application Insights zapisuje událost sledování. Tato událost obsahuje datovou část **customDimensions** s několika poli.  Všechny názvy polí jsou společně s `prop__`předponou.

* **hubName**: Název centra úloh, ve kterém jsou orchestrace spuštěny.
* **appName**: Název aplikace Function App To je užitečné v případě, že máte více aplikací funkcí, které sdílejí stejnou instanci Application Insights.
* **slotName**: [Slot pro nasazení](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) , ve kterém je spuštěná aktuální aplikace Function App To je užitečné v případě, že použijete sloty nasazení na verzi vašich orchestrací.
* **funkce Function**: Název funkce Orchestrator nebo Activity.
* **functionType**: Typ funkce, například **Orchestrator** nebo **aktivita**.
* **instanceId**: Jedinečné ID instance Orchestration.
* **stav**: Stav provádění životního cyklu instance. Platné hodnoty jsou:
  * Naplánovalo se: Funkce byla naplánována na spuštění, ale ještě nebyla spuštěna.
  * **Zahájeno**: Funkce začala běžet, ale ještě nebyla očekávána nebo dokončena.
  * **Očekáváno**: Nástroj Orchestrator naplánoval nějakou práci a čeká na její dokončení.
  * **Naslouchání**: Nástroj Orchestrator naslouchá externímu oznámení o události.
  * **Dokončeno**: Funkce byla úspěšně dokončena.
  * **Selhalo**: Funkce se nezdařila s chybou.
* **důvod**: Další data přidružená k události sledování Pokud například instance čeká na externí oznámení události, toto pole označuje název události, ke které čeká. Pokud se funkce nezdařila, bude obsahovat podrobnosti o chybě.
* **isReplay**: Logická hodnota označující, zda je sledovací událost určena pro opakované spuštění.
* **extensionVersion**: Verze trvalého rozšíření úlohy. To jsou obzvláště důležitá data při hlášení možných chyb v rozšíření. Dlouhotrvající instance mohou hlásit více verzí, pokud dojde k nějaké aktualizaci, je-li spuštěna.
* **sequenceNumber**: Pořadové číslo provádění události V kombinaci s časovým razítkem pomáhá seřadit události podle doby spuštění. *Všimněte si, že toto číslo se nastaví na hodnotu nula, pokud se hostitel při spuštění instance restartuje, takže je důležité vždy řadit podle časového razítka a pak sequenceNumber.*

Podrobnosti sledování dat emitovaných do Application Insights lze konfigurovat v `logger` oddílu (Functions 1. x) nebo `logging` (Functions 2. `host.json` x) v souboru.

#### <a name="functions-1x"></a>Functions 1.x

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

#### <a name="functions-2x"></a>Functions 2.x

```json
{
    "logging": {
        "logLevel": {
          "Host.Triggers.DurableTask": "Information",
        },
    }
}
```

Ve výchozím nastavení jsou vygenerovány všechny události sledování bez přehrání. Objem dat se dá snížit nastavením `Host.Triggers.DurableTask` na `"Warning"` nebo `"Error"` v jakém se budou události sledování případu vysílat jenom pro výjimečné situace.

Chcete-li povolit generování podrobných událostí opětovného přehrání orchestrace `LogReplayEvents` , lze `true` nastavit v `host.json` souboru v části `durableTask` tak, jak je znázorněno v následujícím příkladu:

#### <a name="functions-1x"></a>Functions 1.x

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-2x"></a>Functions 2.x

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
> Ve výchozím nastavení je Application Insights telemetrie vzorkovat modulem runtime Azure Functions, aby nedocházelo k příliš častému generování dat. To může způsobit ztrátu informací o sledování, když dojde v krátké době k mnoha událostem životního cyklu. [Článek monitorování Azure Functions](../functions-monitoring.md#configure-sampling) vysvětluje, jak toto chování nakonfigurovat.

### <a name="single-instance-query"></a>Dotaz s jednou instancí

Následující dotaz ukazuje historické údaje o sledování pro jednu instanci orchestrace funkce [sekvence Hello](durable-functions-sequence.md) . Je napsaný pomocí [jazyka AIQL (Application Insights Query Language)](https://aka.ms/LogAnalyticsLanguageReference). Vyfiltruje provádění opakovaného přehrání, aby se zobrazila pouze cesta logického spuštění. Události lze seřadit podle řazení podle `timestamp` a `sequenceNumber` jak je znázorněno v následujícím dotazu:

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

Výsledkem je seznam sledovacích událostí, které zobrazují cestu provádění orchestrace, včetně všech funkcí aktivity seřazených podle času spuštění ve vzestupném pořadí.

![Application Insights dotaz](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Dotaz na souhrn instancí

Následující dotaz zobrazí stav všech instancí orchestrace, které byly spuštěny v určitém časovém rozsahu.

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

Výsledkem je seznam ID instancí a jejich aktuální stav modulu runtime.

![Application Insights dotaz](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Protokolování

Je důležité, abyste při psaní protokolů přímo z funkce Orchestrator měli na paměti chování při přehrávání nástroje Orchestrator. Zvažte například následující funkci Orchestrator:

### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
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

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

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

Výsledná data protokolu budou vypadat přibližně takto:

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
> Mějte na paměti, že zatímco protokoly přidávají volání F1, F2 a F3, jsou tyto funkce *ve skutečnosti* volány pouze při prvním výskytu. Následná volání, ke kterým dojde během opakovaného přehrávání, se přeskočí a výstupy se přehrají do logiky nástroje Orchestrator.

Pokud chcete přihlašovat pouze při neopakovaném spuštění, můžete napsat podmíněný výraz pro protokolování pouze v případě `IsReplaying` , že je. `false` Vezměte v úvahu výše uvedený příklad, ale tentokrát s kontrolami opětovného přehrání.

#### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext context,
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

#### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

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

V důsledku této změny je výstup protokolu následující:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>Vlastní stav

Vlastní stav orchestrace umožňuje nastavit vlastní hodnotu stavu pro funkci Orchestrator. Tento stav se poskytuje prostřednictvím rozhraní API pro dotazování na stav `DurableOrchestrationClient.GetStatusAsync` http nebo rozhraní API. Vlastní stav orchestrace umožňuje rozšířené monitorování funkcí nástroje Orchestrator. Například kód funkce nástroje Orchestrator může zahrnovat `DurableOrchestrationContext.SetCustomStatus` volání pro aktualizaci průběhu dlouhotrvající operace. Klient, jako je například webová stránka nebo jiný externí systém, by se pak mohl pravidelně dotazovat na rozhraní API pro dotazy na stav HTTP a získat tak lepší informace o průběhu. Ukázka použití `DurableOrchestrationContext.SetCustomStatus` je k dispozici níže:

### <a name="c"></a>C#

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext context)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    context.SetCustomStatus(customStatus);

    // ...do more work...
}
```

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

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

I když je orchestrace spuštěná, externí klienti mohou načíst tento vlastní stav:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Klienti získají následující odpověď:

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
> Vlastní datová část stavu je omezená na 16 KB textu JSON ve formátu UTF-16, protože musí být schopná se umístit do sloupce Azure Table Storage. Pokud potřebujete větší datovou část, můžete použít externí úložiště.

## <a name="debugging"></a>Ladění

Azure Functions podporuje přímo ladění kódu funkce a tato podpora přenáší do Durable Functions, ať už běží v Azure nebo lokálně. Existuje však několik chování, která je potřeba znát při ladění:

* **Přehrát**znovu: Funkce nástroje Orchestrator se pravidelně přehrávají při přijetí nových vstupů. To znamená, že jediné *logické* spuštění funkce Orchestrator může způsobit, že se stejnou zarážku zavede několikrát, zejména pokud je nastavená na začátku v kódu funkce.
* **Očekává**se: Pokaždé `await` , když dojde k nějakému, vrátí řízení k trvalému dispečeru rozhraní úloh. Pokud se jedná o první zjištění určitého konkrétního `await` úkolu, přidružený úkol nebude *nikdy* obnoven. Vzhledem k tomu, že úloha nikdy nepokračuje, krokování na await (F10 v aplikaci Visual Studio) není ve skutečnosti možné. Krokování funguje pouze při opětovném přehrání úkolu.
* **Vypršení časových limitů zasílání zpráv**: Durable Functions interně používá zprávy ve frontě k řízení provádění funkcí Orchestrator i funkcí aktivit. V prostředí s více virtuálními počítači může rozdělení do ladění po dlouhou dobu způsobit, že jiný virtuální počítač tuto zprávu vybere a bude mít za následek duplicitní provádění. Toto chování existuje i u běžných funkcí triggeru fronty, ale je důležité, abyste v tomto kontextu odkazovali, protože fronty představují podrobnosti implementace.

> [!TIP]
> Pokud chcete při nastavování zarážek provést pouze přerušení při neopakovaném spuštění, můžete nastavit podmíněnou zarážku, která bude rozdělena `false`pouze v případě `IsReplaying` , že je.

## <a name="storage"></a>Storage

Ve výchozím nastavení Durable Functions ukládá stav do Azure Storage. To znamená, že můžete zkontrolovat stav orchestrace pomocí nástrojů, jako je [Průzkumník služby Microsoft Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Snímek obrazovky Průzkumník služby Azure Storage](./media/durable-functions-diagnostics/storage-explorer.png)

To je užitečné pro ladění, protože vidíte přesně to, ve kterém stavu může být orchestrace. Zprávy ve frontách je také možné prozkoumat, aby se zjistilo, jaká práce čeká na vyřízení (nebo v některých případech zablokuje).

> [!WARNING]
> I když je praktické zobrazit historii spouštění v úložišti tabulek, vyhněte se pořizování jakékoli závislosti na této tabulce. Může se změnit, jak se vyvíjí rozšíření Durable Functions.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Naučte se používat odolné časovače.](durable-functions-timers.md)
