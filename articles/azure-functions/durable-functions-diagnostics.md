---
title: Diagnostika v Durable Functions – Azure
description: Zjistěte, jak diagnostikovat problémy s rozšíření Durable Functions pro službu Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 04/30/2018
ms.author: azfuncdf
ms.openlocfilehash: e1211241ec3a2b32647260d1a5c7dc561019cfdf
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092237"
---
# <a name="diagnostics-in-durable-functions-azure-functions"></a>Diagnostika v Durable Functions (Azure Functions)

Existuje několik možností pro diagnostiku problémů s [Durable Functions](durable-functions-overview.md). Některé z těchto možností jsou stejné pro běžné funkce a některé z nich jsou jedinečné pro Durable Functions.

## <a name="application-insights"></a>Application Insights

[Application Insights](../application-insights/app-insights-overview.md) je doporučeným způsobem, jak Diagnostika a monitorování ve službě Azure Functions. Totéž platí i pro Durable Functions. Přehled o tom, jak využívat Application Insights ve vaší aplikaci function app, naleznete v tématu [monitorování Azure Functions](functions-monitoring.md).

Trvalé rozšíření funkce Azure také vysílá *sledování událostí* , které umožňují sledování spuštění začátku do konce Orchestrace. Ty najdete Power pivotu a dotazované pomocí [Application Insights Analytics](../application-insights/app-insights-analytics.md) nástroj na webu Azure Portal.

### <a name="tracking-data"></a>Sledování dat

Každé události životního cyklu instance Orchestrace způsobí vygenerování události sledování má být proveden zápis **trasy** kolekce ve službě Application Insights. Tato událost obsahuje **customDimensions** datovou část s několika polí.  Názvy polí jsou před s `prop__`.

