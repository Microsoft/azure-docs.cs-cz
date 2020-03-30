---
title: Odkaz na údaje z monitorování | Dokumenty společnosti Microsoft
titleSuffix: Azure Machine Learning
description: Přečtěte si o datech a prostředcích shromážděných pro Azure Machine Learning a dostupných ve službě Azure Monitor. Azure Monitor shromažďuje a zobrazuje data o pracovním prostoru Azure Machine Learning a umožňuje zobrazit metriky, nastavit výstrahy a analyzovat protokolovaná data.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 03/06/2020
ms.openlocfilehash: 958794cda60d0ce1b0d223b9b5a6c03283022a6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78927562"
---
# <a name="azure-machine-learning-monitoring-data-reference"></a>Referenční informace o datech monitorování azure strojového učení

Seznamte se s daty a prostředky shromážděnými službou Azure Monitor z pracovního prostoru Azure Machine Learning. Podrobnosti o shromažďování a analýze dat monitorování najdete v [tématu Monitoring Azure Machine Learning.](monitor-azure-machine-learning.md)

## <a name="resource-logs"></a>Protokoly prostředků

V následující tabulce jsou uvedeny vlastnosti protokolů prostředků Azure Machine Learning, když se shromažďují v protokolech monitorování Azure nebo azure storage.

### <a name="amlcomputejobevents-table"></a>Tabulka AmlComputeJobEvents

| Vlastnost | Popis |
|:--- |:---|
| TimeGenerated | Čas, kdy byla generována položka protokolu |
| OperationName | Název operace přidružené k události protokolu |
| Kategorie | Název události protokolu, AmlComputeClusterNodeEvent |
| JobId | ID odeslané úlohy |
| Id experimentu | ID experimentu |
| Název experimentu | Název experimentu |
| Id odběru zákazníka | SubscriptionId, kde experiment a úloha jako odeslána |
| Název pracovního__ | Název pracovního prostoru strojového učení |
| Název clusteru | Název clusteru |
| ZřizováníStát | Stav odeslání úlohy |
| ResourceGroupName | Název skupiny prostředků |
| Název_pracovní pozice | Název úlohy |
| ClusterId | ID clusteru |
| Typ události | Typ události Job, např. |
| ExecutionState | Stav úlohy (spustit), například ve frontě, spuštěno, úspěšné, neúspěšné |
| ErrorDetails | Podrobnosti o chybě úlohy |
| Verze CreationApiVersion | Verze rozhraní API použitá k vytvoření úlohy |
| Název_clusteruResourceGroupName | Název skupiny prostředků clusteru |
| TFWorkerCount | Počet pracovníků TF |
| TFParameterServerCount | Počet parametrového serveru TF |
| Typ nástroje | Typ použitého nástroje |
| RunInContainer | Příznak popisující, pokud by úloha měla být spuštěna uvnitř kontejneru |
| Zpráva joberror | podrobná zpráva o chybě úlohy |
| NodeId | ID vytvořeného uzlu, kde je spuštěna úloha |

### <a name="amlcomputeclusterevents-table"></a>Tabulka AmlComputeClusterEvents

