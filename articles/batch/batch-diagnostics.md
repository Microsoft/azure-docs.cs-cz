---
title: Metriky, výstrahy a diagnostické protokoly
description: Zaznamenává a analyzuje události diagnostického protokolu pro prostředky Azure Batch účtů, jako jsou fondy a úkoly.
ms.topic: how-to
ms.date: 12/05/2018
ms.custom: seodec18
ms.openlocfilehash: 0a33f71cd185a327bfe6852b9acd7d7317b94c2c
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726736"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Metriky, výstrahy a protokoly služby Batch pro vyhodnocení a monitorování diagnostiky

 
Tento článek vysvětluje, jak monitorovat účet Batch pomocí funkcí [Azure monitor](../azure-monitor/overview.md). Azure Monitor shromažďuje [metriky](../azure-monitor/platform/data-platform-metrics.md) a [diagnostické protokoly](../azure-monitor/platform/platform-logs-overview.md) pro prostředky v účtu Batch. Shromažďovat a spotřebovávat tato data různými způsoby, jak monitorovat účet Batch a diagnostikovat problémy. Můžete také nakonfigurovat [Upozornění na metriky](../azure-monitor/platform/alerts-overview.md) , abyste obdrželi oznámení, když metrika dosáhne zadané hodnoty. 

## <a name="batch-metrics"></a>Metriky dávky

Metriky jsou data telemetrie Azure (označují se taky jako čítače výkonu), která vycházejí z vašich prostředků Azure, která služba Azure Monitor Service spotřebovává. Mezi příklady metrik v účtu Batch patří: Vytvoření fondu událostí, počet uzlů s nízkou prioritou a události dokončení úlohy. 

