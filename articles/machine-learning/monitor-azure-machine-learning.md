---
title: Monitorování Azure Machine Learning | Dokumenty společnosti Microsoft
description: Zjistěte, jak pomocí Azure Monitoru zobrazit, analyzovat a vytvářet výstrahy na metriky z Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/05/2020
ms.openlocfilehash: eb4f46322bec57fb4412d3ddebb345640556ca5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78399097"
---
# <a name="monitoring-azure-machine-learning"></a>Monitorování Azure Machine Learning

Tento článek popisuje data monitorování generovaná Azure Machine Learning. Také popisuje, jak můžete pomocí Azure Monitor u analyzovat data a definovat výstrahy.

> [!TIP]
> Informace v tomto dokumentu je především pro správce, protože popisuje monitorování pro Azure Machine Learning. Pokud jste datový vědec nebo vývojář a chcete sledovat informace specifické pro spuštění trénovacího modelu, podívejte se na následující dokumenty:
>
> * [Spuštění, monitorování a zrušení tréninkových běhů](how-to-manage-runs.md)
> * [Protokolování metrik pro tréninková spuštění](how-to-track-experiments.md)
> * [Sledování experimentů s využitím MLflow](how-to-use-mlflow.md)
> * [Vizualizace spuštění s využitím TensorBoardu](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure Monitor

Azure Machine Learning protokoluje data monitorování pomocí Azure Monitoru, což je služba monitorování celého zásobníku v Azure. Azure Monitor poskytuje kompletní sadu funkcí pro monitorování vašich prostředků Azure. Může také sledovat prostředky v jiných cloudech a v místním prostředí.

Začněte s článkem [Azure Monitor přehled](/azure/azure-monitor/overview), který poskytuje přehled možností monitorování. Následující části na základě těchto informací poskytují specifika používání Azure Monitor u Azure Machine Learning.

Informace o nákladech spojených s Azure Monitorem najdete v [tématu Využití a odhadované náklady](/azure/azure-monitor/platform/usage-estimated-costs). Informace o době, po kterou se vaše data v Azure Monitoru zobrazují, najdete v [tématu Protokolování doby příjemdat](/azure/azure-monitor/platform/data-ingestion-time).

## <a name="monitoring-data-from-azure-machine-learning"></a>Data monitorování z Azure Machine Learning

Azure Machine Learning shromažďuje stejné druhy dat monitorování jako ostatní prostředky Azure, které jsou popsané v [části Monitorování dat z prostředků Azure](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data). Podrobný odkaz na protokoly a metriky vytvořené Azure Machine Learning najdete [v tématu Odkaz na data monitorování Azure Machine](monitor-resource-reference.md) Learning.

## <a name="analyzing-metric-data"></a>Analýza dat metriky

Metriky pro Azure Machine Learning můžete analyzovat otevřením **metrik z** nabídky **Azure Monitor.** Podrobnosti o používání tohoto nástroje najdete v tématu [Začínáme s Průzkumníkem metrik Azure.](/azure/azure-monitor/platform/metrics-getting-started)

Všechny metriky pro Azure Machine Learning jsou v oboru názvů **Machine Learning Service Workspace**.

![Průzkumník metrik s vybraným pracovním prostorem služby Machine Learning Service](./media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>Filtrování a štípání

U metrik, které podporují dimenze, můžete použít filtry pomocí hodnoty dimenze. Můžete například filtrovat **aktivní jádra** `cpu-cluster`pro název **clusteru** . 

Metriku můžete také rozdělit podle dimenze a vizualizovat, jak se různé segmenty metriky vzájemně porovnávají. Například rozdělení **typu kroku kanálu** zobrazíte počet typů kroků použitých v kanálu.

Další informace o filtrování a rozdělení najdete v [tématu Pokročilé funkce Azure Monitoru](/azure/azure-monitor/platform/metrics-charts).

## <a name="alerts"></a>Výstrahy

K výstraham pro Azure Machine Learning můžete získat přístup otevřením **výstrah** z nabídky **Azure Monitor.** Podrobnosti o vytváření výstrah najdete v tématu [Vytváření, zobrazení a správa upozornění na metriky pomocí Azure Monitoru.](/azure/azure-monitor/platform/alerts-metric)

V následující tabulce jsou uvedena běžná a doporučená pravidla upozornění na metriky pro Azure Machine Learning:

| Typ upozornění | Podmínka | Popis |
|:---|:---|:---|
| Nasazení modelu se nezdařilo. | Typ agregace: Celkem, Operátor: Větší než, Prahová hodnota: 0 | Pokud došlo k selhání jednoho nebo více nasazení modelu |
| Procento využití kvóty | Typ agregace: Průměr, Operátor: Větší než, Prahová hodnota: 90| Pokud je procento využití kvóty větší než 90 % |
| Nepoužitelné uzly | Typ agregace: Celkem, Operátor: Větší než, Prahová hodnota: 0 | Pokud existuje jeden nebo více nepoužitelných uzlů |

## <a name="configuration"></a>Konfigurace

> [!IMPORTANT]
> __Metriky pro Azure Machine Learning není nutné nakonfigurovat__, jsou shromažďovány automaticky a jsou k dispozici v Průzkumníku metrik pro monitorování a upozorňování.

Můžete přidat diagnostické nastavení pro konfiguraci následujících funkcí:

* Archivujte informace o protokolu a metrikách do účtu úložiště Azure.
* Streamujte protokol a informace o metrikách do Centra událostí Azure.
* Odesílejte informace protokolu a metriky do Azure Monitor Log Analytics.

Povolení těchto nastavení vyžaduje další služby Azure (účet úložiště, centrum událostí nebo Log Analytics), což může zvýšit vaše náklady. Odhadovaná cena se najdete v [cenové kalkulačce Azure](https://azure.microsoft.com/pricing/calculator).

Další informace o vytvoření nastavení diagnostiky najdete v [tématu Vytvoření diagnostického nastavení pro shromažďování protokolů platformy a metrik v Azure](/azure/azure-monitor/platform/diagnostic-settings).

Pro Azure Machine Learning můžete nakonfigurovat následující protokoly:

| Kategorie | Popis |
|:---|:---|
| Událost AmlComputeClusterEvent | Události z výpočetních clusterů Azure Machine Learning. |
| Událost AmlComputeClusterNodeEvent | Události z uzlů v rámci výpočetního clusteru Azure Machine Learning. |
| AmlComputeJobEvent | Události z úloh spuštěných ve výpočetních prostředcích Azure Machine Learning. |

> [!NOTE]
> Když povolíte metriky v diagnostickém nastavení, informace o dimenzi nejsou aktuálně zahrnuty jako součást informací odeslaných do účtu úložiště, centra událostí nebo analýzy protokolů.

## <a name="analyzing-log-data"></a>Analýza dat protokolu

Použití Azure Monitor Log Analytics vyžaduje vytvoření diagnostické konfigurace a povolení __odesílání informací do Log Analytics__. Další informace naleznete v části [Konfigurace.](#configuration)

Data v protokolech monitorování Azure se ukládají v tabulkách, přičemž každá tabulka má vlastní sadu jedinečných vlastností. Azure Machine Learning ukládá data v následujících tabulkách:

| Table | Popis |
|:---|:---|
| Událost AmlComputeClusterEvent | Události z výpočetních clusterů Azure Machine Learning. |
| Událost AmlComputeClusterNodeEvent | Události z uzlů v rámci výpočetního clusteru Azure Machine Learning. |
| AmlComputeJobEvent | Události z úloh spuštěných ve výpočetních prostředcích Azure Machine Learning. |

> [!IMPORTANT]
> Když vyberete **protokoly** z nabídky Azure Machine Learning, Log Analytics se otevře s oborem dotazu nastaveným na aktuální pracovní prostor. To znamená, že dotazy protokolu budou obsahovat pouze data z tohoto prostředku. Pokud chcete spustit dotaz, který obsahuje data z jiných databází nebo data z jiných služeb Azure, vyberte **protokoly** z nabídky **Azure Monitor.** Podrobnosti najdete [v tématu Rozsah dotazu protokolu a časový rozsah v Azure Monitor Log Analytics.](/azure/azure-monitor/log-query/scope/)

Podrobný odkaz na protokoly a metriky najdete v [tématu Azure Machine Learning monitorování dat odkaz](monitor-resource-reference.md).

### <a name="sample-queries"></a>Ukázkové dotazy

Následují dotazy, které vám můžou pomoct se sledováním prostředků Azure Machine Learningu: 

+ Získejte neúspěšné úlohy za posledních pět dní:

    ```Kusto
    AmlComputeJobEvent
    | where TimeGenerated > ago(5d) and EventType == "JobFailed"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Získejte záznamy pro konkrétní název úlohy:

    ```Kusto
    AmlComputeJobEvent
    | where JobName == "automl_a9940991-dedb-4262-9763-2fd08b79d8fb_setup"
    | project  TimeGenerated , ClusterId , EventType , ExecutionState , ToolType
    ```

+ Získejte události clusteru v posledních pěti dnech pro clustery, kde je Standard_D1_V2 velikost virtuálního počítače:

    ```Kusto
    AmlComputeClusterEvent
    | where TimeGenerated > ago(4d) and VmSize == "STANDARD_D1_V2"
    | project  ClusterName , InitialNodeCount , MaximumNodeCount , QuotaAllocated , QuotaUtilized
    ```

+ Získejte přidělené uzly za posledních osm dní:

    ```Kusto
    AmlComputeClusterNodeEvent
    | where TimeGenerated > ago(8d) and NodeAllocationTime  > ago(8d)
    | distinct NodeId
    ```

## <a name="next-steps"></a>Další kroky

- Odkaz na protokoly a metriky najdete [v tématu Azure Machine Learning sledování dat odkaz](monitor-resource-reference.md).
- Informace o práci s kvótami souvisejícími s Azure Machine Learning najdete v [tématu Správa a vyžádání kvót pro prostředky Azure](how-to-manage-quotas.md).
- Podrobnosti o monitorování prostředků Azure najdete [v tématu Monitorování prostředků Azure pomocí Azure Monitoru](/azure/azure-monitor/insights/monitor-azure-resource).
