---
title: Azure Machine Learning monitorování | Microsoft Docs
description: Naučte se používat Azure Monitor k zobrazení, analýze a vytváření výstrah na metrikách z Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 10/01/2020
ms.openlocfilehash: b8ca96dd0b11f7a4c76f7a954959ef5005fb4a40
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93323987"
---
# <a name="monitor-azure-machine-learning"></a>Monitorování Azure Machine Learningu

Pokud máte důležité aplikace a obchodní procesy spoléhající se na prostředky Azure, budete chtít tyto prostředky sledovat pro jejich dostupnost, výkon a provoz. Tento článek popisuje data monitorování vygenerovaná nástrojem Azure Machine Learning a postup analýzy a upozornění na tato data pomocí Azure Monitor.

> [!TIP]
> Informace v tomto dokumentu jsou primárně určené pro správce, protože popisují monitorování Azure Machine Learning na úrovni *pracovního prostoru* . Pokud jste odborníkem na data nebo vývojářem a chcete monitorovat informace, které jsou specifické pro vaše *běhy školicích kurzů* , přečtěte si následující dokumenty:
>
> * [Spuštění, monitorování a zrušení školicích běhů](how-to-manage-runs.md)
> * [Protokolování metrik pro tréninková spuštění](how-to-track-experiments.md)
> * [Sledování experimentů s využitím MLflow](how-to-use-mlflow.md)
> * [Vizualizace spuštění s využitím TensorBoardu](how-to-monitor-tensorboard.md)

## <a name="what-is-azure-monitor"></a>Co je Azure Monitor?

Azure Machine Learning vytváří data monitorování pomocí [Azure monitor](../azure-monitor/overview.md), což je plná služba monitorování zásobníku v Azure. Azure Monitor poskytuje kompletní sadu funkcí pro monitorování prostředků Azure. Může také monitorovat prostředky v jiných cloudech i v místním prostředí.

Začněte s článkem [monitorování prostředků Azure pomocí Azure monitor](../azure-monitor/insights/monitor-azure-resource.md), který popisuje následující koncepty:

- Co je Azure Monitor?
- Náklady spojené s monitorováním
- Monitorování dat shromážděných v Azure
- Konfigurace shromažďování dat
- Standardní nástroje v Azure pro analýzu a upozorňování na data monitorování

Následující části jsou uvedené v tomto článku, které popisují konkrétní data shromážděná pro Azure Machine Learning. Tyto části obsahují také příklady konfigurace shromažďování dat a analýzy těchto dat pomocí nástrojů Azure.

> [!TIP]
> Pokud chcete pochopit náklady spojené s Azure Monitor, přečtěte si téma [využití a odhadované náklady](../azure-monitor/platform/usage-estimated-costs.md). Pokud chcete pochopit, jak dlouho trvá, aby se data zobrazovala v Azure Monitor, přečtěte si část [Doba přijímání dat protokolu](../azure-monitor/platform/data-ingestion-time.md).

## <a name="monitoring-data-from-azure-machine-learning"></a>Monitorování dat z Azure Machine Learning

Azure Machine Learning shromažďuje stejné typy dat monitorování jako další prostředky Azure, které jsou popsány v tématu [monitorování dat z prostředků Azure](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). 

Podrobné informace o protokolech a metrikách vytvořených pomocí Azure Machine Learning najdete v tématu [referenční informace k datům monitorování Azure Machine Learning](monitor-resource-reference.md) .

<a id="configuration"></a>

## <a name="collection-and-routing"></a>Shromažďování a směrování

Metriky platforem a protokol aktivit se shromažďují a ukládají automaticky, ale můžete je směrovat do jiných umístění pomocí diagnostického nastavení.  

Protokoly prostředků se neshromažďují a ukládají, dokud nevytvoříte nastavení diagnostiky a nebudete je směrovat do jednoho nebo více umístění.

