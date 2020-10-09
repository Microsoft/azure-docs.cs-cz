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
ms.date: 03/05/2020
ms.openlocfilehash: eb4f46322bec57fb4412d3ddebb345640556ca5c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "78399097"
---
# <a name="monitoring-azure-machine-learning"></a>Azure Machine Learning monitorování

Tento článek popisuje data monitorování vygenerovaná nástrojem Azure Machine Learning. Také popisuje, jak můžete použít Azure Monitor k analýze dat a definování výstrah.

> [!TIP]
> Informace v tomto dokumentu jsou primárně určené pro správce, protože popisují monitorování Azure Machine Learning. Pokud jste odborníkem na data nebo vývojářem a chcete monitorovat informace, které jsou specifické pro vaše běhy školicích kurzů, přečtěte si následující dokumenty:
>
> * [Spuštění, monitorování a zrušení školicích běhů](how-to-manage-runs.md)
> * [Protokolování metrik pro tréninková spuštění](how-to-track-experiments.md)
> * [Sledování experimentů s využitím MLflow](how-to-use-mlflow.md)
> * [Vizualizace spuštění s využitím TensorBoardu](how-to-monitor-tensorboard.md)

## <a name="azure-monitor"></a>Azure Monitor

Azure Machine Learning protokoluje data monitorování pomocí Azure Monitor, což je plná služba monitorování zásobníku v Azure. Azure Monitor poskytuje kompletní sadu funkcí pro monitorování prostředků Azure. Může také monitorovat prostředky v jiných cloudech i v místním prostředí.

Začněte s článkem [Azure monitor přehled](/azure/azure-monitor/overview), který poskytuje přehled možností monitorování. Následující části obsahují informace o těchto informacích, které poskytují konkrétní použití Azure Monitor s Azure Machine Learning.

Pokud chcete pochopit náklady spojené s Azure Monitor, přečtěte si téma [využití a odhadované náklady](/azure/azure-monitor/platform/usage-estimated-costs). Pokud chcete pochopit, jak dlouho trvá, aby se data zobrazovala v Azure Monitor, přečtěte si část [Doba přijímání dat protokolu](/azure/azure-monitor/platform/data-ingestion-time).

## <a name="monitoring-data-from-azure-machine-learning"></a>Monitorování dat z Azure Machine Learning

