---
title: Metriky, výstrahy a diagnostické protokoly – Azure Batch | Dokumenty společnosti Microsoft
description: Zaznamenejte a analyzujte události diagnostického protokolu pro prostředky účtu Azure Batch, jako jsou fondy a úkoly.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254860"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Metriky dávek, výstrahy a protokoly pro diagnostické vyhodnocení a monitorování

 
Tento článek vysvětluje, jak sledovat účet Batch pomocí funkcí [Azure Monitor](../azure-monitor/overview.md). Azure Monitor shromažďuje [metriky](../azure-monitor/platform/data-platform-metrics.md) a [diagnostické protokoly](../azure-monitor/platform/platform-logs-overview.md) pro prostředky ve vašem účtu Batch. Shromažďujte a konzumujte tato data různými způsoby, abyste monitorovali svůj účet Batch a diagnostikovali problémy. Můžete také nakonfigurovat [upozornění na metriky,](../azure-monitor/platform/alerts-overview.md) abyste obdrželi oznámení, když metrika dosáhne zadané hodnoty. 

## <a name="batch-metrics"></a>Metriky dávky

Metriky jsou telemetrická data Azure (označovaná také jako čítače výkonu) vyzařovaná prostředky Azure, které spotřebovává služba Azure Monitor. Příklady metrik v dávkovém účtu zahrnují: Události vytvoření fondu, Počet uzlů s nízkou prioritou a Události dokončení úkolu. 

