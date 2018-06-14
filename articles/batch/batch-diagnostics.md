---
title: Metriky, výstrahy a diagnostické protokoly pro Azure Batch | Microsoft Docs
description: Zaznamenávat a analyzovat události protokolů diagnostiky pro prostředkům účet Azure Batch, jako jsou fondy a úkoly.
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
ms.openlocfilehash: e64d272695c4e47c972df040d1c1c2a63bf3dddd
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2018
ms.locfileid: "31788190"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Metriky batch, výstrahy a protokoly pro monitorování a diagnostiky vyhodnocení

Tento článek vysvětluje, jak monitorovat účtu Batch pomocí funkce [Azure monitorování](../monitoring-and-diagnostics/monitoring-overview-azure-monitor.md). Shromažďuje Azure monitorování [metriky](../monitoring-and-diagnostics/monitoring-overview-metrics.md) a [diagnostické protokoly](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) pro prostředky v účtu Batch. Umožňuje shromáždit a využívat tato data v různých způsobů, jak sledovat vašeho účtu Batch a diagnostikovat problémy. Můžete také nakonfigurovat [metriky výstrahy](../monitoring-and-diagnostics/monitoring-overview-alerts.md#alerts-on-azure-monitor-data) tak při metriky nedosáhne zadané hodnoty se zobrazí oznámení. 

## <a name="batch-metrics"></a>Metriky batch

Metriky jsou Azure telemetrická data (také nazývané čítače výkonu) vysílaných vašich prostředků Azure, které se spotřebovávají službu sledování Azure. Příklad metrikám v účtu Batch patří: události vytvoření fondu, počet uzlů nízkou prioritu a událostí dokončení úlohy. 

Najdete v článku [seznam podporovaných metriky Batch](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftbatchbatchaccounts).

Metriky jsou:

* Ve výchozím nastavení je u každého účtu Batch bez další konfigurace povoleno
* Generuje každých 1 minuta
* Není trvalý automaticky, ale mají postupnou historii 30 dnů. Můžete zachovat metriky aktivity v rámci [protokolování diagnostiky](#work-with-diagnostic-logs).

### <a name="view-metrics"></a>Zobrazit metriky

Zobrazit metriky pro svůj účet Batch na portálu Azure. **Přehled** stránky pro účet ve výchozím nastavení zobrazuje uzel klíče, základní a metriky úloh. 

Chcete-li zobrazit všechny metriky účtu Batch: 

1. Na portálu, klikněte na tlačítko **všechny služby** > **účty Batch**a pak klikněte na název vašeho účtu Batch.
2. V části **monitorování**, klikněte na tlačítko **metriky**.
3. Vyberte jednu nebo více metrik. Pokud chcete, vyberte prostředek další metriky pomocí **odběry**, **skupiny prostředků**, **typ prostředku**, a **prostředků** rozevírací seznamy.

    ![Metriky batch](media/batch-diagnostics/metrics-portal.png)

Pro načtení metriky prostřednictvím kódu programu, použijte rozhraní API Azure monitorování. Například v tématu [metriky načíst monitorování Azure s .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

## <a name="batch-metric-alerts"></a>Batch metriky výstrahy

Volitelně můžete nakonfigurovat téměř v reálném čase *metriky výstrahy* , aktivuje, když hodnota zadané metriky překračuje prahovou hodnotu, která přiřadíte. Generuje výstrahu [oznámení](../monitoring-and-diagnostics/insights-alerts-portal.md) zvolíte, pokud je výstraha "aktivován" (Pokud se překročí prahovou hodnotu a je splněna podmínka výstrahy) a také při se "nevyřeší" (Pokud je znovu překročí prahovou hodnotu a je podmínka vyhodnocena jako žádné nebude splněna). 

Například můžete chtít konfigurace metriky výstrahy, když vaše počet jader s nízkou prioritou klesne na určité úrovni, abyste mohli upravit složení fondech.

Konfigurace upozornění na metriky na portálu:

1. Klikněte na **Všechny služby** > **Účty Batch** a potom klikněte na název vašeho účtu Batch.
2. V části **monitorování**, klikněte na tlačítko **výstrah pravidla** > **přidat metriky upozornění**.
3. Vyberte metriku, výstrahy podmínku (například pokud metriky překročí určitou hodnotu během období) a minimálně jedno oznámení.

Můžete také nakonfigurovat near v reálném čase výstrah [REST API](). Další informace najdete v tématu [novější metriky výstrahy můžete použít pro služby Azure na portálu Azure](../monitoring-and-diagnostics/monitoring-near-real-time-metric-alerts.md)
## <a name="batch-diagnostics"></a>Batch diagnostiky

Diagnostické protokoly obsahují informace, které prostředky Azure, které popisují operaci každého prostředku. Pro dávku můžete shromáždit tyto protokoly:

* **Protokoly služby** události vygenerované službou Azure Batch po dobu životnosti prostředek jednotlivé dávky jako fond nebo úloh. 

* **Metriky** protokoly na úrovni účtu. 

Nastavení pro povolení shromažďování diagnostických protokolů nejsou povolené ve výchozím nastavení. Explicitně povolte nastavení diagnostiky pro každého účtu Batch, který chcete monitorovat.

### <a name="log-destinations"></a>Cíle protokolu

Obvyklým scénářem je vybrat jako cíl protokolu účtu Azure Storage. K ukládání protokolů ve službě Azure Storage, vytvořte účet před povolením shromažďování protokolů. Pokud jste účet úložiště spojené s vaším účtem Batch, můžete tento účet jako cíl protokolu. 

Další volitelné cíle pro diagnostické protokoly:

* Stream události diagnostických protokolu dávky [centra událostí Azure](../event-hubs/event-hubs-what-is-event-hubs.md). Centra událostí můžete přijímat miliony událostí za sekundu, které můžete transformovat a ukládat pomocí libovolného zprostředkovatele analýzu v reálném čase. 

* Odeslání protokolů s diagnostickými [Azure Log Analytics](../log-analytics/log-analytics-overview.md), kde můžete analyzovat na portálu Operations Management Suite (OMS), nebo je exportovat pro analýzu v Power BI nebo Excelu.

> [!NOTE]
> Může způsobit dodatečné náklady na ukládání nebo zpracování dat protokolů diagnostiky se službami Azure. 
>

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Povolit shromažďování diagnostických protokolů Batch

1. Na portálu, klikněte na tlačítko **všechny služby** > **účty Batch**a pak klikněte na název vašeho účtu Batch.
2. V části **monitorování**, klikněte na tlačítko **diagnostické protokoly** > **zapněte diagnostiku**.
3. V **nastavení pro diagnostiku**, zadejte název pro nastavení a vyberte cíl protokolu (stávající úložiště účet, centra událostí nebo analýzy protokolů). Vyberte jednu nebo obě **ServiceLog** a **AllMetrics**.

    Když vyberete účet úložiště, můžete také nastavte zásady uchovávání informací. Pokud nezadáte počet dní pro uchovávání dat, mají být uchována data během doby platnosti účtu úložiště.

4. Klikněte na **Uložit**.

    ![Batch diagnostiky](media/batch-diagnostics/diagnostics-portal.png)

Další možnosti, jak povolit kolekce protokolu zahrnují: pomocí funkce sledování Azure na portálu pro konfiguraci nastavení pro diagnostiku, použijte [šablony Resource Manageru](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md), nebo použijte prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure. v tématu [shromažďovat a využívat data protokolu z vašich prostředků Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs).


### <a name="access-diagnostics-logs-in-storage"></a>Diagnostika přístup přihlásí úložiště

Pokud archivujete Batch diagnostických protokolů v účtu úložiště, kontejner úložiště se vytvoří v účtu úložiště, jakmile dojde k související události. Objekty BLOB jsou vytvářeny podle následující vzoru pro pojmenovávání:

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
Každý soubor blob PT1H.json obsahuje formátu JSON události, k nimž došlo v rámci hodiny zadaného v adrese URL objektu blob (například h = 12). Během aktuální hodiny se události připojují do souboru PT1H.json, když k nim dojde. Hodnota minutu (m = 00) je vždy 00, protože protokolů diagnostiky události jsou rozdělená do jednotlivých objektů blob za hodinu. (Všechny časy jsou ve formátu UTC).


Další informace o schématu diagnostických protokolů v účtu úložiště najdete v tématu [diagnostických protokolů Azure archivu](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

Chcete-li protokoly ve vašem účtu úložiště přístup prostřednictvím kódu programu, použijte rozhraní API úložiště. 

### <a name="service-log-events"></a>Služba Protokol událostí
Azure protokoly služby Batch, pokud shromážděné, obsahují události vygenerované službou Azure Batch po dobu životnosti prostředek jednotlivé dávky jako fondu nebo úloh. Všechny události vygenerované dávkou přihlášen ve formátu JSON. To je třeba do těla ukázku **fondu vytvoření události**:

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

Služba Batch aktuálně vysílá následující události služby protokolu. Tento seznam nemusí být kompletní, protože další události mohly být přidány od poslední aktualizace v tomto článku.

| **Služba Protokol událostí** |
| --- |
| [Vytvoření fondu](batch-pool-create-event.md) |
| [Spuštění odstranění fondu](batch-pool-delete-start-event.md) |
| [Odstranění fondu dokončení](batch-pool-delete-complete-event.md) |
| [Počáteční velikosti fondu](batch-pool-resize-start-event.md) |
| [Dokončení změny velikosti fondu](batch-pool-resize-complete-event.md) |
| [Spuštění úlohy](batch-task-start-event.md) |
| [Dokončení úkolů](batch-task-complete-event.md) |
| [Selhání úlohy](batch-task-fail-event.md) |



## <a name="next-steps"></a>Další postup

* Další informace o dostupných [rozhraních API a nástrojích služby Batch](batch-apis-tools.md) pro sestavování řešení Batch.
* Další informace o [monitorování řešení Batch](monitoring-overview.md).
