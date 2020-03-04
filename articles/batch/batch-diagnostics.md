---
title: Metriky, výstrahy a diagnostické protokoly – Azure Batch | Microsoft Docs
description: Zaznamenávat a analyzovat události diagnostických protokolů pro účet prostředky služby Azure Batch, jako jsou fondy a úlohy.
services: batch
documentationcenter: ''
author: LauraBrenner
manager: evansma
editor: ''
ms.assetid: ''
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 12/05/2018
ms.author: labrenne
ms.custom: seodec18
ms.openlocfilehash: 68d5976a5a79dbde88b7f80b02b39793ffc86de9
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2020
ms.locfileid: "78254860"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batch metriky, výstrahy a protokoly pro diagnostické hodnocení a monitorování

 
Tento článek vysvětluje, jak monitorovat účet Batch pomocí funkcí [Azure monitor](../azure-monitor/overview.md). Azure Monitor shromažďuje [metriky](../azure-monitor/platform/data-platform-metrics.md) a [diagnostické protokoly](../azure-monitor/platform/platform-logs-overview.md) pro prostředky v účtu Batch. Shromažďování a používání těchto dat různými způsoby, jak monitorovat účtu Batch a diagnostikovat problémy s. Můžete také nakonfigurovat [Upozornění na metriky](../azure-monitor/platform/alerts-overview.md) , abyste obdrželi oznámení, když metrika dosáhne zadané hodnoty. 

## <a name="batch-metrics"></a>Metriky služby batch

Metriky jsou Azure telemetrická data (také nazývané čítače výkonu), protože ho vygeneroval vašich prostředků Azure, které se spotřebovávají služby Azure Monitor. Zahrnout metriky příklad v účtu Batch: události vytvoření fondu, počet uzlů s nízkou prioritou a události dokončení úkolu. 

