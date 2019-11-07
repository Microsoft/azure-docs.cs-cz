---
title: Referenční informace o monitorování dat | Microsoft Docs
titleSuffix: Azure Machine Learning
description: Seznamte se s daty a prostředky shromážděnými pro Azure Machine Learning a k dispozici v Azure Monitor. Azure Monitor shromažďuje a dostavuje data o pracovním prostoru Azure Machine Learning a umožňuje vám zobrazovat metriky, nastavit výstrahy a analyzovat data protokolu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 11/06/2019
ms.openlocfilehash: 178c60d82c8337987e9858a409fcdef7063c9559
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73684907"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Referenční informace o monitorování služby Azure Machine Learning

Seznamte se s daty a prostředky shromažďovanými nástrojem Azure Monitor z pracovního prostoru Azure Machine Learning. Podrobnosti o shromažďování a analýze dat monitorování najdete v tématu [monitorování Azure Machine Learning](monitor-azure-machine-learning.md) .

## <a name="resource-logs"></a>Protokoly prostředků

V následující tabulce jsou uvedeny vlastnosti Azure Machine Learningch protokolů prostředků při jejich shromažďování v protokolech Azure Monitor nebo Azure Storage.

### <a name="amlcomputejobevents-table"></a>Tabulka AmlComputeJobEvents

| Vlastnost | Popis |
|:--- |:---|
| TimeGenerated | Čas, kdy byla vygenerována položka protokolu |
| OperationName | Název operace přidružené k události protokolu |
| Kategorie | Název události protokolu, AmlComputeClusterNodeEvent |
| JobId | ID odeslané úlohy |
| ExperimentId | ID experimentu |
| experiment | Název experimentu |
| customerSubscriptionId | SubscriptionId, kde se povedl experiment a úloha |
| workspaceName | Název pracovního prostoru Machine Learning |
| clusterName | Název clusteru |
| ProvisioningState | Stav odeslání úlohy |
| ResourceGroupName | Název skupiny prostředků |
| JobName | Název úlohy |
| ClusterId | ID clusteru |
| Typ | Typ události úlohy, například JobSubmitted, JobRunning, JobFailed, JobSucceeded atd. |
| ExecutionState | Stav úlohy (běh), např. ve frontě, spuštěno, úspěšné, neúspěšné |
| ErrorDetails | Podrobnosti o chybě úlohy |
| CreationApiVersion | Verze rozhraní API, která se používá k vytvoření úlohy |
| ClusterResourceGroupName | Název skupiny prostředků clusteru |
| TFWorkerCount | Počet pracovních procesů TF |
| TFParameterServerCount | Počet parametrů serveru TF |
| ToolType | Typ používaného nástroje |
| RunInContainer | Příznak popisující, zda se má úloha spustit uvnitř kontejneru |
| JobErrorMessage | Podrobná zpráva o chybě úlohy |
| NodeId | ID uzlu, který byl vytvořen v případě běhu úlohy |

### <a name="amlcomputeclusterevents-table"></a>Tabulka AmlComputeClusterEvents

