---
title: Referenční informace o monitorování dat | Microsoft Docs
titleSuffix: Azure Machine Learning
description: Seznamte se s daty a prostředky shromážděnými pro Azure Machine Learning a k dispozici v Azure Monitor. Azure Monitor shromažďuje a dostavuje data o pracovním prostoru Azure Machine Learning a umožňuje vám zobrazovat metriky, nastavit výstrahy a analyzovat data protokolu.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 04/27/2020
ms.openlocfilehash: 405b0aa051d0d1142d7dd4ccbf2bca4ef9cc3545
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89650598"
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
| Experiment | Název experimentu |
| CustomerSubscriptionId | SubscriptionId, kde se povedl experiment a úloha |
| WorkspaceName | Název pracovního prostoru Machine Learning |
| Název clusteru | Název clusteru |
| ProvisioningState | Stav odeslání úlohy |
| ResourceGroupName | Název skupiny prostředků |
| JobName | Název úlohy |
| ClusterId | ID clusteru |
| Typ události | Typ události úlohy, například JobSubmitted, JobRunning, JobFailed, JobSucceeded atd. |
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
| Název clusteru | Název clusteru |
| ClusterType | Typ clusteru |
| CreatedBy | Uživatel, který vytvořil cluster |
| CoreCount | Počet jader v clusteru |
| VmSize | Velikost virtuálního počítače v clusteru |
| VmPriority | Priorita uzlů vytvořených v rámci vyhrazeného/LowPriority clusteru |
| ScalingType | Typ ručního a automatického škálování clusteru |
| InitialNodeCount | Počáteční počet uzlů clusteru |
| MinimumNodeCount | Minimální počet uzlů clusteru |
| MaximumNodeCount | Maximální počet uzlů clusteru |
| NodeDeallocationOption | Jak se má uzel uvolnit |
| Publisher | Vydavatel typu clusteru |
| Nabídka | Nabídka, se kterou je cluster vytvořený |
| Skladová jednotka (SKU) | SKU uzlu nebo virtuálního počítače vytvořeného v rámci clusteru |
| Verze | Verze image, která se používá při vytváření uzlu nebo virtuálního počítače |
| SubnetId | SubnetId clusteru |
| AllocationState | Stav přidělení clusteru |
| CurrentNodeCount | Aktuální počet uzlů clusteru |
| TargetNodeCount | Počet cílových uzlů clusteru při vertikálním navýšení nebo zmenšení |
| Typ události | Typ události při vytváření clusteru |
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
| Název clusteru | Název clusteru |
| NodeId | ID vytvořeného uzlu clusteru |
| VmSize | Velikost virtuálního počítače uzlu |
| VmFamilyName | Rodina virtuálních počítačů, do které uzel patří |
| VmPriority | Priorita uzlu, který byl vytvořen jako vyhrazený/LowPriority |
| Publisher | Vydavatel image virtuálního počítače, třeba Microsoft-dsvm |
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

V následujících tabulkách jsou uvedeny metriky platforem shromážděné pro Azure Machine Learning všechny metriky jsou uloženy v oboru názvů **pracovní prostor Azure Machine Learning**.

**Model**

| Metrika | Jednotka | Description |
| ----- | ----- | ----- |
| Nasazení modelu selhalo. | Počet | Počet nasazení modelů, které selhaly. |
| Nasazení modelu začalo | Počet | Počet spuštěných nasazení modelu |
| Nasazení modelu bylo úspěšné. | Počet | Počet nasazení modelů, které byly úspěšně dokončeny. |
| Nepovedlo se zaregistrovat model | Počet | Počet registrací modelu, které selhaly. |
| Registr modelu byl úspěšný. | Počet | Počet registrací modelů, které byly úspěšně dokončeny. |

**Kvóta**

Informace o kvótách jsou jenom Azure Machine Learning výpočetní služby.

| Metrika | Jednotka | Description |
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
| Celkem jader | Počet | Celkový počet jader. |
| Celkem uzlů | Počet | Celkový počet uzlů. |
| Nepoužitelné jádra | Počet | Počet nepoužitelných jader. |
| Nepoužité uzly | Počet | Počet nepoužitelných uzlů. |

Níže jsou uvedené dimenze, které lze použít k filtrování metrik kvót:

| Rozměr | Metriky dostupné s | Description |
| ---- | ---- | ---- |
| Název clusteru | Všechny metriky kvót | Název výpočetní instance. |
| Název rodiny virtuálních počítačů | Procento využití kvóty | Název rodiny virtuálních počítačů, kterou cluster používá. |
| Priorita virtuálního počítače | Procento využití kvóty | Priorita virtuálního počítače.

**Prostředek**

| Metrika | Jednotka | Description |
| ----- | ----- | ----- |
| CpuUtilization | Procento | Kolik procent procesoru bylo využito pro daný uzel během běhu nebo úlohy. Tato metrika je publikována pouze v případě, že úloha běží na uzlu. Jedna úloha může používat jeden nebo více uzlů. Tato metrika je publikována na jeden uzel. |
| GpuUtilization | Procento | Kolik procent GPU bylo využito pro daný uzel během běhu nebo úlohy. Jeden uzel může mít jeden nebo více GPU. Tato metrika je publikována na základě GPU na jeden uzel. |

Níže jsou uvedené dimenze, které lze použít k filtrování metrik prostředků:

| Rozměr | Description |
| ----- | ----- |
| CreatedTime | |
| DeviceId | ID zařízení (GPU) K dispozici pouze pro GpuUtilization. |
| NodeId | ID uzlu, který byl vytvořen v případě běhu úlohy. |
| RunId | ID běhu/úlohy |

**Spustit**

Informace o spuštěných školicích kurzech.

| Metrika | Jednotka | Description |
| ----- | ----- | ----- |
| Dokončená spuštění | Počet | Počet dokončených spuštění. |
| Neúspěšná spuštění | Počet | Počet neúspěšných spuštění. |
| Spuštěná spuštění | Počet | Počet spuštěných spuštění. |

Níže jsou uvedené dimenze, které lze použít k filtrování metrik spuštění:

| Rozměr | Description |
| ---- | ---- |
| ComputeType | Typ výpočtu, který se používá. |
| PipelineStepType | Typ [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py&preserve-view=true) použitý v běhu. |
| PublishedPipelineId | ID publikovaného kanálu používaného v běhu |
| RunType | Typ běhu. |

Platné hodnoty pro dimenzi RunType jsou:

| Hodnota | Popis |
| ----- | ----- |
| Experiment | Spuštění bez kanálu. |
| PipelineRun | Spuštění kanálu, které je nadřazeným objektem StepRun. |
| StepRun | Spuštění pro krok kanálu. |
| ReusedStepRun | Spuštění pro krok kanálu, který znovu používá předchozí běh. |

## <a name="see-also"></a>Viz také

- Popis Azure Machine Learning monitorování najdete v tématu [monitorování Azure Machine Learning](monitor-azure-machine-learning.md) .
- Podrobnosti o monitorování prostředků Azure najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](/azure/azure-monitor/insights/monitor-azure-resource) .