Podívejte se na [seznam podporovaných metrik Batch](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Metriky jsou:

* Ve výchozím nastavení je povolena v každém dávkovém účtu bez další konfigurace
* Generováno každých 1 minuta
* Není automaticky zachováno, ale má 30denní historii. Metriky aktivity můžete zachovat jako součást protokolování diagnostiky.

### <a name="view-metrics"></a>Zobrazit metriky

Zobrazte metriky pro svůj dávkový účet na webu Azure Portal. Stránka **Přehled** účtu ve výchozím nastavení zobrazuje klíčové metriky uzlů, jádra a úkolů. 

Zobrazení všech metrik účtu batch: 

1. Na portálu klikněte na **Všechny dávkové** > **účty**služeb a potom klikněte na název svého účtu Batch.
2. V části **Sledování**klikněte na **Metriky**.
3. Vyberte jednu nebo více metrik. Pokud chcete, vyberte další metriky prostředků pomocí nabídky **Odběry**, **Skupina prostředků**, **Typ prostředku**a **Zdroj.**
    * Pro metriky založené na počítání (například "Vyhrazený počet jádra" nebo "Počet uzlů s nízkou prioritou" použijte agregaci Průměr. Pro metriky založené na událostech (například "Události dokončení velikosti fondu) použijte agregaci Počet.

> [!WARNING]
> Nepoužívejte agregaci "Sum", která sečte hodnoty všech datových bodů přijatých za období grafu.
> 
> 

    ![Batch metrics](media/batch-diagnostics/metrics-portal.png)

K programovému načtení metrik použijte api azure monitoru. Například najdete [v tématu Načtení metriky Azure Monitor s rozhraním .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-reliability"></a>Spolehlivost dávkové metriky

Metriky jsou určeny pro trendy a analýzu dat. Doručení metriky není zaručeno a podléhá mimoobobní doručení, ztrátě dat a/nebo duplikaci. Použití jednotlivých událostí k upozornění nebo aktivaci funkcí se nedoporučuje. Další informace o tom, jak nastavit prahové hodnoty pro upozorňování, najdete v části [Upozornění na metriky dávky.](#batch-metric-alerts)

Metriky vyzařované v posledních 3 minutách může být stále agregovat. Během tohoto časového rámce mohou být hodnoty metriky podhodnoceny.

## <a name="batch-metric-alerts"></a>Upozornění na dávkové metriky

Volitelně můžete nakonfigurovat *téměř výstrahy metrik* v reálném čase, které se aktivují, když hodnota zadané metriky překročí prahovou hodnotu, kterou přiřadíte. Výstraha vygeneruje [oznámení,](../monitoring-and-diagnostics/insights-alerts-portal.md) které zvolíte, když je výstraha "Aktivována" (při překročení prahové hodnoty a splnění podmínky výstrahy) a také když je "Vyřešeno" (když je prahová hodnota znovu překročena a podmínka již není splněna). Upozorňování na základě jednotlivých datových bodů se nedoporučuje, protože metriky podléhají doručování mimo objednávku, ztrátě dat a/nebo duplikaci. Upozorňování by mělo využívat prahové hodnoty k zohlednění těchto nesrovnalostí.

Můžete například nakonfigurovat upozornění na metriku, když počet jádra s nízkou prioritou klesne na určitou úroveň, abyste mohli upravit složení fondů. Doporučuje se nastavit období 10 nebo více minut, kdy se výstrahy aktivují, pokud průměrný počet jádra s nízkou prioritou klesne pod prahovou hodnotu za celé období. Nedoporučuje se upozornit na období 1–5 minut, protože metriky mohou být stále agregovány.

Konfigurace upozornění metriky na portálu:

1. Klikněte na **Všechny dávkové** > **účty**služeb a potom klikněte na název účtu Batch.
2. V části **Sledování**klikněte na **Pravidla** > **výstrahPřidat upozornění na metriku**.
3. Vyberte metriku, výstražnou podmínku (například když metrika překročí určitou hodnotu během období) a jedno nebo více oznámení.

Můžete také nakonfigurovat výstrahu téměř v reálném čase pomocí [rozhraní REST API](https://docs.microsoft.com/rest/api/monitor/). Další informace naleznete v [tématu Přehled výstrah](../azure-monitor/platform/alerts-overview.md). Pokud chcete do výstrah zahrnout informace o úloze, úloze nebo fondu, přečtěte si informace o vyhledávacích dotazech v [části Reagovat na události pomocí výstrah Monitoru Azure.](../azure-monitor/learn/tutorial-response.md)

## <a name="batch-diagnostics"></a>Diagnostika služby Batch

Diagnostické protokoly obsahují informace vyzařované prostředky Azure, které popisují provoz každého prostředku. Pro Batch, můžete shromažďovat následující protokoly:

* **Protokoly služeb** události vyzařované službou Azure Batch během životnosti jednotlivého dávkového prostředku, jako je fond nebo úkol. 

* **Metriky protokoly** na úrovni účtu. 

Nastavení umožňující shromažďování diagnostických protokolů nejsou ve výchozím nastavení povolena. Explicitně povolte diagnostická nastavení pro každý dávkový účet, který chcete sledovat.

### <a name="log-destinations"></a>Protokolovat cíle

Běžným scénářem je výběr účtu úložiště Azure jako cíl protokolu. Chcete-li ukládat protokoly ve službě Azure Storage, vytvořte účet před povolením shromažďování protokolů. Pokud jste přidružili účet úložiště ke svému účtu Batch, můžete si tento účet vybrat jako cíl protokolu. 

Další volitelné cíle pro diagnostické protokoly:

* Streamujte události diagnostického protokolu služby Batch do [centra událostí Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Centra událostí mohou ingestovat miliony událostí za sekundu, které pak můžete transformovat a ukládat pomocí libovolného poskytovatele analýzy v reálném čase. 

* Odešlete diagnostické protokoly do [protokolů Azure Monitoru](../log-analytics/log-analytics-overview.md), kde je můžete analyzovat nebo exportovat pro analýzu v Power BI nebo Excelu.

> [!NOTE]
> Mohou vám vzniknout další náklady na ukládání nebo zpracování diagnostických dat protokolu se službami Azure. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Povolit shromažďování protokolů diagnostiky dávek

1. Na portálu klikněte na **Všechny dávkové** > **účty**služeb a potom klikněte na název svého účtu Batch.
2. V části **Sledování**klepněte na **položku Diagnostické protokoly** > **Zapnout diagnostiku**.
3. V **nastavení diagnostiky**zadejte název nastavení a zvolte cíl protokolu (existující účet úložiště, centrum událostí nebo protokoly Azure Monitoru). Vyberte buď nebo oba **ServiceLog** a **AllMetrics**.

    Když vyberete účet úložiště, volitelně nastavte zásady uchovávání informací. Pokud nezadáte počet dní pro uchovávání, data se uchovají po dobu životnosti účtu úložiště.

4. Klikněte na **Uložit**.

    ![Diagnostika služby Batch](media/batch-diagnostics/diagnostics-portal.png)

Mezi další možnosti, jak povolit shromažďování protokolů, patří: pomocí Nástroje pro sledování Azure na portálu nakonfigurujte nastavení diagnostiky, použijte [šablonu Správce prostředků](../azure-monitor/platform/diagnostic-settings-template.md)nebo použijte Azure PowerShell nebo Azure CLI. viz [Shromažďování a využívání dat protokolu z prostředků Azure](../azure-monitor/platform/platform-logs-overview.md).


### <a name="access-diagnostics-logs-in-storage"></a>Přístup k protokolům diagnostiky v úložišti

Pokud archivujete protokoly diagnostiky dávky v účtu úložiště, kontejner úložiště se vytvoří v účtu úložiště, jakmile dojde k související události. Objekty BLOB jsou vytvořeny podle následujícího vzoru pojmenování:

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
Každý `PT1H.json` soubor objektu blob obsahuje události ve formátu JSON, ke kterým `h=12`došlo během hodiny zadané v adrese URL objektu blob (například). Během této hodiny události jsou připojeny k souboru, `PT1H.json` jakmile k nim dojde. Hodnota minuty`m=00`( ) `00`je vždy , protože události diagnostického protokolu jsou rozděleny do jednotlivých objektů BLOB za hodinu. (Všechny časy jsou v UTC.)

Níže je uveden `PoolResizeCompleteEvent` příklad položky v souboru `PT1H.json` protokolu. Obsahuje informace o aktuálním a cílovém počtu vyhrazených uzlů a uzlů s nízkou prioritou, jakož i počáteční a koncový čas operace:

```
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Další informace o schématu diagnostických protokolů v účtu úložiště naleznete v [tématu Archivní protokoly diagnostiky Azure](../azure-monitor/platform/resource-logs-collect-storage.md#schema-of-platform-logs-in-storage-account). Chcete-li získat přístup k protokolům v účtu úložiště programově, použijte úložiště API. 

### <a name="service-log-events"></a>Události protokolu služeb
Azure Batch Service Protokoly, pokud jsou shromažďovány, obsahují události vyzařované službou Azure Batch během životnosti jednotlivých dávkových prostředků, jako je fond nebo úkol. Každá událost vyzařovaná batch je zaznamenána ve formátu JSON. Například toto je tělo ukázkového **fondu vytvořit událost**:

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

Služba Batch aktuálně vydává následující události protokolu služeb. Tento seznam nemusí být vyčerpávající, protože od poslední aktualizace tohoto článku mohly být přidány další události.

| **Události protokolu služeb** |
| --- |
| [Vytvoření fondu](batch-pool-create-event.md) |
| [Začátek odstranění fondu](batch-pool-delete-start-event.md) |
| [Odstranění fondu dokončeno](batch-pool-delete-complete-event.md) |
| [Začátek změny velikosti fondu](batch-pool-resize-start-event.md) |
| [Změna velikosti fondu dokončena](batch-pool-resize-complete-event.md) |
| [Zahájení úkolu](batch-task-start-event.md) |
| [Úkol byl dokončen.](batch-task-complete-event.md) |
| [Úloha se nezdaří](batch-task-fail-event.md) |



## <a name="next-steps"></a>Další kroky

* Další informace o dostupných [rozhraních API a nástrojích služby Batch](batch-apis-tools.md) pro sestavování řešení Batch.
* Další informace o [monitorování dávkových řešení](monitoring-overview.md).