Podívejte se na [seznam podporovaných metrik dávky](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Metriky jsou:

* Ve výchozím nastavení povolených v každém účtu Batch bez další konfigurace
* Vygenerováno každou 1 minutu
* Netrvalá automaticky, ale má 30denní historii průběžných změn. Metriky aktivity můžete zachovat jako součást protokolování diagnostiky.

### <a name="view-metrics"></a>Zobrazit metriky

Zobrazte metriky pro účet Batch v Azure Portal. Stránka **Přehled** pro účet ve výchozím nastavení zobrazuje metriky klíčových uzlů, jader a úloh. 

Zobrazení všech metrik účtu Batch: 

1. Na portálu klikněte na **všechny služby**  >  **účty Batch**a potom klikněte na název vašeho účtu Batch.
2. V části **monitorování**klikněte na **metriky**.
3. Vyberte jednu nebo více metrik. Pokud chcete, vyberte další metriky prostředků pomocí rozevíracích seznamů **předplatná**, **Skupina prostředků**, **typ prostředku**a **prostředky** .
    * Pro metriky založené na počtu (například "vyhrazený počet jader" nebo "počet uzlů s nízkou prioritou") použijte agregaci "průměr". Pro metriky založené na událostech (například "velikost fondu dokončené události") použijte agregaci Count (počet).

> [!WARNING]
> Nepoužívejte agregaci Sum, která sečte hodnoty všech datových bodů přijatých za období grafu.
> 
> 

    ![Batch metrics](media/batch-diagnostics/metrics-portal.png)

Pokud chcete metriky načíst programově, použijte rozhraní API Azure Monitor. Příklad naleznete v tématu [načtení Azure monitor metriky pomocí technologie .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-reliability"></a>Spolehlivost metriky dávky

Metriky jsou určené pro použití při trendech a analýze dat. Doručení metriky není zaručené a podléhá doručování, ztrátě a odstraňování duplicitních dat. Použití jednoduchých událostí pro upozornění nebo Trigger funkcí se nedoporučuje. Další podrobnosti o tom, jak nastavit prahové hodnoty pro upozorňování, najdete v části [výstrahy metriky služby Batch](#batch-metric-alerts) .

Metriky emitované za poslední 3 minuty se stále můžou agregovat. Během tohoto časového rámce mohou být hodnoty metriky podsestavně.

## <a name="batch-metric-alerts"></a>Výstrahy metriky dávky

Volitelně můžete nakonfigurovat *výstrahy metriky* téměř v reálném čase, které se aktivují, když hodnota zadané metriky překračuje prahovou hodnotu, kterou přiřadíte. Výstraha vygeneruje [oznámení](../monitoring-and-diagnostics/insights-alerts-portal.md) , které jste si zvolili, když je výstraha "aktivována" (při překročení prahové hodnoty a při splnění podmínky upozornění), a také v případě, že se jedná o "Vyřešený" (Pokud se prahová hodnota opakuje a podmínka už není splněná). Výstrahy na základě jednotlivých datových bodů se nedoporučují, protože metriky podléhají doručování, ztrátě dat a duplicitám. Při upozorňování by se měly pro tyto nekonzistence použít prahové hodnoty.

Můžete například chtít nakonfigurovat výstrahu metriky, když počet jader s nízkou prioritou spadá na určitou úroveň, abyste mohli upravit složení fondů. Doporučuje se nastavit období 10 nebo více minut, kde výstrahy aktivují, pokud průměrný počet jader s nízkou prioritou klesne pod prahovou hodnotu pro celé období. Nedoporučujeme upozorňování na období 1-5 minut, protože metriky stále můžou být agregovány.

Konfigurace upozornění na metriku na portálu:

1. Klikněte na **všechny služby**  >  **účty Batch**a potom klikněte na název vašeho účtu Batch.
2. V části **monitorování**klikněte na **pravidla výstrah**  >  **Přidat výstrahu metriky**.
3. Vyberte metriku, podmínku výstrahy (například když metrika překročí určitou hodnotu během období) a jedno nebo více oznámení.

Pomocí [REST API](https://docs.microsoft.com/rest/api/monitor/)můžete také nakonfigurovat upozornění téměř v reálném čase. Další informace najdete v tématu [Přehled výstrah](../azure-monitor/platform/alerts-overview.md). Pokud chcete do výstrah zahrnout informace o úlohách, úkolech nebo fondech, přečtěte si informace o vyhledávacích dotazech v tématu [reakce na události s výstrahami Azure monitor](../azure-monitor/learn/tutorial-response.md) .

## <a name="batch-diagnostics"></a>Diagnostika služby Batch

Diagnostické protokoly obsahují informace vydávané prostředky Azure, které popisují operace každého prostředku. V případě služby Batch můžete shromažďovat následující protokoly:

* **Služba protokoluje** události generované službou Azure Batch během doby životnosti jednotlivých prostředků služby Batch, jako je fond nebo úloha. 

* **Metriky** se protokolují na úrovni účtu. 

Nastavení pro povolení shromažďování diagnostických protokolů není ve výchozím nastavení povolené. Explicitně povolte nastavení diagnostiky pro každý účet Batch, který chcete monitorovat.

### <a name="log-destinations"></a>Cíle protokolu

Běžným scénářem je výběr účtu Azure Storage jako cíle protokolu. Chcete-li ukládat protokoly v Azure Storage, vytvořte účet před povolením shromažďování protokolů. Pokud jste k účtu Batch přihlásili účet úložiště, můžete tento účet zvolit jako cíl protokolu. 

Další volitelné cíle pro diagnostické protokoly:

* Umožňuje streamovat události protokolu diagnostiky služby Batch do [centra událostí Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Event Hubs může ingestovat miliony událostí za sekundu, které pak můžete transformovat a ukládat pomocí libovolného zprostředkovatele analýz v reálném čase. 

* Odešlete diagnostické protokoly do [protokolů Azure monitor](../log-analytics/log-analytics-overview.md), kde je můžete analyzovat nebo exportovat pro účely analýzy v Power BI nebo Excelu.

> [!NOTE]
> Pro ukládání nebo zpracování dat protokolů diagnostiky se službami Azure můžete účtovat další náklady. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Povolit shromažďování diagnostických protokolů Batch

1. Na portálu klikněte na **všechny služby**  >  **účty Batch**a potom klikněte na název vašeho účtu Batch.
2. V části **monitorování**klikněte na **diagnostické protokoly**  >  **zapnout diagnostiku**.
3. V okně **nastavení diagnostiky**zadejte název nastavení a vyberte cíl protokolu (existující účet úložiště, centrum událostí nebo protokoly Azure monitor). Vyberte buď **ServiceLog** a **AllMetrics**.

    Když vyberete účet úložiště, můžete volitelně nastavit zásady uchovávání informací. Pokud nezadáte počet dní pro uchování, data se zachovají během životnosti účtu úložiště.

4. Klikněte na **Uložit**.

    ![Diagnostika služby Batch](media/batch-diagnostics/diagnostics-portal.png)

Další možnosti povolení shromažďování protokolů: pomocí Azure Monitor na portálu můžete nakonfigurovat nastavení diagnostiky, použít [šablonu správce prostředků](../azure-monitor/platform/diagnostic-settings-template.md)nebo použít Azure PowerShell nebo rozhraní příkazového řádku Azure CLI. viz [shromažďování a zpracování dat protokolu z vašich prostředků Azure](../azure-monitor/platform/platform-logs-overview.md).


### <a name="access-diagnostics-logs-in-storage"></a>Přístup k diagnostickým protokolům v úložišti

Pokud archivujete diagnostické protokoly služby Batch v účtu úložiště, vytvoří se v účtu úložiště kontejner úložiště hned po výskytu související události. Objekty BLOB se vytvářejí podle následujícího vzoru názvů:

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
Příklad:

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
Každý `PT1H.json` soubor objektu BLOB obsahuje události ve formátu JSON, ke kterým došlo během hodiny zadané v adrese URL objektu BLOB (například `h=12` ). Během této hodiny se události připojí k `PT1H.json` souboru, když k nim dojde. Hodnota minute ( `m=00` ) je vždy `00` , protože události diagnostického protokolu jsou v jednotlivých objektech blob za hodinu rozdělené. (Všechny časy jsou v UTC.)

Níže je uveden příklad `PoolResizeCompleteEvent` položky v `PT1H.json` souboru protokolu. Obsahuje informace o aktuálním a cílovém počtu vyhrazených uzlů a uzlů s nízkou prioritou a také o počátečním a koncovém času operace:

```
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Další informace o schématu diagnostických protokolů v účtu úložiště najdete v tématu [archivace diagnostických protokolů Azure](../azure-monitor/platform/resource-logs-collect-storage.md#schema-of-platform-logs-in-storage-account). Pokud chcete získat přístup k protokolům v účtu úložiště programově, použijte rozhraní API pro úložiště. 

### <a name="service-log-events"></a>Události protokolu služby
Protokoly služby Azure Batch, pokud jsou shromažďovány, obsahují události vydávané službou Azure Batch během životnosti jednotlivého prostředku služby Batch, jako je například fond nebo úloha. Každá událost generovaná dávkou je protokolována ve formátu JSON. Jedná se například o tělo **události vytvoření**ukázkového fondu:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "5",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicatedComputeNodes": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Služba Batch aktuálně generuje následující události protokolu služby. Tento seznam nemusí být vyčerpávající, protože se od poslední aktualizace tohoto článku mohla přidat další události.

| **Události protokolu služby** |
| --- |
| [Vytvoření fondu](batch-pool-create-event.md) |
| [Spuštění odstranění fondu](batch-pool-delete-start-event.md) |
| [Odstranění fondu bylo dokončeno.](batch-pool-delete-complete-event.md) |
| [Začátek změny velikosti fondu](batch-pool-resize-start-event.md) |
| [Změna velikosti fondu dokončena](batch-pool-resize-complete-event.md) |
| [Začátek úlohy](batch-task-start-event.md) |
| [Úloha dokončena](batch-task-complete-event.md) |
| [Selhání úlohy](batch-task-fail-event.md) |



## <a name="next-steps"></a>Další kroky

* Další informace o dostupných [rozhraních API a nástrojích služby Batch](batch-apis-tools.md) pro sestavování řešení Batch.
* Přečtěte si další informace o [monitorování řešení Batch](monitoring-overview.md).
