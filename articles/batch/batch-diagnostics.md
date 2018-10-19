---
title: Metriky, výstrahy a diagnostické protokoly pro službu Azure Batch | Dokumentace Microsoftu
description: Zaznamenávat a analyzovat události diagnostických protokolů pro účet prostředky služby Azure Batch, jako jsou fondy a úlohy.
services: batch
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: batch
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: big-compute
ms.date: 04/05/2018
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 0c045d82e47d2554c2767e6f52491c780618abc2
ms.sourcegitcommit: 07a09da0a6cda6bec823259561c601335041e2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2018
ms.locfileid: "49409522"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Batch metriky, výstrahy a protokoly pro diagnostické hodnocení a monitorování

 
Tento článek vysvětluje, jak monitorovat pomocí funkce účtu Batch [Azure Monitor](../azure-monitor/overview.md). Azure Monitor provádí sběr [metriky](../monitoring/monitoring-data-collection.md#metrics) a [diagnostické protokoly](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) pro prostředky v účtu Batch. Shromažďování a používání těchto dat různými způsoby, jak monitorovat účtu Batch a diagnostikovat problémy s. Můžete taky nakonfigurovat [upozornění na metriku](../monitoring-and-diagnostics/monitoring-overview-alerts.md) tak můžete dostávat upozornění na metriku dosáhne zadanou hodnotu. 

## <a name="batch-metrics"></a>Metriky služby batch

Metriky jsou Azure telemetrická data (také nazývané čítače výkonu), protože ho vygeneroval vašich prostředků Azure, které se spotřebovávají služby Azure Monitor. Zahrnout metriky příklad v účtu Batch: události vytvoření fondu, počet uzlů s nízkou prioritou a události dokončení úkolu. 

Zobrazit [seznam podporovaných metrik služby Batch](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftbatchbatchaccounts).

Metriky jsou:

* Povolené ve výchozím nastavení v každém účtu Batch bez další konfigurace
* Vygeneruje každou 1 minutu
* Není trvalý automaticky, ale historie se zajištěním provozu na 30 dnů. Je možné zachovat metriky aktivity v rámci [protokolování diagnostiky](#work-with-diagnostic-logs).

### <a name="view-metrics"></a>Zobrazit metriky

Zobrazit metriky pro účet Batch na webu Azure Portal. **Přehled** stránky pro účet ve výchozím nastavení zobrazuje uzel klíče, core a úlohy metriky. 

Chcete-li zobrazit všechny metriky pro účet Batch: 

1. Na portálu klikněte na tlačítko **všechny služby** > **účty Batch**a pak klikněte na název vašeho účtu Batch.
2. V části **monitorování**, klikněte na tlačítko **metriky**.
3. Vyberte jednu nebo několik metrik. Pokud chcete, vyberte metriky dalších prostředků s použitím **předplatná**, **skupiny prostředků**, **typ prostředku**, a **prostředků** rozevírací seznamy.

    ![Metriky služby batch](media/batch-diagnostics/metrics-portal.png)

Pokud chcete načíst metriky prostřednictvím kódu programu, pomocí rozhraní API služby Azure Monitor. Viz například [metriky načíst Azure Monitor s využitím .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-alerts"></a>Upozornění na metriky služby batch

Volitelně můžete nakonfigurovat téměř v reálném čase *upozornění na metriku* , který se aktivuje, když hodnota zadaného metrika překročí mezní hodnotu, která přiřadíte. Vygeneruje výstrahu [oznámení](../monitoring-and-diagnostics/insights-alerts-portal.md) zvolíte při "Aktivaci upozornění" (Pokud se překročí prahovou hodnotu a je splněná podmínka výstrahy) i když ho se "Vyřešeno" (když je znovu překročí prahovou hodnotu a je podmínka žádné splněna). 

Například můžete chtít konfigurovat upozornění na metriku při vaší počtu jader s nízkou prioritou spadá do určité úrovně, tak můžete upravit složení fondech.

Konfigurace upozornění na metriku na portálu:

1. Klikněte na **Všechny služby** > **Účty Batch** a potom klikněte na název vašeho účtu Batch.
2. V části **monitorování**, klikněte na tlačítko **pravidla upozornění** > **přidat upozornění metriky**.
3. Vyberte metriku, výstražný stav (např. Pokud metrika překročí určitou hodnotu v období) a minimálně jedno oznámení.

Můžete také nakonfigurovat, a téměř v reálném čase výstrahy s použitím [rozhraní REST API](https://docs.microsoft.com/rest/api/monitor/). Další informace najdete v tématu [přehled výstrah](../monitoring-and-diagnostics/monitoring-overview-alerts.md)

## <a name="batch-diagnostics"></a>Diagnostika služby batch

Diagnostické protokoly obsahují informace, protože ho vygeneroval prostředky Azure, které popisují operace každého prostředku. Pro službu Batch můžete shromažďovat tyto protokoly:

* **Protokoly služby** fondem nebo úlohou, jako jsou události, protože ho pomocí služby Azure Batch po celou dobu životnosti jednotlivých prostředků služby Batch. 

* **Metriky** protokoly na úrovni účtu. 

Nastavení pro povolení shromažďování diagnostických protokolů nejsou povolené ve výchozím nastavení. Explicitně povolte nastavení diagnostiky pro každý účet Batch, které chcete monitorovat.

### <a name="log-destinations"></a>Log cílů

Běžný scénář, kdy je výběr účtu služby Azure Storage jako cíl protokolu. K ukládání protokolů ve službě Azure Storage, vytvořte účet před povolením shromažďování protokolů. Pokud jste účet úložiště spojené s vaším účtem Batch, můžete tento účet jako cíl protokolu. 

Ostatní volitelné cíle pro diagnostické protokoly:

* Stream Batch diagnostický protokol událostí do [Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md). Event Hubs můžete ingestovat miliony událostí za sekundu, které pak můžete transformovat a uložit pomocí libovolného zprostředkovatele datové analýzy v reálném čase. 

* Poslat protokoly pro diagnostiku [Azure Log Analytics](../log-analytics/log-analytics-overview.md), kde můžete analyzovat nebo exportovat pro analýzy v Power BI nebo Excelu.

> [!NOTE]
> Vám může účtovat další poplatky za uložení nebo zpracování protokol diagnostiky dat pomocí služby Azure. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Povolit shromažďování diagnostické protokoly služby Batch

1. Na portálu klikněte na tlačítko **všechny služby** > **účty Batch**a pak klikněte na název vašeho účtu Batch.
2. V části **monitorování**, klikněte na tlačítko **diagnostické protokoly** > **zapnout diagnostiku**.
3. V **nastavení diagnostiky**, zadejte název pro nastavení a vyberte cíl protokolu (existující úložiště účtu, Centrum událostí nebo Log Analytics). Vyberte jednu nebo obě **ServiceLog** a **AllMetrics**.

    Když vyberete účet úložiště, volitelně nastavte zásady uchovávání informací. Pokud nechcete zadat počet dní pro uchovávání dat, data se uchovávají po dobu trvání účtu úložiště.

4. Klikněte na **Uložit**.

    ![Diagnostika služby batch](media/batch-diagnostics/diagnostics-portal.png)

Další možnosti Povolit shromažďování protokolů zahrnují: pomocí Azure monitoru na portálu můžete nakonfigurovat nastavení diagnostiky, použijte [šablony Resource Manageru](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md), nebo pomocí Azure Powershellu nebo rozhraní příkazového řádku Azure. Zobrazit [shromažďovat a zpracovávat data protokolu z vašich prostředků Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs).


### <a name="access-diagnostics-logs-in-storage"></a>Protokoly diagnostiky přístup ve službě storage

Při archivaci diagnostické protokoly služby Batch v účtu úložiště, kontejner úložiště se vytvoří v účtu úložiště, jakmile dojde k související události. Objekty BLOB jsou vytvářeny podle následující vzor pro pojmenování:

```
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
Příklad:

```
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```
Každý soubor blob PT1H.json obsahuje ve formátu JSON událostí, ke kterým došlo během hodiny zadané v adrese URL objektu blob (například h = 12). Během aktuální hodiny se události připojují do souboru PT1H.json, když k nim dojde. Minut (m = 00) je vždy 00, protože události protokolu diagnostiky se rozdělují do jednotlivých objektů BLOB po hodinách. (Všechny časy jsou ve standardu UTC).


Další informace o schématu diagnostické protokoly v aktuálním účtu úložiště najdete v tématu [archivace diagnostických protokolů Azure](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

Pro přístup k protokolům ve vašem účtu úložiště prostřednictvím kódu programu, použijte rozhraní API služby Storage. 

### <a name="service-log-events"></a>Služba Protokol událostí
Azure protokoly služby Batch, je-li shromažďovat, obsahují události, protože ho pomocí služby Azure Batch po celou dobu životnosti jednotlivých prostředků Batch jako fondem nebo úlohou. Každou službou Batch byla vyvolána událost se protokoluje ve formátu JSON. To je například textu ukázku **událost vytvoření fondu**:

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

| **Služba Protokol událostí** |
| --- |
| [Vytvoření fondu](batch-pool-create-event.md) |
| [Zahájení odstranění fondu](batch-pool-delete-start-event.md) |
| [Dokončení odstranění fondu](batch-pool-delete-complete-event.md) |
| [Zahájení změny velikosti fondu](batch-pool-resize-start-event.md) |
| [Dokončení změny velikosti fondu](batch-pool-resize-complete-event.md) |
| [Zahájení úlohy](batch-task-start-event.md) |
| [Úloha je dokončená](batch-task-complete-event.md) |
| [Selhání úlohy](batch-task-fail-event.md) |



## <a name="next-steps"></a>Další postup

* Další informace o dostupných [rozhraních API a nástrojích služby Batch](batch-apis-tools.md) pro sestavování řešení Batch.
* Další informace o [monitorování řešení Batch](monitoring-overview.md).