Azure Machine Learning shromažďuje stejné typy dat monitorování jako jiné prostředky Azure, které jsou popsány v tématu [monitorování dat z prostředků Azure](/azure/azure-monitor/insights/monitor-azure-resource#monitoring-data). Podrobné informace o protokolech a metrikách vytvořených pomocí Azure Machine Learning najdete v tématu [referenční informace k datům monitorování Azure Machine Learning](monitor-resource-reference.md) .

## <a name="analyzing-metric-data"></a>Analýza dat metriky

Metriky pro Azure Machine Learning můžete analyzovat otevřením **metrik** v nabídce **Azure monitor** . Podrobnosti o použití tohoto nástroje najdete v tématu [Začínáme s Azure Průzkumník metrik](/azure/azure-monitor/platform/metrics-getting-started) .

Všechny metriky pro Azure Machine Learning jsou v oboru názvů **Machine Learning pracovním prostoru služby**.

![Průzkumník metrik s vybraným pracovním prostorem služby Machine Learning](./media/monitor-azure-machine-learning/metrics.png)

### <a name="filtering-and-splitting"></a>Filtrování a rozdělování

Pro metriky, které podporují dimenze, můžete použít filtry pomocí hodnoty dimenze. Například filtrování **aktivních jader** pro **název clusteru** `cpu-cluster` . 

Metriku můžete také rozdělit podle dimenzí, abyste vizualizují, jak různé segmenty metriky jsou vzájemně porovnány. Například rozdělením **typu kroku kanálu** zobrazíte počet typů kroků použitých v kanálu.

Další informace o filtrování a rozdělování najdete v tématu [Pokročilé funkce Azure monitor](/azure/azure-monitor/platform/metrics-charts).

## <a name="alerts"></a>Výstrahy

Výstrahy pro Azure Machine Learning můžete zpřístupnit otevřením **výstrahy** z nabídky **Azure monitor** . Podrobnosti o vytváření výstrah najdete v tématu [Vytvoření, zobrazení a správa výstrah metrik pomocí Azure monitor](/azure/azure-monitor/platform/alerts-metric) .

V následující tabulce jsou uvedeny běžné a doporučené pravidla upozorňujících na metriky pro Azure Machine Learning:

| Typ upozornění | Stav | Popis |
|:---|:---|:---|
| Nasazení modelu se nezdařilo | Typ agregace: celkem, operátor: větší než, prahová hodnota: 0 | V případě neúspěchu jednoho nebo více nasazení modelů |
| Procento využití kvóty | Typ agregace: průměr, operátor: větší než, prahová hodnota: 90| Pokud je procento využití kvóty větší než 90% |
| Nepoužité uzly | Typ agregace: celkem, operátor: větší než, prahová hodnota: 0 | Když je jeden nebo více nepoužitelných uzlů |

## <a name="configuration"></a>Konfigurace

> [!IMPORTANT]
> __Metriky pro Azure Machine Learning nemusíte konfigurovat__, jsou shromažďovány automaticky a jsou k dispozici v Průzkumník metrik pro monitorování a upozorňování.

Můžete přidat nastavení diagnostiky pro konfiguraci následujících funkcí:

* Archivujte informace protokolu a metrik do účtu služby Azure Storage.
* Zasílat informace o protokolech a metrikách do centra událostí Azure.
* Odesílat informace o protokolech a metrikách Azure Monitor Log Analytics.

Povolení těchto nastavení vyžaduje další služby Azure (účet úložiště, centrum událostí nebo Log Analytics), což může zvýšit vaše náklady. Pokud chcete vypočítat odhadované náklady, přejděte na [cenové kalkulačky Azure](https://azure.microsoft.com/pricing/calculator).

Další informace o vytváření nastavení diagnostiky najdete v tématu [Vytvoření nastavení diagnostiky pro shromažďování protokolů a metrik platforem v Azure](/azure/azure-monitor/platform/diagnostic-settings).

Pro Azure Machine Learning můžete nakonfigurovat následující protokoly:

| Kategorie | Popis |
|:---|:---|
| AmlComputeClusterEvent | Události z Azure Machine Learning výpočetních clusterů. |
| AmlComputeClusterNodeEvent | Události z uzlů v rámci Azure Machine Learning výpočetního clusteru. |
| AmlComputeJobEvent | Události z úloh běžících na Azure Machine Learning Compute. |

> [!NOTE]
> Pokud povolíte metriky v nastavení diagnostiky, informace o dimenzích nejsou aktuálně zahrnuty jako součást informací odesílaných do účtu úložiště, centra událostí nebo Log Analytics.

## <a name="analyzing-log-data"></a>Analýza dat protokolu

Použití Azure Monitor Log Analytics vyžaduje, abyste vytvořili konfiguraci diagnostiky a povolili __odesílání informací do Log Analytics__. Další informace najdete v části věnované [konfiguraci](#configuration) .

Data v Azure Monitor protokoly se ukládají v tabulkách, přičemž každá tabulka má vlastní sadu jedinečných vlastností. Azure Machine Learning ukládá data v následujících tabulkách:

| Tabulka | Popis |
|:---|:---|
| AmlComputeClusterEvent | Události z Azure Machine Learning výpočetních clusterů. |
| AmlComputeClusterNodeEvent | Události z uzlů v rámci Azure Machine Learning výpočetního clusteru. |
| AmlComputeJobEvent | Události z úloh běžících na Azure Machine Learning Compute. |

> [!IMPORTANT]
> Když v nabídce Azure Machine Learning vyberete **protokoly** , Log Analytics se otevře s oborem dotazu nastaveným na aktuální pracovní prostor. To znamená, že dotazy protokolu budou zahrnovat pouze data z tohoto prostředku. Pokud chcete spustit dotaz, který zahrnuje data z jiných databází nebo dat z jiných služeb Azure, vyberte z nabídky **Azure monitor** **protokoly** . Podrobnosti najdete [v tématu Rozsah dotazů protokolu a časový rozsah v Azure Monitor Log Analytics](/azure/azure-monitor/log-query/scope/) .

Podrobné informace o protokolech a metrikách naleznete v tématu Azure Machine Learning – referenční informace k [datům monitorování](monitor-resource-reference.md).

### <a name="sample-queries"></a>Ukázkové dotazy

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

## <a name="next-steps"></a>Další kroky

- Referenční informace o protokolech a metrikách naleznete v tématu [Azure Machine Learning – referenční informace k datům monitorování](monitor-resource-reference.md).
- Informace o práci s kvótami týkajícími se Azure Machine Learning najdete v tématu [Správa a vyžádání kvót pro prostředky Azure](how-to-manage-quotas.md).
- Podrobnosti o monitorování prostředků Azure najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](/azure/azure-monitor/insights/monitor-azure-resource).
