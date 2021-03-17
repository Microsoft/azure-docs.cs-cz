---
title: Diagnostika v Durable Functions – Azure
description: Naučte se diagnostikovat problémy s rozšířením Durable Functions pro Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: azfuncdf
ms.openlocfilehash: 62cc5e1762a2a54b26cbebae5aa7cfbf64204ba5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584621"
---
# <a name="diagnostics-in-durable-functions-in-azure"></a>Diagnostika v Durable Functions v Azure

K dispozici je několik možností pro diagnostiku problémů s [Durable Functions](durable-functions-overview.md). Některé z těchto možností jsou stejné pro normální funkce a některé jsou jedinečné pro Durable Functions.

## <a name="application-insights"></a>Application Insights

[Application Insights](../../azure-monitor/app/app-insights-overview.md) je doporučený způsob, jak diagnostikovat a monitorovat v Azure Functions. Totéž platí pro Durable Functions. Přehled toho, jak ve vaší aplikaci Function App využít Application Insights, najdete v tématu [monitorování Azure Functions](../functions-monitoring.md).

Azure Functions trvalá přípona také generuje *sledovací události* , které umožňují sledovat komplexní provádění orchestrace. Tyto události sledování se dají najít a dotazovat pomocí nástroje [Application Insights Analytics](../../azure-monitor/logs/log-query-overview.md) v Azure Portal.

### <a name="tracking-data"></a>Sledování dat

Každá událost životního cyklu instance orchestrace způsobí, že se do kolekce **Traces** v Application Insights zapisuje událost sledování. Tato událost obsahuje datovou část **customDimensions** s několika poli.  Všechny názvy polí jsou společně s předponou `prop__` .

* **hubName**: název centra úloh, ve kterém jsou orchestrace spuštěné.
* **AppName**: název aplikace Function App. Toto pole je užitečné, pokud máte více aplikací Function App sdílejících stejnou instanci Application Insights.
* **slot**: [slot nasazení](../functions-deployment-slots.md) , ve kterém je spuštěná aktuální aplikace Function App. Toto pole je užitečné, pokud používáte nasazovací sloty pro správu verzí vašich orchestrací.
* název **funkce**: název funkce Orchestrator nebo Activity.
* **functionType**: typ funkce, jako je například **Orchestrator** nebo **aktivita**.
* **InstanceId**: jedinečné ID instance Orchestration.
* **stav**: stav provádění životního cyklu instance. Platné hodnoty zahrnují:
  * **Naplánováno**: funkce byla naplánována na provedení, ale ještě nebyla spuštěna.
  * **Zahájeno**: funkce začala běžet, ale ještě nebyla očekávána nebo dokončena.
  * **Očekáváno**: produkt Orchestrator naplánoval nějakou práci a čeká na jeho dokončení.
  * **Poslouchání**: Nástroj Orchestrator naslouchá externímu oznámení o události.
  * **Dokončeno**: funkce byla úspěšně dokončena.
  * **Selhalo**: funkce se nezdařila s chybou.
* **důvod**: další data přidružená k události sledování. Pokud například instance čeká na externí oznámení události, toto pole označuje název události, ke které čeká. Pokud se funkce nezdařila, bude toto pole obsahovat podrobnosti o chybě.
* **isReplay**: logická hodnota označující, zda je sledovací událost určena pro opakované spuštění.
* **extensionVersion**: verze rozšíření odolného úkolu. Informace o verzi jsou obzvláště důležitá data při vytváření sestav možných chyb v rozšíření. Dlouhotrvající instance mohou hlásit více verzí, pokud dojde k nějaké aktualizaci, je-li spuštěna.
* **sequenceNumber**: pořadové číslo provádění události. V kombinaci s časovým razítkem pomáhá seřadit události podle doby spuštění. *Všimněte si, že toto číslo se nastaví na hodnotu nula, pokud se hostitel při spuštění instance restartuje, takže je důležité vždy řadit podle časového razítka a pak sequenceNumber.*

Podrobnosti sledování dat emitovaných do Application Insights lze nakonfigurovat v `logger` části souboru (Functions 1. x) nebo `logging` (functions 2,0) `host.json` .