| Vlastnost | Popis |
|:--- |:--- |
| TimeGenerated | Čas, kdy byla vygenerována položka protokolu |
| OperationName | Název operace přidružené k události protokolu |
| Kategorie | Název události protokolu, AmlComputeClusterNodeEvent |
| ProvisioningState | Stav zřizování clusteru |
| clusterName | Název clusteru |
| clusterType | Typ clusteru |
| CreatedBy | Uživatel, který vytvořil cluster |
| CoreCount | Počet jader v clusteru |
| vmSize | Velikost virtuálního počítače v clusteru |
| VmPriority | Priorita uzlů vytvořených v rámci vyhrazeného/LowPriority clusteru |
| ScalingType | Typ ručního a automatického škálování clusteru |
| InitialNodeCount | Počáteční počet uzlů clusteru |
| MinimumNodeCount | Minimální počet uzlů clusteru |
| MaximumNodeCount | Maximální počet uzlů clusteru |
| NodeDeallocationOption | Jak se má uzel uvolnit |
| Vydavatel | Vydavatel typu clusteru |
| Nabídka | Nabídka, se kterou je cluster vytvořený |
| Skladová jednotka (SKU) | SKU uzlu nebo virtuálního počítače vytvořeného v rámci clusteru |
| Verze | Verze image, která se používá při vytváření uzlu nebo virtuálního počítače |
| SubnetId | SubnetId clusteru |
| AllocationState | Stav přidělení clusteru |
| CurrentNodeCount | Aktuální počet uzlů clusteru |
| TargetNodeCount | Počet cílových uzlů clusteru při vertikálním navýšení nebo zmenšení |
| Typ | Typ události při vytváření clusteru |
| NodeIdleTimeSecondsBeforeScaleDown | Doba nečinnosti v sekundách, po jejímž uplynutí se cluster zmenší |
| PreemptedNodeCount | Počet zrušených uzlů v clusteru |
| IsResizeGrow | Příznak označující, že cluster je škálovatelný |
| VmFamilyName | Název rodiny virtuálních počítačů uzlů, které se dají vytvořit v rámci clusteru |
| LeavingNodeCount | Zanechává se počet uzlů clusteru. |
| UnusableNodeCount | Počet nepoužitelných uzlů clusteru |
| IdleNodeCount | Počet nečinných uzlů clusteru |
| RunningNodeCount | Počet spuštěných uzlů clusteru |
| PreparingNodeCount | Připravuje se počet uzlů clusteru. |
| QuotaAllocated | Přidělená kvóta clusteru |
| QuotaUtilized | Využitá kvóta clusteru |
| AllocationStateTransitionTime | Doba přechodu z jednoho stavu do druhého |
| ClusterErrorCodes | Kód chyby přijatý při vytváření nebo škálování clusteru |
| CreationApiVersion | Verze rozhraní API použitá při vytváření clusteru |

### <a name="amlcomputeclusternodeevents-table"></a>Tabulka AmlComputeClusterNodeEvents

| Vlastnost | Popis |
|:--- |:--- |
| TimeGenerated | Čas, kdy byla vygenerována položka protokolu |
| OperationName | Název operace přidružené k události protokolu |
| Kategorie | Název události protokolu, AmlComputeClusterNodeEvent |
| clusterName | Název clusteru |
| NodeId | ID vytvořeného uzlu clusteru |
| vmSize | Velikost virtuálního počítače uzlu |
| VmFamilyName | Rodina virtuálních počítačů, do které uzel patří |
| VmPriority | Priorita uzlu, který byl vytvořen jako vyhrazený/LowPriority |
| Vydavatel | Vydavatel image virtuálního počítače, třeba Microsoft-dsvm |
| Nabídka | Nabídka přidružená k vytvoření virtuálního počítače |
| Skladová jednotka (SKU) | SKU vytvořeného uzlu nebo virtuálního počítače |
| Verze | Verze image, která se používá při vytváření uzlu nebo virtuálního počítače |
| ClusterCreationTime | Čas, kdy byl cluster vytvořen |
| ResizeStartTime | Doba, po kterou bylo spuštěno navýšení nebo snížení kapacity clusteru |
| ResizeEndTime | Čas, kdy bylo ukončeno horizontální navýšení kapacity clusteru |
| NodeAllocationTime | Čas, kdy byl uzel přidělen |
| NodeBootTime | Čas, kdy se uzel spustil |
| StartTaskStartTime | Čas, kdy byl úkol přiřazen uzlu a spuštěn |
| StartTaskEndTime | Čas, kdy byl dokončen úkol přiřazený uzlu |
| TotalE2ETimeInSeconds | Celkový čas aktivního uzlu |

### <a name="metrics"></a>Metriky