Podívejte se na [seznam podporovaných metrik dávky](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Metriky jsou:

* Povolené ve výchozím nastavení v každém účtu Batch bez další konfigurace
* Vygeneruje každou 1 minutu
* Není trvalý automaticky, ale historie se zajištěním provozu na 30 dnů. Metriky aktivity můžete zachovat jako součást protokolování diagnostiky.

### <a name="view-metrics"></a>Zobrazit metriky

Zobrazit metriky pro účet Batch na webu Azure Portal. Stránka **Přehled** pro účet ve výchozím nastavení zobrazuje metriky klíčových uzlů, jader a úloh. 

Chcete-li zobrazit všechny metriky pro účet Batch: 

1. Na portálu klikněte na **všechny služby** > **účty Batch**a potom klikněte na název vašeho účtu Batch.
2. V části **monitorování**klikněte na **metriky**.
3. Vyberte jednu nebo několik metrik. Pokud chcete, vyberte další metriky prostředků pomocí rozevíracích seznamů **předplatná**, **Skupina prostředků**, **typ prostředku**a **prostředky** .
    * Pro metriky založené na počtu (například "vyhrazený počet jader" nebo "počet uzlů s nízkou prioritou") použijte agregaci "průměr". Pro metriky založené na událostech (například "velikost fondu dokončené události") použijte agregaci Count (počet).

> [!WARNING]
> Nepoužívejte agregaci Sum, která sečte hodnoty všech datových bodů přijatých za období grafu.
> 
> 

    ![Batch metrics](media/batch-diagnostics/metrics-portal.png)

Pokud chcete načíst metriky prostřednictvím kódu programu, pomocí rozhraní API služby Azure Monitor. Příklad naleznete v tématu [načtení Azure monitor metriky pomocí technologie .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-reliability"></a>Metriky spolehlivost služby batch

Metriky jsou určena pro použití ke sledování trendů a analýze dat. Metriky doručení není zaručeno, že a je náchylné ke mimo pořadí doručení, ztrátu dat a/nebo duplicity. Pomocí jedné události upozornění nebo aktivační události funkcí se nedoporučuje. Další podrobnosti o tom, jak nastavit prahové hodnoty pro upozorňování, najdete v části [výstrahy metriky služby Batch](#batch-metric-alerts) .

Metriky za posledních 3 minut, protože ho může být stále agregaci. Během tohoto časového rámce může být underreported hodnoty metrik.

## <a name="batch-metric-alerts"></a>Upozornění na metriky služby batch

Volitelně můžete nakonfigurovat *výstrahy metriky* téměř v reálném čase, které se aktivují, když hodnota zadané metriky překračuje prahovou hodnotu, kterou přiřadíte. Výstraha vygeneruje [oznámení](../monitoring-and-diagnostics/insights-alerts-portal.md) , které jste si zvolili, když je výstraha "aktivována" (při překročení prahové hodnoty a při splnění podmínky upozornění), a také v případě, že se jedná o "Vyřešený" (Pokud se prahová hodnota opakuje a podmínka už není splněná). Výstrahy na základě jednotlivých datových bodů se nedoporučuje, protože metriky jsou v souladu s mimo pořadí doručení, ztrátu dat a/nebo duplicity. Výstrahy se ujistěte, použití prahových hodnot pro tyto nekonzistence.

Například můžete chtít konfigurovat upozornění na metriku při vaší počtu jader s nízkou prioritou spadá do určité úrovně, tak můžete upravit složení fondech. Doporučujeme nastavit 10 minut, kde aktivovat výstrahy v případě počtu jader s nízkou prioritou průměrné klesne pod prahovou hodnotu pro celou dobu. Není doporučeno upozornění na dobu 1 – 5 minut, protože stále může být agregace metrik.

Konfigurace upozornění na metriku na portálu:

1. Klikněte na **Všechny služby** > **Účty Batch** a potom klikněte na název vašeho účtu Batch.
2. V části **monitorování**klikněte na **pravidla výstrahy** > **Přidat výstrahu metriky**.
3. Vyberte metriku, výstražný stav (např. Pokud metrika překročí určitou hodnotu v období) a minimálně jedno oznámení.

Pomocí [REST API](https://docs.microsoft.com/rest/api/monitor/)můžete také nakonfigurovat upozornění téměř v reálném čase. Další informace najdete v tématu [Přehled výstrah](../azure-monitor/platform/alerts-overview.md). Pokud chcete do výstrah zahrnout informace o úlohách, úkolech nebo fondech, přečtěte si informace o vyhledávacích dotazech v tématu [reakce na události s výstrahami Azure monitor](../azure-monitor/learn/tutorial-response.md) .

## <a name="batch-diagnostics"></a>Diagnostika služby batch

Diagnostické protokoly obsahují informace, protože ho vygeneroval prostředky Azure, které popisují operace každého prostředku. Pro službu Batch můžete shromažďovat tyto protokoly:

* **Služba protokoluje** události generované službou Azure Batch během doby životnosti jednotlivých prostředků služby Batch, jako je fond nebo úloha. 

* **Metriky** se protokolují na úrovni účtu. 

Nastavení pro povolení shromažďování diagnostických protokolů nejsou povolené ve výchozím nastavení. Explicitně povolte nastavení diagnostiky pro každý účet Batch, které chcete monitorovat.

### <a name="log-destinations"></a>Log cílů

Běžný scénář, kdy je výběr účtu služby Azure Storage jako cíl protokolu. K ukládání protokolů ve službě Azure Storage, vytvořte účet před povolením shromažďování protokolů. Pokud jste účet úložiště spojené s vaším účtem Batch, můžete tento účet jako cíl protokolu. 

Ostatní volitelné cíle pro diagnostické protokoly:

* Umožňuje streamovat události protokolu diagnostiky služby Batch do [centra událostí Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Event Hubs můžete ingestovat miliony událostí za sekundu, které pak můžete transformovat a uložit pomocí libovolného zprostředkovatele datové analýzy v reálném čase. 

* Odešlete diagnostické protokoly do [protokolů Azure monitor](../log-analytics/log-analytics-overview.md), kde je můžete analyzovat nebo exportovat pro účely analýzy v Power BI nebo Excelu.

> [!NOTE]
> Vám může účtovat další poplatky za uložení nebo zpracování protokol diagnostiky dat pomocí služby Azure. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Povolit shromažďování diagnostické protokoly služby Batch

1. Na portálu klikněte na **všechny služby** > **účty Batch**a potom klikněte na název vašeho účtu Batch.
2. V části **monitorování**klikněte na **diagnostické protokoly** > **zapnout diagnostiku**.
3. V okně **nastavení diagnostiky**zadejte název nastavení a vyberte cíl protokolu (existující účet úložiště, centrum událostí nebo protokoly Azure monitor). Vyberte buď **ServiceLog** a **AllMetrics**.

    Když vyberete účet úložiště, volitelně nastavte zásady uchovávání informací. Pokud nechcete zadat počet dní pro uchovávání dat, data se uchovávají po dobu trvání účtu úložiště.

4. Klikněte na **Uložit**.

    ![Diagnostika služby batch](media/batch-diagnostics/diagnostics-portal.png)

Další možnosti povolení shromažďování protokolů: pomocí Azure Monitor na portálu můžete nakonfigurovat nastavení diagnostiky, použít [šablonu správce prostředků](../azure-monitor/platform/diagnostic-settings-template.md)nebo použít Azure PowerShell nebo rozhraní příkazového řádku Azure CLI. viz [shromažďování a zpracování dat protokolu z vašich prostředků Azure](../azure-monitor/platform/platform-logs-overview.md).


### <a name="access-diagnostics-logs-in-storage"></a>Protokoly diagnostiky přístup ve službě storage

Při archivaci diagnostické protokoly služby Batch v účtu úložiště, kontejner úložiště se vytvoří v účtu úložiště, jakmile dojde k související události. Objekty BLOB jsou vytvářeny podle následující vzor pro pojmenování:

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
Každý `PT1H.json` soubor BLOB obsahuje události ve formátu JSON, ke kterým došlo během hodiny zadané v adrese URL objektu BLOB (například `h=12`). Během této hodiny se události připojí k souboru `PT1H.json`, jak se vyskytují. Hodnota minute (`m=00`) je vždy `00`, protože události protokolu diagnostiky jsou v jednotlivých objektech blob za hodinu rozděleny. (Všechny časy jsou ve standardu UTC).

Níže je uveden příklad položky `PoolResizeCompleteEvent` v souboru protokolu `PT1H.json`. Obsahuje informace o aktuálním a cílovém počtu vyhrazených uzlů a uzlů s nízkou prioritou a také o počátečním a koncovém času operace:

```
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Další informace o schématu diagnostických protokolů v účtu úložiště najdete v tématu [archivace diagnostických protokolů Azure](../azure-monitor/platform/resource-logs-collect-storage.md#schema-of-platform-logs-in-storage-account). Pro přístup k protokolům ve vašem účtu úložiště prostřednictvím kódu programu, použijte rozhraní API služby Storage. 

### <a name="service-log-events"></a>Služba Protokol událostí
Azure protokoly služby Batch, je-li shromažďovat, obsahují události, protože ho pomocí služby Azure Batch po celou dobu životnosti jednotlivých prostředků Batch jako fondem nebo úlohou. Každou službou Batch byla vyvolána událost se protokoluje ve formátu JSON. Jedná se například o tělo **události vytvoření**ukázkového fondu:

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

Služba Batch aktuálně vysílá následující události protokol služby. Tento seznam nemusí být vyčerpávající, protože další události může byly přidány od poslední aktualizace v tomto článku.

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
