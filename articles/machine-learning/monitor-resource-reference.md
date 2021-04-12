---
title: Referenční informace o Azure Machine Learning dat monitorování | Microsoft Docs
description: Referenční dokumentace k monitorování Azure Machine Learning. Seznamte se s daty & prostředky shromážděnými a dostupnými v Azure Monitor.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.custom: subject-monitoring
ms.date: 04/07/2021
ms.openlocfilehash: de4d934144d6721db8c00d7199061842e518e44f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "107031065"
---
# <a name="monitoring-azure-machine-learning-data-reference"></a>Referenční informace k datům monitorování Azure Machine Learning

Seznamte se s daty a prostředky shromažďovanými nástrojem Azure Monitor z pracovního prostoru Azure Machine Learning. Podrobnosti o shromažďování a analýze dat monitorování najdete v tématu [monitorování Azure Machine Learning](monitor-azure-machine-learning.md) .

## <a name="metrics"></a>Metriky

V této části jsou uvedené všechny automaticky shromážděné metriky platforem pro Azure Machine Learning. Poskytovatel prostředků pro tyto metriky je [Microsoft. MachineLearningServices/Workspaces](../azure-monitor/essentials/metrics-supported.md#microsoftmachinelearningservicesworkspaces).

**Modelování**

| Metric | Jednotka | Description |
|--|--|--|
| Registr modelu byl úspěšný. | Počet | Počet registrací modelů, které byly v tomto pracovním prostoru úspěšně dokončeny |
| Nepovedlo se zaregistrovat model | Počet | Počet registrací modelů, které se v tomto pracovním prostoru nezdařily |
| Nasazení modelu spuštěn | Počet | Počet nasazení modelů spuštěných v tomto pracovním prostoru |
| Nasazení modelu bylo úspěšné. | Počet | Počet nasazení modelů, která byla v tomto pracovním prostoru úspěšná |
| Nasazení modelu se nezdařilo | Počet | Počet nasazení modelů, která v tomto pracovním prostoru selhala |

**Kvóta**

Informace o kvótách jsou jenom Azure Machine Learning výpočetní služby.

| Metric | Jednotka | Description |
|--|--|--|
| Celkem uzlů | Počet | Počet uzlů celkem Tento součet zahrnuje některé z aktivních uzlů, nečinných uzlů, nepoužitelných uzlů, přerušených uzlů a opouští uzly. |
| Aktivní uzly | Počet | Počet aktivních uzlů. Uzly, které aktivně spouštějí úlohu. |
| Nečinné uzly | Počet | Počet nečinných uzlů. Nečinné uzly jsou uzly, které nespouštějí žádné úlohy, ale mohou přijmout novou úlohu, je-li k dispozici. |
| Nepoužité uzly | Počet | Počet nepoužitelných uzlů. Nepoužité uzly nejsou funkční kvůli nějakému problému s nepřeložitelné. Azure bude tyto uzly recyklovat. |
| Přepnuté uzly | Počet | Počet zrušených uzlů. Tyto uzly jsou uzly s nízkou prioritou, které jsou mimo dostupný fond uzlů. |
| Ukončení uzlů | Počet | Počet opouštících uzlů. Ukončení uzlů jsou uzly, které právě dokončily zpracování úlohy a přestanou do stavu nečinnosti. |
| Celkový počet jader | Počet | Počet jader celkem |
| Aktivní jádra | Počet | Počet aktivních jader |
| Jádra nečinných | Počet | Počet nečinných jader |
| Nepoužitelné jádra | Počet | Počet nepoužitelných jader |
| Zrušené jádra | Počet | Počet zrušených jader |
| Vynechávání jader | Počet | Počet opouštících jader |
| Procento využití kvóty | Počet | Procento využité kvóty |

**Prostředek**

| Metric| Jednotka | Description |
|--|--|--|
| CpuUtilization | Počet | Procento využití na uzlu procesoru Využití je nahlášeno v intervalu 1 minuty. |
| GpuUtilization | Počet | Procento využití v uzlu GPU Využití je nahlášeno v intervalu 1 minuty. |
| GpuMemoryUtilization | Počet | Procento využití paměti v uzlu GPU Využití je nahlášeno v intervalu 1 minuty. |
| GpuEnergyJoules | Počet | Rozsah energie v Joules v uzlu GPU Energie se oznamuje v intervalu jedné minuty. |

**Spustit**

Informace o spuštění školení pro pracovní prostor.

| Metric | Jednotka | Description |
|--|--|--|
| Zrušená spuštění | Počet | Počet zrušených spuštění pro tento pracovní prostor Počet se aktualizuje po úspěšném zrušení běhu. |
| Zrušit požadovaná spuštění | Počet | Počet spuštění, kde se pro tento pracovní prostor požádalo o zrušení Počet se aktualizuje po přijetí žádosti o zrušení pro spuštění. |
| Dokončená spuštění | Počet | Počet spuštěných běhů pro tento pracovní prostor byl úspěšně dokončen. Počet se aktualizuje po dokončení běhu a shromažďování výstupu. |
| Neúspěšná spuštění | Počet | Počet spuštění pro tento pracovní prostor se nezdařil. Počet se aktualizuje, když se spuštění nezdařilo. |
| Dokončují se běhy. | Počet | Počet spuštění, které vstoupily do stavu dokončení pro tento pracovní prostor. Počet se aktualizuje po dokončení běhu, ale shromažďování výstupu ještě probíhá. | 
| Neodpovídá spuštění | Počet | Počet běhů nereagujících na tento pracovní prostor. Počet se aktualizuje, když běh vstoupí do stavu neodpovídá. |
| Nespuštěná spuštění | Počet | Počet spuštění v neaktivním stavu pro tento pracovní prostor Počet se aktualizuje, když se přijme požadavek na vytvoření běhu, ale informace o spuštění ještě nejsou naplněné. |
| Připravují se běhy. | Počet | Počet běhů, které se připravují pro tento pracovní prostor. Počet se aktualizuje, když spuštění vstoupí do stavu Příprava během přípravy prostředí pro spuštění. |
| Spuštění zřizování | Počet | Počet spuštění, která jsou zajišťována pro tento pracovní prostor. Počet se aktualizuje, když běh čeká na vytvoření nebo zřizování cíle výpočetní funkce. |
| Spuštění ve frontě | Počet | Počet spuštění, které jsou zařazeny do fronty pro tento pracovní prostor. Počet se aktualizuje při zařazení běhu do fronty v cíli výpočtu. Může nastat při čekání na přípravu požadovaných výpočetních uzlů. |
| Spuštěná spuštění | Počet | Počet spuštěných běhů pro tento pracovní prostor. Počet se aktualizuje, když se spuštění spustí na požadovaných prostředcích. |
| Spouštění spuštění | Počet | Počet spuštění spuštěných pro tento pracovní prostor. Počet se aktualizuje po žádosti o vytvoření informací o spuštění a spuštění, jako je například ID běhu. |
| Chyby | Počet | Počet chyb spuštění v tomto pracovním prostoru. Počet se aktualizuje pokaždé, když při spuštění dojde k chybě. |
| Upozornění | Počet | Počet upozornění spuštění v tomto pracovním prostoru. Počet se aktualizuje pokaždé, když při spuštění dojde k upozornění. |

## <a name="metric-dimensions"></a>Dimenze metriky

Další informace o tom, jaké dimenze metriky jsou, najdete v tématu multidimenzionální [metriky](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics).

Azure Machine Learning má k jeho metrikám přidružené následující dimenze.

| Dimenze | Description |
| ---- | ---- |
| Název clusteru | Název prostředku Compute Cluster. K dispozici pro všechny metriky kvót. |
| Název rodiny virtuálních počítačů | Název rodiny virtuálních počítačů, kterou cluster používá. K dispozici pro procento využití kvóty. |
| Priorita virtuálního počítače | Priorita virtuálního počítače. K dispozici pro procento využití kvóty.
| CreatedTime | K dispozici pouze pro CpuUtilization a GpuUtilization. |
| DeviceId | ID zařízení (GPU) K dispozici pouze pro GpuUtilization. |
| NodeId | ID uzlu, který byl vytvořen v případě běhu úlohy. K dispozici pouze pro CpuUtilization a GpuUtilization. |
| RunId | ID běhu/úlohy K dispozici pouze pro CpuUtilization a GpuUtilization. |
| ComputeType | Typ výpočtu, který se používá. K dispozici pouze pro dokončená spuštění, neúspěšná spuštění a spuštěná spuštění. |
| PipelineStepType | Typ [PipelineStep](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep) použitý v běhu. K dispozici pouze pro dokončená spuštění, neúspěšná spuštění a spuštěná spuštění. |
| PublishedPipelineId | ID publikovaného kanálu používaného v běhu K dispozici pouze pro dokončená spuštění, neúspěšná spuštění a spuštěná spuštění. |
| RunType | Typ běhu. K dispozici pouze pro dokončená spuštění, neúspěšná spuštění a spuštěná spuštění. |

Platné hodnoty pro dimenzi RunType jsou:

| Hodnota | Popis |
| ----- | ----- |
| Experiment | Spuštění bez kanálu. |
| PipelineRun | Spuštění kanálu, které je nadřazeným objektem StepRun. |
| StepRun | Spuštění pro krok kanálu. |
| ReusedStepRun | Spuštění pro krok kanálu, který znovu používá předchozí běh. |

## <a name="activity-log"></a>Protokol aktivit

V následující tabulce jsou uvedeny operace týkající se Azure Machine Learning, které mohou být vytvořeny v protokolu aktivit.

| Operace | Description |
|:---|:---|
| Vytvoří nebo aktualizuje pracovní prostor Machine Learning. | Pracovní prostor se vytvořil nebo aktualizoval. |
| CheckComputeNameAvailability | Zkontroluje, jestli se tento výpočetní název už používá. |
| Vytvoří nebo aktualizuje výpočetní prostředky. | Výpočetní prostředek se vytvořil nebo aktualizoval. |
| Odstraní výpočetní prostředky. | Výpočetní prostředek se odstranil. |
| Výpis tajných kódů | Při operaci s uvedenými tajnými klíči pro Machine Learning pracovní prostor |

## <a name="resource-logs"></a>Protokoly prostředků

V této části jsou uvedené typy protokolů prostředků, které můžete shromažďovat pro Azure Machine Learning pracovní prostor.

Poskytovatel prostředků a typ: [Microsoft. MachineLearningServices/Workspace](../azure-monitor/essentials/resource-logs-categories.md#microsoftmachinelearningservicesworkspaces).

| Kategorie | Zobrazovaný název |
| ----- | ----- |
| AmlComputeClusterEvent | AmlComputeClusterEvent |
| AmlComputeClusterNodeEvent | AmlComputeClusterNodeEvent |
| AmlComputeCpuGpuUtilization | AmlComputeCpuGpuUtilization |
| AmlComputeJobEvent | AmlComputeJobEvent |
| AmlRunStatusChangedEvent | AmlRunStatusChangedEvent |

## <a name="schemas"></a>Schémata

Následující schémata používá Azure Machine Learning

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
| Typ události | Typ události úlohy Například JobSubmitted, JobRunning, JobFailed, JobSucceeded. |
| ExecutionState | Stav úlohy (spuštění). Například ve frontě, spuštění, úspěšné, neúspěšné |
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
| Publisher | Vydavatel image virtuálního počítače Například Microsoft-dsvm |
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


## <a name="see-also"></a>Viz také

- Popis Azure Machine Learning monitorování najdete v tématu [monitorování Azure Machine Learning](monitor-azure-machine-learning.md) .
- Podrobnosti o monitorování prostředků Azure najdete v tématu [monitorování prostředků Azure pomocí Azure monitor](../azure-monitor/essentials/monitor-azure-resource.md) .