V následujících tabulkách jsou uvedeny metriky platforem shromážděné pro Azure Machine Learning všechny metriky jsou uloženy v oboru názvů **Azure Machine Learning pracovním prostoru služby**.

**Vzorový**

| Model | Jednotka | Popis |
| ----- | ----- | ----- |
| Nasazení modelu selhalo. | Počet | Počet nasazení modelů, které selhaly. |
| Nasazení modelu začalo | Počet | Počet spuštěných nasazení modelu |
| Nasazení modelu bylo úspěšné. | Počet | Počet nasazení modelů, které byly úspěšně dokončeny. |
| Nepovedlo se zaregistrovat model | Počet | Počet registrací modelu, které selhaly. |
| Registr modelu byl úspěšný. | Počet | Počet registrací modelů, které byly úspěšně dokončeny. |

**Kvóta**

Informace o kvótách jsou jenom Azure Machine Learning výpočetní služby.

| Metrika | Jednotka | Popis |
| ----- | ----- | ----- |
| Aktivní jádra | Počet | Počet aktivních výpočetních jader. |
| Aktivní uzly | Počet | Počet aktivních uzlů. |
| Jádra nečinných | Počet | Počet nečinných výpočetních jader. |
| Nečinné uzly | Počet | Počet nečinných výpočetních uzlů. |
| Vynechávání jader | Počet | Počet opouštících jader. |
| Ukončení uzlů | Počet | Počet opouštících uzlů. |
| Zrušené jádra | Počet | Počet zrušených jader. |
| Přepnuté uzly | Počet | Počet přerušených uzlů. |
| Procento využití kvóty | Procento | Procento využité kvóty |
| Celkový počet jader | Počet | Celkový počet jader. |
| Celkem uzlů | Počet | Celkový počet uzlů. |
| Nepoužitelné jádra | Počet | Počet nepoužitelných jader. |
| Nepoužité uzly | Počet | Počet nepoužitelných uzlů. |

Níže jsou uvedené dimenze, které lze použít k filtrování metrik kvót:

| Dimenze | Metriky dostupné s | Popis |
| ---- | ---- | ---- |
| Název clusteru | Všechny metriky kvót | Název výpočetní instance. |
| Název rodiny virtuálních počítačů | Procento využití kvóty | Název rodiny virtuálních počítačů, kterou cluster používá. |
| Priorita virtuálního počítače | Procento využití kvóty | Priorita virtuálního počítače.

**Spouštěl**

Informace o spuštěných školicích kurzech.

| Metrika | Jednotka | Popis |
| ----- | ----- | ----- |
| Dokončená spuštění | Počet | Počet dokončených spuštění. |
| Neúspěšná spuštění | Počet | Počet neúspěšných spuštění. |
| Spuštěná spuštění | Počet | Počet spuštěných spuštění. |

Níže jsou uvedené dimenze, které lze použít k filtrování metrik spuštění:

| Dimenze | Popis |
| ---- | ---- |
| ComputeType | Typ výpočtu, který se používá. |
| PipelineStepType | Typ [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py) použitý v běhu. |
| PublishedPipelineId | ID publikovaného kanálu používaného v běhu |
| RunType | Typ běhu. |

Platné hodnoty pro dimenzi RunType jsou:

| Hodnota | Popis |
| ----- | ----- |
| Začátku | Spuštění bez kanálu. |
| PipelineRun | Spuštění kanálu, které je nadřazeným objektem StepRun. |
| StepRun | Spuštění pro krok kanálu. |
| ReusedStepRun | Spuštění pro krok kanálu, který znovu používá předchozí běh. |

## <a name="see-also"></a>Viz také

- Popis Azure Machine Learning monitorování najdete v tématu [monitorování Azure Machine Learning](monitor-azure-machine-learning.md) .
- Podrobnosti o monitorování prostředků Azure najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](/azure/azure-monitor/insights/monitor-azure-resource) .
