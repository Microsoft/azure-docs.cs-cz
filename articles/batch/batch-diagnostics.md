---
title: Metriky, výstrahy a diagnostické protokoly
description: Zaznamenává a analyzuje události diagnostického protokolu pro prostředky Azure Batch účtů, jako jsou fondy a úkoly.
ms.topic: how-to
ms.date: 10/08/2020
ms.custom: seodec18
ms.openlocfilehash: 265149e8d3cd775974ec690ebffbce92a1b82b2e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91848683"
---
# <a name="batch-metrics-alerts-and-logs-for-diagnostic-evaluation-and-monitoring"></a>Metriky, výstrahy a protokoly služby Batch pro vyhodnocení a monitorování diagnostiky

Tento článek vysvětluje, jak monitorovat účet Batch pomocí funkcí [Azure monitor](../azure-monitor/overview.md). Azure Monitor shromažďuje [metriky](../azure-monitor/platform/data-platform-metrics.md) a [diagnostické protokoly](../azure-monitor/platform/platform-logs-overview.md) pro prostředky v účtu Batch. Shromažďovat a spotřebovávat tato data různými způsoby, jak monitorovat účet Batch a diagnostikovat problémy. Můžete také nakonfigurovat [Upozornění na metriky](../azure-monitor/platform/alerts-overview.md) , abyste obdrželi oznámení, když metrika dosáhne zadané hodnoty.

## <a name="batch-metrics"></a>Metriky dávky

Metriky jsou data telemetrie Azure (označují se taky jako čítače výkonu), která vycházejí z vašich prostředků Azure a jsou využívaná službou Azure Monitor. Příklady metrik v účtu Batch jsou události vytvoření fondu, Low-Priority počet uzlů a události dokončení úlohy.