Podrobný postup pro vytvoření nastavení diagnostiky pomocí Azure Portal, CLI nebo PowerShellu najdete v tématu [Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik platforem v Azure](../azure-monitor/platform/diagnostic-settings.md) . Při vytváření nastavení diagnostiky určíte, které kategorie protokolů se mají shromáždit. Kategorie pro Azure Machine Learning jsou uvedeny v referenčních informacích o [monitorování Azure Machine Learning](monitor-resource-reference.md#resource-logs).

> [!IMPORTANT]
> Povolení těchto nastavení vyžaduje další služby Azure (účet úložiště, centrum událostí nebo Log Analytics), což může zvýšit vaše náklady. Pokud chcete vypočítat odhadované náklady, přejděte na [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator).

Pro Azure Machine Learning můžete nakonfigurovat následující protokoly:

| Kategorie | Popis |
|:---|:---|
| AmlComputeClusterEvent | Události z Azure Machine Learning výpočetních clusterů. |
| AmlComputeClusterNodeEvent | Události z uzlů v rámci Azure Machine Learning výpočetního clusteru. |
| AmlComputeJobEvent | Události z úloh běžících na Azure Machine Learning Compute. |

> [!NOTE]
> Pokud povolíte metriky v nastavení diagnostiky, informace o dimenzích nejsou aktuálně zahrnuty jako součást informací odesílaných do účtu úložiště, centra událostí nebo Log Analytics.

Metriky a protokoly, které můžete shromažďovat, jsou popsány v následujících částech.

## <a name="analyzing-metrics"></a>Analýza metrik

Metriky pro Azure Machine Learning můžete analyzovat společně s metrikami z jiných služeb Azure tak, že v nabídce **Azure monitor** otevřete **metriky** . Podrobnosti o použití tohoto nástroje najdete v tématu [Začínáme s Azure Průzkumník metrik](../azure-monitor/platform/metrics-getting-started.md) .

Seznam shromážděných metrik platformy najdete v tématu [monitorování Azure Machine Learning metriky referenčních dat](monitor-resource-reference.md#metrics).

Všechny metriky pro Azure Machine Learning jsou v oboru názvů **Machine Learning pracovním prostoru služby**.

![Průzkumník metrik s vybraným pracovním prostorem služby Machine Learning](./media/monitor-azure-machine-learning/metrics.png)

Pro referenci se zobrazí seznam [všech metrik prostředků, které jsou podporované v Azure monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="filtering-and-splitting"></a>Filtrování a rozdělování

Pro metriky, které podporují dimenze, můžete použít filtry pomocí hodnoty dimenze. Například filtrování **aktivních jader** pro **název clusteru** `cpu-cluster` . 

Metriku můžete také rozdělit podle dimenzí, abyste vizualizují, jak různé segmenty metriky jsou vzájemně porovnány. Například rozdělením **typu kroku kanálu** zobrazíte počet typů kroků použitých v kanálu.

Další informace o filtrování a rozdělování najdete v tématu [Pokročilé funkce Azure monitor](../azure-monitor/platform/metrics-charts.md).

<a id="analyzing-log-data"></a>
## <a name="analyzing-logs"></a>Analýza protokolů

Použití Azure Monitor Log Analytics vyžaduje, abyste vytvořili konfiguraci diagnostiky a povolili __odesílání informací do Log Analytics__. Další informace najdete v části [shromažďování a směrování](#collection-and-routing) .

Data v Azure Monitor protokoly se ukládají v tabulkách, přičemž každá tabulka má vlastní sadu jedinečných vlastností. Azure Machine Learning ukládá data v následujících tabulkách:

| Tabulka | Popis |
|:---|:---|
| AmlComputeClusterEvent | Události z Azure Machine Learning výpočetních clusterů. |
| AmlComputeClusterNodeEvent | Události z uzlů v rámci Azure Machine Learning výpočetního clusteru. |
| AmlComputeJobEvent | Události z úloh běžících na Azure Machine Learning Compute. |

> [!IMPORTANT]
> Když v nabídce Azure Machine Learning vyberete **protokoly** , Log Analytics se otevře s oborem dotazu nastaveným na aktuální pracovní prostor. To znamená, že dotazy protokolu budou zahrnovat pouze data z tohoto prostředku. Pokud chcete spustit dotaz, který zahrnuje data z jiných databází nebo dat z jiných služeb Azure, vyberte z nabídky **Azure monitor** **protokoly** . Podrobnosti najdete [v tématu Rozsah dotazů protokolu a časový rozsah v Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md) .

Podrobné informace o protokolech a metrikách naleznete v tématu Azure Machine Learning – referenční informace k [datům monitorování](monitor-resource-reference.md).

### <a name="sample-kusto-queries"></a>Ukázkové dotazy Kusto

> [!IMPORTANT]
> Když vyberete **protokoly** v nabídce [Service-Name], Log Analytics se otevře s oborem dotazu nastaveným na aktuální Azure Machine Learning pracovní prostor. To znamená, že dotazy protokolu budou zahrnovat pouze data z tohoto prostředku. Pokud chcete spustit dotaz, který obsahuje data z jiných pracovních prostorů nebo dat z jiných služeb Azure, vyberte z nabídky **Azure monitor** **protokoly** . Podrobnosti najdete [v tématu Rozsah dotazů protokolu a časový rozsah v Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md) .

Níže jsou uvedené dotazy, které vám pomůžou monitorovat prostředky Azure Machine Learning: 

+ Získejte neúspěšné úlohy za posledních pět dnů:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Získat záznamy pro určitý název úlohy:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Získejte události clusteru za posledních pět dní pro clustery, na kterých je velikost virtuálního počítače Standard_D1_V2:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Získat uzly přidělené za posledních osm dní:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="alerts"></a>Výstrahy

Výstrahy pro Azure Machine Learning můžete zpřístupnit otevřením **výstrahy** z nabídky **Azure monitor** . Podrobnosti o vytváření výstrah najdete v tématu [Vytvoření, zobrazení a správa výstrah metrik pomocí Azure monitor](../azure-monitor/platform/alerts-metric.md) .

V následující tabulce jsou uvedeny běžné a doporučené pravidla upozorňujících na metriky pro Azure Machine Learning:

| Typ upozornění | Stav | Popis |
|:---|:---|:---|
| Nasazení modelu se nezdařilo | Typ agregace: celkem, operátor: větší než, prahová hodnota: 0 | V případě neúspěchu jednoho nebo více nasazení modelů |
| Procento využití kvóty | Typ agregace: průměr, operátor: větší než, prahová hodnota: 90| Pokud je procento využití kvóty větší než 90% |
| Nepoužité uzly | Typ agregace: celkem, operátor: větší než, prahová hodnota: 0 | Když je jeden nebo více nepoužitelných uzlů |

## <a name="next-steps"></a>Další kroky

- Referenční informace o protokolech a metrikách naleznete v tématu [Monitoring Azure Machine Learning data reference](monitor-resource-reference.md).
- Informace o práci s kvótami týkajícími se Azure Machine Learning najdete v tématu [Správa a vyžádání kvót pro prostředky Azure](how-to-manage-quotas.md).
- Podrobnosti o monitorování prostředků Azure najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](../azure-monitor/insights/monitor-azure-resource.md).