| Vlastnost | Popis |
|:--- |:--- |
| TimeGenerated | Čas, kdy byla generována položka protokolu |
| OperationName | Název operace přidružené k události protokolu |
| Kategorie | Název události protokolu, AmlComputeClusterNodeEvent |
| ZřizováníStát | Zřizování stavu clusteru |
| Název clusteru | Název clusteru |
| Typ clusteru | Typ clusteru |
| Vytvořilby | Uživatel, který vytvořil cluster |
| Počet corecount | Počet jader v clusteru |
| Velikost vm | Velikost virtuálního počítače clusteru |
| Priorita Vm | Priorita uzlů vytvořených v clusteru Dedicated/LowPriority |
| Změna velikostiTyp | Typ ručního měřítka clusteru/automaticky |
| InitialNodeCount | Počáteční počet uzlů clusteru |
| MinimumNodeCount | Minimální počet uzlů clusteru |
| MaximumNodeCount | Maximální počet uzlů clusteru |
| NodeDeallocationOption | Jak by měl být uzel rozmísťován |
| Vydavatel | Vydavatel typu clusteru |
| Nabídka | Nabídka, se kterou je cluster vytvořen |
| Skladová jednotka (SKU) | Sku uzlu/virtuálního virtuálního mísa vytvořeného uvnitř clusteru |
| Version | Verze bitové kopie použitá při vytvoření uzlu a virtuálního virtuálního mě |
| PodsíťId | PodsíťId clusteru |
| AllocationState | Stav přidělení clusteru |
| CurrentNodeCount | Počet aktuálních uzlů clusteru |
| Počet cílových nodecount | Počet cílových uzlů clusteru při škálování nahoru/dolů |
| Typ události | Typ události během vytváření clusteru. |
| NodeIdleTimeSecondsBeforeScaleDown | Doba nečinnosti v sekundách před zmenšením clusteru |
| PreemptedNodeCount | Počet předpočtů uzlů clusteru |
| IsResizeGrow | Příznak označující, že cluster se zvětšuje |
| Název vmFamily | Název řady virtuálních mandů uzlů, které lze vytvořit uvnitř clusteru |
| LeavingNodeCount | Ponechání počtu uzlů clusteru |
| UnusableNodeCount | Počet nepoužitelných uzlů clusteru |
| IdleNodeCount | Počet nečinných uzlů clusteru |
| RunningNodeCount | Spuštění počtu uzlů clusteru |
| PreparingNodeCount | Příprava počtu uzlů clusteru |
| Přidělená kvóta | Přidělená kvóta clusteru |
| Využitá kvóta | Využitá kvóta clusteru |
| AllocationStateTransitionTime | Doba přechodu z jednoho stavu do druhého |
| Kódy clustererrorcodes | Kód chyby přijatý při vytváření nebo škálování clusteru |
| Verze CreationApiVersion | Verze rozhraní API použitá při vytváření clusteru |

### <a name="amlcomputeclusternodeevents-table"></a>Tabulka AmlComputeClusterNodeEvents

| Vlastnost | Popis |
|:--- |:--- |
| TimeGenerated | Čas, kdy byla generována položka protokolu |
| OperationName | Název operace přidružené k události protokolu |
| Kategorie | Název události protokolu, AmlComputeClusterNodeEvent |
| Název clusteru | Název clusteru |
| NodeId | ID vytvořeného uzlu clusteru |
| Velikost vm | Velikost virtuálního počítače uzlu |
| Název vmFamily | Rodina Vm, ke kterému uzel patří |
| Priorita Vm | Priorita vytvořeného uzlu Dedicated/LowPriority |
| Vydavatel | Vydavatel obrázku virtuálního videa, například Microsoft-DSVM |
| Nabídka | Nabídka přidružená k vytvoření virtuálního virtuálního mísy |
| Skladová jednotka (SKU) | Vytvořenou položku Sku uzlu/virtuálního mísy |
| Version | Verze bitové kopie použitá při vytvoření uzlu a virtuálního virtuálního mě |
| ClusterCreationTime | Čas vytvoření clusteru |
| Změna velikostiČastime | Čas, kdy byl spuštěn vertikálně navýšit/snížit kapacitu clusteru |
| Změna velikostiEndTime | Čas, kdy bylo ukončeno škálování clusteru nahoru/dolů |
| NodeAllocationTime | Čas přidělení uzlu |
| NodeBootTime | Čas, kdy byl uzel spuštěn |
| StartTaskStartTime | Čas, kdy byl úkol přiřazen k uzlu a spuštěn |
| Čas spuštění úlohy | Čas ukončení úkolu přiřazeného k uzlu |
| TotalE2EtimeInSeconds | Celkový časový uzel byl aktivní. |

### <a name="metrics"></a>Metriky

