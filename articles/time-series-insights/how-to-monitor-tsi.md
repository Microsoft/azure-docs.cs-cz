---
title: Time Series Insights monitorování | Microsoft Docs
description: Monitorování Time Series Insights dostupnosti, výkonu a provozu.
author: deepakpalled
ms.author: lyhughes
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2020
ms.custom: lyrana
ms.openlocfilehash: cff0c54cf5aa8854273be9502f5cf6df4e0a055b
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632736"
---
# <a name="monitoring-time-series-insights"></a>Time Series Insights monitorování

Pokud máte důležité aplikace a obchodní procesy spoléhající se na prostředky Azure, budete chtít tyto prostředky sledovat pro jejich dostupnost, výkon a provoz. Tento článek popisuje data monitorování vygenerovaná nástrojem Time Series Insights a způsob použití funkcí Azure Monitor k analýze a upozornění na tato data.

## <a name="monitor-overview"></a>Přehled monitorování

Stránka **Přehled** v Azure Portal pro každé prostředí Time Series Insights obsahuje stručný přehled využití prostředků, jako je například počet přijatých zpráv a počet uložených bajtů. Tyto informace jsou užitečné, ale v tomto podokně jsou k dispozici pouze malé množství dat monitorování. Některá z těchto dat jsou shromažďována automaticky a jsou k dispozici pro analýzu ihned po vytvoření prostředku. Můžete povolit další typy shromažďování dat s určitou konfigurací.

## <a name="what-is-azure-monitor"></a>Co je Azure Monitor

Time Series Insights vytváří data monitorování pomocí [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview), což je plná služba monitorování zásobníku v Azure, která poskytuje kompletní sadu funkcí pro monitorování prostředků Azure kromě prostředků v jiných cloudech a místních prostředích.