Podívejte se na [seznam podporovaných metrik dávky](../azure-monitor/platform/metrics-supported.md#microsoftbatchbatchaccounts).

Metriky jsou:

- Ve výchozím nastavení povolených v každém účtu Batch bez další konfigurace
- Vygenerováno každou 1 minutu
- Netrvalá automaticky, ale má 30denní historii průběžných změn. Metriky aktivity můžete zachovat jako součást protokolování diagnostiky.

## <a name="view-batch-metrics"></a>Zobrazit metriky dávky

V Azure Portal zobrazí stránka **Přehled** pro účet ve výchozím nastavení metriky klíčového uzlu, jádra a úlohy.

Zobrazení všech metrik účtu Batch v Azure Portal:

1. V Azure Portal vyberte **všechny služby**  >  **účty Batch**a potom vyberte název účtu Batch.
2. V části **Sledování** klikněte na **Metriky**.
3. Vyberte **Přidat metriku** a potom vyberte metriku v rozevíracím seznamu.
4. Vyberte možnost **agregace** pro metriku. Pro metriky založené na počtu (například "vyhrazený počet jader" nebo "počet uzlů s nízkou prioritou") použijte **průměrnou** agregaci. Pro metriky založené na událostech (jako je "Změna velikosti fondu na události") použijte agregaci **Count (počet**dokončených událostí).

   > [!WARNING]
   > Nepoužívejte agregaci Sum, která přidá hodnoty všech datových bodů přijatých za období grafu.

5. Pokud chcete přidat další metriky, opakujte kroky 3 a 4.

Metriky můžete také programově načíst pomocí rozhraní API Azure Monitor. Příklad najdete v tématu [načtení metrik Azure monitor pomocí .NET](https://azure.microsoft.com/resources/samples/monitor-dotnet-metrics-api/).

### <a name="batch-metric-reliability"></a>Spolehlivost metriky dávky

Metriky můžou identifikovat trendy a můžou se použít k analýze dat. Je důležité si uvědomit, že doručení metriky není zaručené a může být vystavené doručování, ztrátám dat a/nebo duplicitám. Z tohoto důvodu se nedoporučuje použití jednoduchých událostí na výstrahy nebo aktivační funkce. Další informace o tom, jak nastavit prahové hodnoty pro upozorňování, najdete v další části.

Metriky vygenerované za poslední 3 minuty mohou být stále agregovány, takže hodnoty metriky mohou být během tohoto časového období v sestavách.

## <a name="batch-metric-alerts"></a>Výstrahy metriky dávky

Můžete nakonfigurovat *výstrahy metriky* téměř v reálném čase, které se aktivují, když hodnota zadané metriky překračuje prahovou hodnotu, kterou přiřadíte. Výstraha vygeneruje oznámení v případě, že je výstraha "aktivována" (při překročení prahové hodnoty a při splnění podmínky upozornění) a také v případě, že se jedná o "Vyřešený" (Pokud se prahová hodnota opakuje a podmínka již není splněna).

Výstrahy, které se aktivují v jednom datovém bodě, se nedoporučují, protože metriky podléhají doručování, ztrátě dat a duplicitám při neurčitém pořadí. Při vytváření výstrah můžete pro tyto nekonzistence použít prahové hodnoty.

Můžete například chtít nakonfigurovat výstrahu metriky, když počet jader s nízkou prioritou spadá na určitou úroveň, abyste mohli upravit složení fondů. Pro dosažení nejlepších výsledků nastavte období 10 nebo více minut, kde se aktivují výstrahy, pokud průměrný počet jader s nízkou prioritou klesne pod prahovou hodnotu pro celé období. Díky tomu je možné metrikám agregovat více času, abyste získali přesnější výsledky.

Postup konfigurace výstrahy metriky v Azure Portal:

1. Vyberte **všechny služby**  >  **Batch účty**a potom vyberte název účtu Batch.
2. V části **monitorování**vyberte **výstrahy**a pak vyberte **nové pravidlo výstrahy**.
3. Klikněte na **vybrat podmínku**a pak vyberte metriku. Potvrďte hodnoty pro **periodu grafu**, **Typ prahové**hodnoty, **operátora**a **typ agregace**a zadejte **prahovou hodnotu**. Potom vyberte **Done** (Hotovo).
4. Přidejte skupinu akcí k výstraze buď výběrem existující skupiny akcí, nebo vytvořením nové skupiny akcí.
5. V části **Podrobnosti pravidla výstrahy** zadejte název a popis **pravidla upozornění** a vyberte **Description** **závažnost** .
6. Vyberte **Vytvořit pravidlo upozornění**.

Další informace o vytváření výstrah metrik najdete v tématu [vysvětlení, jak výstrahy metrik fungují v Azure monitor](../azure-monitor/platform/alerts-metric-overview.md) a [vytváření, zobrazování a správa výstrah metrik pomocí Azure monitor](../azure-monitor/platform/alerts-metric.md).

Můžete také nakonfigurovat upozornění téměř v reálném čase pomocí [REST API](/rest/api/monitor/)Azure monitor. Další informace najdete v tématu [Přehled výstrah v Microsoft Azure](../azure-monitor/platform/alerts-overview.md). Pokud chcete do upozornění zahrnout informace o úlohách, úkolech nebo fondech, přečtěte si informace o vyhledávacích dotazech v tématu [reakce na události s výstrahami Azure monitor](../azure-monitor/learn/tutorial-response.md).

## <a name="batch-diagnostics"></a>Diagnostika služby Batch

Diagnostické protokoly obsahují informace vydávané prostředky Azure, které popisují operace každého prostředku. V případě služby Batch můžete shromažďovat následující protokoly:

- **Služba protokoluje** události generované službou Azure Batch během doby životnosti jednotlivých prostředků služby Batch, jako je fond nebo úloha.
- **Metriky** se protokolují na úrovni účtu.

Nastavení pro povolení shromažďování diagnostických protokolů není ve výchozím nastavení povolené. Explicitně povolte nastavení diagnostiky pro každý účet Batch, který chcete monitorovat.

### <a name="log-destinations"></a>Cíle protokolu

Běžným scénářem je výběr účtu Azure Storage jako cíle protokolu. Chcete-li ukládat protokoly v Azure Storage, vytvořte účet před povolením shromažďování protokolů. Pokud jste k účtu Batch přihlásili účet úložiště, můžete tento účet zvolit jako cíl protokolu.

Alternativně můžete:

- Umožňuje streamovat události protokolu diagnostiky služby Batch do [centra událostí Azure](../event-hubs/event-hubs-about.md). Event Hubs může ingestovat miliony událostí za sekundu, které pak můžete transformovat a ukládat pomocí libovolného zprostředkovatele analýz v reálném čase.
- Odešlete diagnostické protokoly do [protokolů Azure monitor](../azure-monitor/log-query/log-query-overview.md), kde je můžete analyzovat nebo exportovat pro účely analýzy v Power BI nebo Excelu.

> [!NOTE]
> Pro ukládání nebo zpracování dat protokolů diagnostiky se službami Azure můžete účtovat další náklady.

### <a name="enable-collection-of-batch-diagnostic-logs"></a>Povolit shromažďování diagnostických protokolů Batch

Chcete-li vytvořit nové nastavení diagnostiky v Azure Portal, postupujte podle následujících kroků.

1. V Azure Portal vyberte **všechny služby**  >  **účty Batch**a potom vyberte název účtu Batch.
2. V části **Monitorování** vyberte **Nastavení diagnostiky**.
3. V **nastavení diagnostiky**vyberte **Přidat nastavení diagnostiky**.
4. Zadejte název nastavení.
5. Vyberte cíl: **odeslat Log Analytics**, **archivovat do účtu úložiště**nebo **Stream do centra událostí**. Pokud vyberete účet úložiště, můžete volitelně nastavit zásady uchovávání informací. Pokud nezadáte počet dní pro uchování, data se zachovají během životnosti účtu úložiště.
6. Vyberte **ServiceLog**, **AllMetrics**nebo obojí.
7. Vyberte **Save (Uložit** ) a vytvořte nastavení diagnostiky.

Můžete také [Povolit shromažďování prostřednictvím Azure monitor v Azure Portal](../azure-monitor/platform/diagnostic-settings.md) ke konfiguraci nastavení diagnostiky, pomocí [šablony Správce prostředků](../azure-monitor/platform/diagnostic-settings-template.md)nebo pomocí Azure PowerShell nebo rozhraní příkazového řádku Azure CLI. Další informace najdete v tématu [Přehled protokolů platformy Azure](../azure-monitor/platform/platform-logs-overview.md).

### <a name="access-diagnostics-logs-in-storage"></a>Přístup k diagnostickým protokolům v úložišti

Pokud archivujete diagnostické protokoly služby Batch v účtu úložiště, vytvoří se v účtu úložiště kontejner úložiště hned po výskytu související události. Objekty BLOB se vytvářejí podle následujícího vzoru názvů:

```json
insights-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/
RESOURCEGROUPS/{resource group name}/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/{Batch account name}/y={four-digit numeric year}/
m={two-digit numeric month}/d={two-digit numeric day}/
h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Například:

```json
insights-metrics-pt1m/resourceId=/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/
RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/
BATCHACCOUNTS/MYBATCHACCOUNT/y=2018/m=03/d=05/h=22/m=00/PT1H.json
```

Každý `PT1H.json` soubor objektu BLOB obsahuje události ve formátu JSON, ke kterým došlo během hodiny zadané v adrese URL objektu BLOB (například `h=12` ). Během této hodiny se události připojí k `PT1H.json` souboru, když k nim dojde. Hodnota minute ( `m=00` ) je vždy `00` , protože události diagnostického protokolu jsou v jednotlivých objektech blob za hodinu rozdělené. (Všechny časy jsou v UTC.)

Níže je uveden příklad `PoolResizeCompleteEvent` položky v `PT1H.json` souboru protokolu. Obsahuje informace o aktuálním a cílovém počtu vyhrazených uzlů a uzlů s nízkou prioritou a také o počátečním a koncovém času operace:

```json
{ "Tenant": "65298bc2729a4c93b11c00ad7e660501", "time": "2019-08-22T20:59:13.5698778Z", "resourceId": "/SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.BATCH/BATCHACCOUNTS/MYBATCHACCOUNT/", "category": "ServiceLog", "operationName": "PoolResizeCompleteEvent", "operationVersion": "2017-06-01", "properties": {"id":"MYPOOLID","nodeDeallocationOption":"Requeue","currentDedicatedNodes":10,"targetDedicatedNodes":100,"currentLowPriorityNodes":0,"targetLowPriorityNodes":0,"enableAutoScale":false,"isAutoPool":false,"startTime":"2019-08-22 20:50:59.522","endTime":"2019-08-22 20:59:12.489","resultCode":"Success","resultMessage":"The operation succeeded"}}
```

Další informace o schématu diagnostických protokolů v účtu úložiště najdete v tématu [archivace protokolů prostředků Azure do účtu úložiště](../azure-monitor/platform/resource-logs.md#send-to-azure-storage). Pokud chcete získat přístup k protokolům v účtu úložiště programově, použijte rozhraní API pro úložiště.

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
    "taskSlotsPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Události protokolu služby emitované službou Batch zahrnují následující:

- [Vytvoření fondu](batch-pool-create-event.md)
- [Spuštění odstranění fondu](batch-pool-delete-start-event.md)
- [Odstranění fondu bylo dokončeno.](batch-pool-delete-complete-event.md)
- [Začátek změny velikosti fondu](batch-pool-resize-start-event.md)
- [Změna velikosti fondu dokončena](batch-pool-resize-complete-event.md)
- [Automatické škálování fondu](batch-pool-autoscale-event.md)
- [Začátek úlohy](batch-task-start-event.md)
- [Úloha dokončena](batch-task-complete-event.md)
- [Selhání úlohy](batch-task-fail-event.md)
- [Plán úlohy selže](batch-task-schedule-fail-event.md)

## <a name="next-steps"></a>Další kroky

- Další informace o dostupných [rozhraních API a nástrojích služby Batch](batch-apis-tools.md) pro sestavování řešení Batch.
- Přečtěte si další informace o [monitorování řešení Batch](monitoring-overview.md).