* **hubName**: název centra úloh, ve kterém běží vaše Orchestrace.
* **appName**: název aplikace function app. To je užitečné v případě, že máte více aplikací funkcí sdílení stejné instance služby Application Insights.
* **slotName**: [slot nasazení](https://blogs.msdn.microsoft.com/appserviceteam/2017/06/13/deployment-slots-preview-for-azure-functions/) v aktuální aplikaci function app běží. To je užitečné, když využít sloty nasazení na verzi vašeho Orchestrace.
* **functionName**: název funkce nástroje orchestrator nebo aktivity.
* **functiontype –**: typ funkce, jako například **Orchestrator** nebo **aktivity**.
* **instanceId**: Jedinečný ID instance Orchestrace.
* **Stav**: stav provádění životního cyklu instance. Platné hodnoty jsou:
    * **Naplánované**: funkce byla naplánována na spuštění, ale nebyl spuštěn dosud spuštěna.
    * **Spuštění**: funkce byla spuštěna, ale nebylo dosud očekáváno nebo dokončení.
    * **Očekáváno**: orchestrátoru je naplánováno úkony a čeká na její dokončení.
    * **Naslouchání**: orchestrátoru naslouchá oznámení o externí události.
    * **Dokončení**: funkce byla úspěšně dokončena.
    * **Nepovedlo**: funkce se nezdařila s chybou.
* **z důvodu**: další data přidružená k události sledování. Například pokud instanci čeká na oznámení o externí události, toto pole indikuje název události, kterou je čekání. V případě neúspěchu se funkce, to bude obsahovat podrobnosti o chybě.
* **isReplay**: logickou hodnotu označující, zda je událost sledování pro přehrály spuštění.
* **extensionVersion**: verze rozšíření trvalý úlohy. To je zvlášť důležitá data při hlášení případné chyby v rozšíření. Dlouho běžící instance může hlásit více verzí, pokud je spuštěný, dojde k aktualizaci. 
* **sequenceNumber**: číslo pořadí spuštění pro událost. V kombinaci s pomáhá časové razítko pro řazení událostí podle času spuštění. *Všimněte si, že toto číslo bude resetování nula, pokud hostitel restartuje je spuštěna instance, proto je důležité vždy seřadit podle časového razítka nejprve pak sequenceNumber.*

Úroveň podrobností pro sledování dat do služby Application Insights se dá nakonfigurovat v `logger` část `host.json` souboru.

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

Ve výchozím nastavení sledování všechny opakované události se vysílají. Lze snížit objem dat nastavením `Host.Triggers.DurableTask` k `"Warning"` nebo `"Error"` v takovém případě sledování událostí bude pouze měl vyzařovaného pro výjimečné situace.

Povolit generování události opakování podrobné Orchestrace, `LogReplayEvents` může být nastaven na `true` v `host.json` soubor `durableTask` znázorněno:

```json
{
    "durableTask": {
        "logReplayEvents": true
    }
}
```

> [!NOTE]
> Telemetrie Application Insights je ve výchozím nastavení, vzorkovat modulu runtime Azure Functions, aby se zabránilo příliš časté generování dat. To může způsobit informace o sledování ztratí, pokud v krátké době dojde k události životního cyklu. [Monitorování služby Azure Functions článku](functions-monitoring.md#configure-sampling) vysvětluje postup konfigurace tohoto chování.

### <a name="single-instance-query"></a>Jedna instance dotazu

Následující dotaz zobrazí sledování historických dat pro jednu instanci [Hello pořadí](durable-functions-sequence.md) funkce Orchestrace. To je zapsáno s použitím [Application Insights dotazu jazyka (AIQL)](https://docs.loganalytics.io/docs/Language-Reference). Odfiltruje spuštění přehrání takže jen *logické* je zobrazena cesta provedení. Události lze provést řazení podle řazení podle `timestamp` a `sequenceNumber` jak je znázorněno v následujícím dotazu: 

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

Výsledkem je seznam sledování událostí, který zobrazuje cesta provedení Orchestrace, včetně funkce všechny aktivity, seřazené podle času spuštění ve vzestupném pořadí.

![Dotaz Application Insights](media/durable-functions-diagnostics/app-insights-single-instance-ordered-query.png)


### <a name="instance-summary-query"></a>Souhrn dotazu instance

Následující dotaz zobrazí stav všech instancí Orchestrace, které byly spuštěny v zadaném časovém rozmezí.

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
Výsledkem je seznam ID instance a jejich aktuální stav modulu runtime.

![Dotaz Application Insights](media/durable-functions-diagnostics/app-insights-single-summary-query.png)

## <a name="logging"></a>Protokolování

Je důležité udržovat orchestrátoru chování opakování v úvahu při zápisu protokoly přímo z funkce orchestrátoru. Představte si třeba následující funkce nástroje orchestrator:

#### <a name="c"></a>C#

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    TraceWriter log)
{
    log.Info("Calling F1.");
    await ctx.CallActivityAsync("F1");
    log.Info("Calling F2.");
    await ctx.CallActivityAsync("F2");
    log.Info("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.Info("Done!");
}
```

#### <a name="javascript-functions-v2-only"></a>JavaScript (jenom funkce v2)

```javascript
const df = require("durable-functions");

module.exports = df(function*(context){
    context.log("Calling F1.");
    yield context.df.callActivityAsync("F1");
    context.log("Calling F2.");
    yield context.df.callActivityAsync("F2");
    context.log("Calling F3.");
    yield context.df.callActivityAsync("F3");
    context.log("Done!");
});
```

Výsledná data protokolu bude vypadat přibližně takto:

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
> Mějte na paměti, že deklarace bude volat F1, F2 a F3 protokoly, tyto funkce jsou pouze *skutečně* zavoláno poprvé jejich výskytu. Následná volání, ke kterým dochází při přehrávání se přeskočí a výstupy jsou odesílal do logiky nástroje orchestrator.

Pokud budete chtít přihlásit pouze provádění bez opětovného přehrání, podmíněný výraz můžete zapisovat do protokolu pouze tehdy, pokud `IsReplaying` je `false`. Vezměte v úvahu výše uvedený příklad, ale tentokrát s opakování kontroly.

```cs
public static async Task Run(
    DurableOrchestrationContext ctx,
    TraceWriter log)
{
    if (!ctx.IsReplaying) log.Info("Calling F1.");
    await ctx.CallActivityAsync("F1");
    if (!ctx.IsReplaying) log.Info("Calling F2.");
    await ctx.CallActivityAsync("F2");
    if (!ctx.IsReplaying) log.Info("Calling F3");
    await ctx.CallActivityAsync("F3");
    log.Info("Done!");
}
```
Díky této změně protokolu výstup vypadá takto:

```txt
Calling F1.
Calling F2.
Calling F3.
Done!
```

> [!NOTE]
> `IsReplaying` Vlastnost ještě není k dispozici v jazyce JavaScript.

## <a name="custom-status"></a>Vlastní stav

Stav vlastní Orchestrace umožňuje nastavit vlastní stav hodnotu funkce nástroje orchestrator. Tento stav se poskytuje prostřednictvím dotazu na stav protokolu HTTP rozhraní API nebo `DurableOrchestrationClient.GetStatusAsync` rozhraní API. Stav vlastní Orchestrace umožňuje bohatší monitorování pro funkce nástroje orchestrator. Například může obsahovat kód funkce orchestrátoru `DurableOrchestrationContext.SetCustomStatus` volání k aktualizaci průběhu pro dlouho běžící operace. Klientovi, například na webové stránce nebo jiné externí systém, může pak bude pravidelně odesílat dotazy dotazu stav protokolu HTTP rozhraní API pro bohatší informace o průběhu. Ukázkový používání `DurableOrchestrationContext.SetCustomStatus` jsou uvedeny níže:

```csharp
public static async Task SetStatusTest([OrchestrationTrigger] DurableOrchestrationContext ctx)
{
    // ...do work...

    // update the status of the orchestration with some arbitrary data
    var customStatus = new { completionPercentage = 90.0, status = "Updating database records" };
    ctx.SetCustomStatus(customStatus);

    // ...do more work...
}
```

Je spuštěn orchestraci, můžete načíst externí klienti tento vlastní stav:

```http
GET /admin/extensions/DurableTaskExtension/instances/instance123

```

Klienti získáte odpovědi na následující: 

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
>  Datová část vlastní stav je omezena na 16 KB text JSON UTF-16, protože se musí být schopni vyplňoval sloupec Azure Table Storage. Externí úložiště můžete použít, pokud potřebujete větší datovou část.

## <a name="debugging"></a>Ladění

Ladění přímo v kódu funkce Azure Functions podporuje a které podporují stejné přenesou do Durable Functions, jestli běží v Azure nebo místně. Existuje však několik chování je potřeba vědět při ladění:

* **Přehrát**: funkce Orchestrátoru pravidelně opakované přehrání při přijetí nové vstupů. To znamená, že jeden *logické* spuštění funkce orchestrátoru může vést k dosažení stejného zarážku více než jednou, zejména v případě, že je nastavena v rané fázi kód funkce.
* **Operátor await**: pokaždé, když `await` je nalezen, bude vrácen řízení zpět do dispečera trvalý Framework úloh. Pokud je to poprvé konkrétní `await` byl nalezen, je přidruženého úkolu *nikdy* obnoveno. Protože úloha nikdy obnoví, krokování *přes* await (F10 v sadě Visual Studio) není ve skutečnosti je to možné. Krokování přes funguje pouze v případě znovu přehrát úkolu.
* **Zasílání zpráv vypršení časových limitů**: odolná služba Functions interně používá fronta zpráv do jednotky provádění funkcí nástroje orchestrator a funkce aktivity. V prostředí více virtuálních počítačů rozdělení do ladění pro dlouhou dobu způsobit jinému virtuálnímu počítači, aby se získaly zpráva, což vede k duplicitní spuštění. Toto chování pro pravidelné aktivační událost fronty funkce také existuje, ale je důležité zdůraznit v tomto kontextu, protože fronty jsou podrobnosti implementace.

> [!TIP]
> Při nastavení zarážek, pokud chcete přerušit pouze na provádění bez opakování, můžete nastavit podmíněné zarážky, že pouze pokud konce `IsReplaying` je `false`.

## <a name="storage"></a>Úložiště

Ve výchozím nastavení Durable Functions ukládá stav ve službě Azure Storage. To znamená, že si můžete prohlédnout stav vaší Orchestrace pomocí nástrojů, jako [Microsoft Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

![Snímek obrazovky Azure Storage Exploreru](media/durable-functions-diagnostics/storage-explorer.png)

To je užitečné pro ladění, protože se zobrazí přesně jednotlivých stavech Orchestrace může být v. Zprávy ve frontách se dají prozkoumat také se dozvíte, jaká práce čeká na vyřízení (nebo zablokuje a v některých případech).

> [!WARNING]
> I když je vhodné zobrazit historii provádění ve službě table storage, vyhnete se tak všechny závislosti pro tuto tabulku. To může změnit, protože rozšíření Durable Functions vyvíjí.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Další informace o použití trvalý časovače](durable-functions-timers.md)