Začněte s článkem [monitorování prostředků Azure pomocí Azure monitor](https://docs.microsoft.com/azure/azure-monitor/insights/monitor-azure-resource), který popisuje následující koncepty:

- Co je Azure Monitor?
- Náklady spojené s monitorováním
- Monitorování dat shromážděných v Azure
- Konfigurace shromažďování dat
- Standardní nástroje v Azure pro analýzu a upozorňování na data monitorování

Následující části jsou uvedené v tomto článku, které popisují konkrétní data shromážděná pro Azure Time Series Insights. Tyto části obsahují také příklady konfigurace shromažďování dat a analýzy těchto dat pomocí nástrojů Azure.

> [!TIP]
> Pokud chcete pochopit náklady spojené s Azure Monitor, přečtěte si téma [využití a odhadované náklady](../azure-monitor/platform/usage-estimated-costs.md). Pokud chcete pochopit, jak dlouho trvá, aby se data zobrazovala v Azure Monitor, přečtěte si část [Doba přijímání dat protokolu](../azure-monitor/platform/data-ingestion-time.md).

## <a name="monitoring-data-from-azure-time-series-insights"></a>Monitorování dat z Azure Time Series Insights

Azure Time Series Insights shromažďuje stejné typy dat monitorování jako další prostředky Azure, které jsou popsány v tématu [monitorování dat z prostředků Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). 

Podrobné informace o protokolech a metrikách, které můžete shromažďovat, najdete v tématu [referenční informace o monitorování dat Azure Time Series Insights](how-to-monitor-tsi-reference.md) .

## <a name="collection-and-routing"></a>Shromažďování a směrování

Metriky platformy se shromažďují a ukládají automaticky, ale můžou se směrovat do jiných umístění pomocí diagnostického nastavení.

Protokoly prostředků se neshromažďují a ukládají, dokud nevytvoříte nastavení diagnostiky a nebudete je směrovat do jednoho nebo více umístění.
Podrobný postup pro vytvoření nastavení diagnostiky pomocí Azure Portal, CLI nebo PowerShellu najdete v tématu [Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik platforem v Azure](../azure-monitor/platform/diagnostic-settings.md) . Při vytváření nastavení diagnostiky určíte, které kategorie protokolů se mají shromáždit.

Pro Azure Time Series Insights můžete shromažďovat protokoly z následujících kategorií:

   | Kategorie | Popis |
   |---|---|
   | Příchozí přenos dat  | Kategorie příchozího přenosu dat sleduje chyby, ke kterým došlo v kanálu příchozího přenosu dat. Tato kategorie zahrnuje chyby, ke kterým dochází při přijímání událostí (například selhání pro připojení ke zdroji událostí) a zpracování událostí (například chyby při analýze datové části události). |

## <a name="analyzing-metrics"></a>Analýza metrik

Metriky pro Azure Time Series Insights můžete analyzovat společně s metrikami z jiných služeb Azure tak, že v nabídce Azure Monitor otevřete metriky. Podrobnosti o použití tohoto nástroje najdete v tématu [Začínáme s Azure Průzkumník metrik](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-getting-started) .

Seznam shromážděných metrik platformy najdete v tématu [monitorování Azure Time Series Insights dat – referenční informace](how-to-monitor-tsi-reference.md#metrics)

V tomto příkladu se zobrazuje počet bajtů přijatých ze všech zdrojů událostí do prostředí Azure Time Series Insights.

**Příchozí bajty přijatých** bajtů [ ![ Azure Time Series pro příchozí](media/how-to-monitor-tsi/ingress-received-bytes.png)](media/how-to-monitor-tsi/ingress-received-bytes.png#lightbox) přenosy

V příkladu se zobrazuje počet bajtů úspěšně zpracovaných a dostupných pro dotaz ve vašem prostředí Azure Time Series Insights.

Uložené bajty příchozího přenosu dat v **bajtech** [ ![ Azure Time Series](media/how-to-monitor-tsi/ingress-stored-bytes.png)](media/how-to-monitor-tsi/ingress-stored-bytes.png#lightbox)

## <a name="analyzing-logs"></a>Analýza protokolů
Protokoly prostředků můžete přistupovat buď jako objekt BLOB v účtu úložiště, jako data události, nebo prostřednictvím analytických dotazů log.

Data v Azure Monitor protokoly se ukládají v tabulkách, ve kterých každá tabulka má vlastní sadu jedinečných vlastností.

Všechny protokoly prostředků v Azure Monitor mají stejná pole následovaná poli pro konkrétní služby. Společné schéma je popsáno v [Azure monitor schéma protokolu prostředků](../azure-monitor/platform/resource-logs-schema.md#top-level-common-schema). Seznam typů protokolů prostředků shromážděných pro Azure Time Series Insights naleznete v tématu [Azure Time Series Insights monitoring data reference](how-to-monitor-tsi-reference.md#resource-logs).

Azure Time Series Insights ukládá data v následujících tabulkách.

| Tabulka | Popis |
|:---|:---|
| TSIIngress | Tabulka, která ukládá data z kategorie příchozího přenosu dat Kategorie příchozího přenosu dat sleduje chyby, ke kterým došlo v kanálu příchozího přenosu dat. Tato kategorie zahrnuje chyby, ke kterým dochází při přijímání událostí (například selhání pro připojení ke zdroji událostí) a zpracování událostí (například chyby při analýze datové části události).

Chcete-li směrovat data do protokolů Azure Monitor, je nutné vytvořit nastavení diagnostiky k odeslání protokolů prostředků nebo metriky platformy do pracovního prostoru Log Analytics. Další informace najdete v tématu [shromažďování a směrování](https://docs.microsoft.com/azure/iot-hub/monitor-iot-hub#collection-and-routing).

## <a name="sample-queries"></a>Ukázkové dotazy

Níže jsou uvedené dotazy, které vám pomůžou monitorovat Azure Time Series Insights prostředí:

+ Získat podrobnosti o selhání připojení ke zdroji událostí za posledních pět dní:

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/connect"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```
+ Získat podrobnosti o neplatných zprávách přijatých za posledních pět dnů:

    ```Kusto
   TSIIngress
   | where OperationName == "Microsoft.TimeSeriesInsights/environments/eventsources/ingress/deserialize"
   | where _ResourceId contains "<your environment name, event source name, or the full event source resource URL>"
   | where TimeGenerated > ago(5d)

    ```

## <a name="alerts"></a>Výstrahy

Azure Monitor výstrahy proaktivně upozorní na to, že se ve vašich datech monitorování nacházejí důležité podmínky. Umožňují identifikovat a řešit problémy v systému před tím, než si je vaši zákazníci všimnete. Můžete nastavit výstrahy na [metrikách](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-metric-overview), [protokolech](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)a [protokolu aktivit](https://docs.microsoft.com/azure/azure-monitor/platform/activity-log-alerts). Různé typy výstrah mají výhody a nevýhody.

Při vytváření pravidla výstrahy na základě metrik platforem mějte na paměti, že pro Time Series Insights metriky platforem, které jsou shromažďovány v jednotkách Count, nemusí být některé agregace dostupné nebo použitelné.

## <a name="next-steps"></a>Další kroky

* Odkaz na protokoly a metriky vytvořené pomocí Azure Time Series Insights najdete v tématu informace o [monitorování Azure Time Series Insights dat](how-to-monitor-tsi-reference.md) .
* Podrobnosti o monitorování prostředků Azure najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) .