V následujících tabulkách jsou uvedeny metriky platformy shromážděné pro Azure Machine Learning Všechny metriky jsou uloženy v oboru názvů **Azure Machine Learning Workspace**.

**Model**

| Metrika | Jednotka | Popis |
| ----- | ----- | ----- |
| Nasazení modelu se nezdařilo. | Počet | Počet nasazení modelu, které se nezdařilo. |
| Zahájení nasazení modelu | Počet | Počet spuštění nasazení modelu. |
| Nasazení modelu bylo úspěšné. | Počet | Počet úspěšných nasazení modelu. |
| Registr modelů se nezdařil. | Počet | Počet registrací modelu, které se nezdařily. |
| Registr modelů byl úspěšný. | Počet | Počet registrací modelu, které byly úspěšné. |

**Kvóta**

Informace o kvótě je určen y pro výpočetní prostředky Azure Machine Learning.

| Metrika | Jednotka | Popis |
| ----- | ----- | ----- |
| Aktivní jádra | Počet | Počet aktivních výpočetních jader. |
| Aktivní uzly | Počet | Počet aktivních uzlů. |
| Nečinná jádra | Počet | Počet nečinných výpočetních jader. |
| Nečinné uzly | Počet | Počet nečinných výpočetních uzlů. |
| Opuštění jader | Počet | Počet opouštějících jader. |
| Opuštění uzlů | Počet | Počet opouštějících uzlů. |
| Předpjatojádra | Počet | Počet preempted jader. |
| Předpisované uzly | Počet | Počet předplyňovaných uzlů. |
| Procento využití kvóty | Procento | Procento použité kvóty. |
| Celkem jader | Počet | Celková jádra. |
| Uzly celkem | Počet | Celkový počet uzlů. |
| Nepoužitelná jádra | Počet | Počet nepoužitelných jader. |
| Nepoužitelné uzly | Počet | Počet nepoužitelných uzlů. |

Níže jsou uvedeny dimenze, které lze použít k filtrování metrik kvót:

| Dimenze | Metriky, které jsou k dispozici | Popis |
| ---- | ---- | ---- |
| Název clusteru | Všechny metriky kvót | Název instance výpočetní ch od posuzte. |
| Název rodiny virtuálních vod | Procento využití kvóty | Název rodiny virtuálních mitů používaných clusterem. |
| Priorita virtuálního virtuálního mispozi | Procento využití kvóty | Priorita virtuálního soudu.

**Spustit**

Informace o tréninkových jízdách.

| Metrika | Jednotka | Popis |
| ----- | ----- | ----- |
| Dokončené spuštění | Počet | Počet dokončených spuštění. |
| Selhání spuštění | Počet | Počet neúspěšných spuštění. |
| Spuštěné spuštění | Počet | Počet spuštění. |

Níže jsou uvedeny dimenze, které lze použít k filtrování metriky spuštění:

| Dimenze | Popis |
| ---- | ---- |
| ComputeType | Typ výpočetního prostředí, který používá spuštění. |
| Type PipelineStepType | Typ [PipelineStep](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelinestep?view=azure-ml-py) používá v běhu. |
| PublikovánoPipelineId | ID publikovaného kanálu použitého při spuštění. |
| Typ běhu | Typ spuštění. |

Platné hodnoty dimenze RunType jsou:

| Hodnota | Popis |
| ----- | ----- |
| Experiment | Bez kanálu běží. |
| PipelineRun | Spuštění kanálu, který je nadřazenou StepRun. |
| Spustit krok | Spuštění kroku kanálu. |
| Znovu použité steprun | Spuštění kroku kanálu, který opakovaně používá předchozí spuštění. |

## <a name="see-also"></a>Viz také

- Popis monitorování Azure Machine Learning najdete v tématu [Monitoring Azure Machine Learning.](monitor-azure-machine-learning.md)
- Podrobnosti o monitorování prostředků Azure najdete v tématu [Sledování prostředků Azure pomocí Azure Monitoru.](/azure/azure-monitor/insights/monitor-azure-resource)