#### <a name="functions-10"></a>Funkce 1,0

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

#### <a name="functions-20"></a>Funkce 2,0

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

Chcete-li povolit generování podrobných událostí opětovného přehrání orchestrace, `LogReplayEvents` lze nastavit `true` v souboru v `host.json` části `durableTask` tak, jak je znázorněno v následujícím příkladu:

#### <a name="functions-10"></a>Funkce 1,0

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

#### <a name="functions-20"></a>Funkce 2,0

```json
{
    "extensions": {
        "durableTask": {
            "logReplayEvents": true
        }
    }
}
```

> [!NOTE]
> Ve výchozím nastavení je Application Insights telemetrie vzorkovat modulem runtime Azure Functions, aby nedocházelo k příliš častému generování dat. To může způsobit ztrátu informací o sledování, když dojde v krátké době k mnoha událostem životního cyklu. [Článek monitorování Azure Functions](../configure-monitoring.md#configure-sampling) vysvětluje, jak toto chování nakonfigurovat.

### <a name="single-instance-query"></a>Dotaz s jednou instancí

Následující dotaz ukazuje historické údaje o sledování pro jednu instanci orchestrace funkce [sekvence Hello](durable-functions-sequence.md) . Je napsaný pomocí [dotazovacího jazyka Kusto](/azure/data-explorer/kusto/query/). Vyfiltruje provádění opakovaného přehrání, aby se zobrazila pouze cesta *logického* spuštění. Události lze seřadit podle řazení podle `timestamp` a `sequenceNumber` jak je znázorněno v následujícím dotazu:

```kusto
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

![Dotaz seřazené na jednu instanci Application Insights](./media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)

### <a name="instance-summary-query"></a>Dotaz na souhrn instancí

Následující dotaz zobrazí stav všech instancí orchestrace, které byly spuštěny v určitém časovém rozsahu.

```kusto
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

![Dotaz na Application Insights s jednou instancí](./media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="durable-task-framework-logging"></a>Trvalé protokolování rozhraní úloh

Trvalé protokoly rozšíření jsou užitečné pro porozumění chování logiky orchestrace. Nicméně tyto protokoly neobsahují vždy dostatek informací pro ladění problémů s výkonem a spolehlivostí na úrovni architektury. Počínaje v v **2.3.0** trvalého rozšíření jsou pro kolekci k dispozici také protokoly vydávané základními rozhraními DTFx (trvalá úloha).

Při prohlížení protokolů vysílaných službou DTFx je důležité pochopit, že se modul DTFx skládá ze dvou součástí: Core Dispatch Engine ( `DurableTask.Core` ) a jeden z mnoha podporovaných zprostředkovatelů úložiště (Durable Functions používá standardně `DurableTask.AzureStorage` ).

* **DurableTask. Core**: obsahuje informace o provádění Orchestrace a plánování nízké úrovně.
* **DurableTask. AzureStorage**: obsahuje informace týkající se interakcí s Azure Storage artefakty, včetně vnitřních front, objektů BLOB a tabulek úložiště, které se používají k ukládání a načítání interního stavu orchestrace.

Tyto protokoly můžete povolit tak, že aktualizujete `logging/logLevel` částhost.jsaplikace Function App **na** soubor. Následující příklad ukazuje, jak povolit protokoly upozornění a chyb z obou `DurableTask.Core` i `DurableTask.AzureStorage` :

```json
{
  "version": "2.0",
  "logging": {
    "logLevel": {
      "DurableTask.AzureStorage": "Warning",
      "DurableTask.Core": "Warning"
    }
  }
}
```

Pokud máte povoleno Application Insights, budou tyto protokoly automaticky přidány do `trace` kolekce. Můžete je vyhledat stejným způsobem jako jiné `trace` protokoly pomocí dotazů Kusto.

> [!NOTE]
> U produkčních aplikací doporučujeme povolit `DurableTask.Core` a `DurableTask.AzureStorage` zaprotokolovat pomocí `"Warning"` filtru. Vyšší filtry s podrobnostmi, jako `"Information"` jsou velmi užitečné pro ladění problémů s výkonem. Tyto události protokolu jsou ale vysoké objemy a můžou významně zvýšit Application Insights náklady na úložiště dat.

Následující dotaz Kusto ukazuje, jak se dotázat na protokoly DTFx. Nejdůležitější část dotazu je `where customerDimensions.Category startswith "DurableTask"` , vzhledem k tomu, že filtruje výsledky do protokolů v `DurableTask.Core` `DurableTask.AzureStorage` kategoriích a.

```kusto
traces
| where customDimensions.Category startswith "DurableTask"
| project
    timestamp,
    severityLevel,
    Category = customDimensions.Category,
    EventId = customDimensions.EventId,
    message,
    customDimensions
| order by timestamp asc 
```
Výsledkem je sada protokolů zapsaných pomocí zprostředkovatelů protokolů rozhraní pro trvalé úlohy.

![Výsledky dotazu Application Insights DTFx](./media/durable-functions-diagnostics/app-insights-dtfx.png)

Další informace o tom, jaké události protokolu jsou k dispozici, najdete v [dokumentaci strukturovaného protokolování pro rozhraní úloh v GitHubu](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Core/Logging#durabletaskcore-logging).

## <a name="app-logging"></a>Protokolování aplikace

Je důležité, abyste při psaní protokolů přímo z funkce Orchestrator měli na paměti chování při přehrávání nástroje Orchestrator. Zvažte například následující funkci Orchestrator:

# <a name="c"></a>[C#](#tab/csharp)

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

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)
```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    logging.info("Calling F1.")
    yield context.call_activity("F1")
    logging.info("Calling F2.")
    yield context.call_activity("F2")
    logging.info("Calling F3.")
    yield context.call_activity("F3")
    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

Výsledná data protokolu budou vypadat přibližně podobně jako v následujícím příkladu výstupu:

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

Chcete-li pouze zapisovat protokoly při spuštění bez přehrání, můžete napsat podmíněný výraz pro protokolování pouze v případě, že je příznak "se přehráním" `false` . Vezměte v úvahu výše uvedený příklad, ale tentokrát s kontrolami opětovného přehrání.

# <a name="c"></a>[C#](#tab/csharp)

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

Počínaje Durable Functions 2,0 mají funkce .NET Orchestrator také možnost vytvořit `ILogger` , která při opakovaném přehrávání automaticky filtruje příkazy protokolu. Toto automatické filtrování se provádí pomocí rozhraní API [IDurableOrchestrationContext. CreateReplaySafeLogger (ILogger)](/dotnet/api/microsoft.azure.webjobs.extensions.durabletask.durablecontextextensions.createreplaysafelogger) .

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

> [!NOTE]
> Předchozí příklady jazyka C# jsou pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationContext` místo `IDurableOrchestrationContext` . Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    if not context.is_replaying:
        logging.info("Calling F1.")
    yield context.call_activity("F1")
    if not context.is_replaying:
        logging.info("Calling F2.")
    yield context.call_activity("F2")
    if not context.is_replaying:
        logging.info("Calling F3.")
    yield context.call_activity("F3")
    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

Ve výše zmíněných změnách výstup protokolu je následující:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

## <a name="custom-status"></a>Vlastní stav

Vlastní stav orchestrace umožňuje nastavit vlastní hodnotu stavu pro funkci Orchestrator. Tento vlastní stav je pak viditelný pro externí klienty prostřednictvím [rozhraní API pro dotazování na stav HTTP](durable-functions-http-api.md#get-instance-status) nebo prostřednictvím volání rozhraní API pro konkrétní jazyk. Vlastní stav orchestrace umožňuje rozšířené monitorování funkcí nástroje Orchestrator. Například kód funkce Orchestrator může vyvolat rozhraní API "nastavit vlastní stav" a aktualizovat průběh dlouhotrvající operace. Klient, jako je například webová stránka nebo jiný externí systém, by se pak mohl pravidelně dotazovat na rozhraní API pro dotazy na stav HTTP a získat tak lepší informace o průběhu. Vzorový kód pro nastavení vlastní hodnoty stavu ve funkci Orchestrator je uveden níže:

# <a name="c"></a>[C#](#tab/csharp)

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
> Předchozí příklad v jazyce C# je pro Durable Functions 2. x. Pro Durable Functions 1. x je nutné použít `DurableOrchestrationContext` místo `IDurableOrchestrationContext` . Další informace o rozdílech mezi verzemi najdete v článku o [Durable Functions verzích](durable-functions-versions.md) .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

# <a name="python"></a>[Python](#tab/python)

```python
import logging
import azure.functions as func
import azure.durable_functions as df

def orchestrator_function(context: df.DurableOrchestrationContext):
    # ...do work...

    # update the status of the orchestration with some arbitrary data
    custom_status = {'completionPercentage': 90.0, 'status': 'Updating database records'}
    context.set_custom_status(custom_status)
    # ...do more work...

    return None

main = df.Orchestrator.create(orchestrator_function)
```

---

I když je orchestrace spuštěná, externí klienti mohou načíst tento vlastní stav:

```http
GET /runtime/webhooks/durabletask/instances/instance123?code=XYZ

```

Klienti získají následující odpověď:

```json
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

* **Přehrát** znovu: funkce Orchestrator se pravidelně [přehrávají](durable-functions-orchestrations.md#reliability) , když se přijímají nové vstupy. Toto chování znamená, že jediné *logické* spuštění funkce Orchestrator může vést ke stejné zarážce několikrát, zejména pokud je nastaveno na začátku v kódu funkce.
* **Await**: pokaždé `await` , když se ve funkci Orchestrator narazí, vrátí řízení k trvalému dispečeru rozhraní úloh. Pokud se jedná o první `await` nalezenou konkrétní úlohu, přidružený úkol nebude *nikdy* obnoven. Vzhledem k tomu, že se úloha nikdy neobnoví *, krokování* na await (F10 v aplikaci Visual Studio) není možné. Krokování funguje pouze při opětovném přehrání úkolu.
* **Vypršení časových limitů zasílání zpráv**: Durable Functions interně používá zprávy ve frontě k řízení provádění funkcí Orchestrator, Activity a entity. V prostředí s více virtuálními počítači může rozdělení do ladění po dlouhou dobu způsobit, že jiný virtuální počítač tuto zprávu vybere a bude mít za následek duplicitní provádění. Toto chování existuje i u běžných funkcí triggeru fronty, ale je důležité, abyste v tomto kontextu odkazovali, protože fronty představují podrobnosti implementace.
* **Zastavování a spouštění**: zprávy v trvalých funkcích trvaly mezi relacemi ladění. Pokud zastavíte ladění a ukončíte proces místního hostitele, zatímco je vykonávána trvalá funkce, tato funkce se může v budoucí ladicí relaci znovu spustit automaticky. Toto chování může být matoucí, pokud není očekáváno. Pro zamezení tohoto chování je mazání všech zpráv z [interních front úložiště](durable-functions-perf-and-scale.md#internal-queue-triggers) mezi relacemi ladění jedním způsobem.

> [!TIP]
> Pokud při nastavování zarážek ve funkcích nástroje Orchestrator chcete provést pouze přerušení při neopakovaném spuštění, můžete nastavit podmíněný bod přerušení, který bude zalomen pouze v případě, že je hodnota "je přehrána" `false` .

## <a name="storage"></a>Storage

Ve výchozím nastavení Durable Functions ukládá stav do Azure Storage. Toto chování znamená, že můžete zkontrolovat stav orchestrace pomocí nástrojů, jako je [Průzkumník služby Microsoft Azure Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md).

![Snímek obrazovky Průzkumník služby Azure Storage](./media/durable-functions-diagnostics/storage-explorer.png)

To je užitečné pro ladění, protože vidíte přesně to, ve kterém stavu může být orchestrace. Zprávy ve frontách je také možné prozkoumat, aby se zjistilo, jaká práce čeká na vyřízení (nebo v některých případech zablokuje).

> [!WARNING]
> I když je praktické zobrazit historii spouštění v úložišti tabulek, vyhněte se pořizování jakékoli závislosti na této tabulce. Může se změnit, jak se vyvíjí rozšíření Durable Functions.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Další informace o monitorování v Azure Functions](../functions-monitoring.md)
