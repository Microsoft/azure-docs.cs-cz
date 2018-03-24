---
title: V Azure Data Factory vytvořit aktivační události přeskakující okno | Microsoft Docs
description: Naučte se vytvořit aktivační událost v Azure Data Factory, která běží na přeskakující okno kanálu.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2018
ms.author: shlo
ms.openlocfilehash: 312072a5de21ff1c6b602fed93b77c564b15a9f1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Vytvořit aktivační událost, která běží na přeskakující okno kanálu
Tento článek obsahuje kroky k vytvoření, spuštění a monitorování aktivační událost přeskakující okno. Obecné informace o aktivační události a podporované typy najdete v tématu [kanálu spouštěcí a aktivační události](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Tento článek se týká služby Azure Data Factory verze 2, která je aktuálně ve verzi Preview. Pokud používáte Azure Data Factory verze 1, který je všeobecně dostupná (GA), najdete v části [Začínáme s Azure Data Factory verze 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Aktivační události pro přeskakující okno jsou typem aktivačních událostí, které se aktivuje v pravidelných časových intervalech od určeného počátečního okamžiku a které zachovávají stav. Přeskakující okna jsou řada nepřekrývajících se souvislých časových intervalů s pevnou velikostí. Přeskakující okno aktivační událost je v relaci 1: 1 se zřetězením příkazů a může odkazovat pouze na singulární kanálu.

## <a name="tumbling-window-trigger-type-properties"></a>Přeskakujícího okna vlastnosti typ aktivační události
Přeskakující okno má následující vlastnosti typ aktivační události:

```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": "<<Minute/Hour>>",
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: "<<datetime – optional>>"",
            "delay": "<<timespan – optional>>",
            “maxConcurrency”: <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count":  <<int - optional, default: 0>>,
                “intervalInSeconds”: <<int>>,
            }
        },
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "parameter1": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter2": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "parameter3": "https://mydemo.azurewebsites.net/api/demoapi"
                }
            }
      }    
}
```  

Následující tabulka obsahuje přehled hlavních prvků JSON, které se vztahují k opakování a plánování aktivační události přeskakující okno:

| Element JSON | Popis | Typ | Povolené hodnoty | Požaduje se |
|:--- |:--- |:--- |:--- |:--- |
| **Typ** | Typ aktivační události. Typ je pevná hodnota "TumblingWindowTrigger." | Řetězec | "TumblingWindowTrigger" | Ano |
| **runtimeState** | Aktuální stav, čas spuštění aktivační události.<br/>**Poznámka:**: Tento element má \<jen pro čtení >. | Řetězec | "Spustit", "Stopped," "Zakázat" | Ano |
| **frequency** | Řetězec, který představuje jednotku frekvenci (minut nebo hodin), kdy dojde znovu aktivační událost. Pokud **startTime** hodnoty data jsou podrobnější než **frekvence** hodnota, **startTime** data jsou považovány za, když se vypočítávají v hranicích okno. Například pokud **frekvence** hodnota je každou hodinu a **startTime** hodnota je 2016-04-01T10:10:10Z, je první okno (2017-09-01T10:10:10Z 2017-09-01T11:10:10Z). | Řetězec | "minut", "hodina"  | Ano |
| **interval** | Kladné celé číslo označující interval pro hodnotu **frequency**, která určuje, jak často se má aktivační událost spouštět. Například pokud **interval** 3 a **frekvence** je "hodina," aktivační událost opakovat každých 3 hodiny. | Integer | Kladné celé číslo. | Ano |
| **startTime**| První výskyt, který může být v minulosti. Je první interval aktivační události (**startTime**, **startTime** + **interval**). | DateTime | Hodnota, datum a čas. | Ano |
| **endTime**| Poslední výskyt, který může být v minulosti. | DateTime | Hodnota, datum a čas. | Ano |
| **delay** | Množství času zpoždění spuštění zpracování dat pro okno. Spustí kanálu spustit po je očekávaná doba provádění, a navíc **zpoždění**. **Zpoždění** definuje, jak dlouho má aktivační procedura čeká po splatnosti čase před aktivací nového spustit. **Zpoždění** nemění okno **startTime**. Například **zpoždění** hodnota 00:10:00 znamená zpoždění 10 minut. | Časový interval  | Hodnota času, kde výchozí hodnota je 00:00:00. | Ne |
| **maxConcurrency** | Počet souběžných aktivační událost spustí, které se aktivuje například pro windows, které jsou připravené. Například zálohování výplně každou hodinu pro včerejšek za následek 24 windows běží. Pokud **maxConcurrency** = 10, aktivační události při vyvolání pouze pro prvních 10 windows (00:00-01:00 - 09:00 – 10:00). Po dokončení prvních 10 běží spouštěná kanálu se spustí aktivační událost při vyvolání pro další 10 windows (10:00 – 11:00 – 19:00 až 20:00). Pokračujte v tomto příkladu z **maxConcurrency** = 10, pokud existují 10 windows připravený, existují 10 běží celkový kanálu. Pokud je pouze 1 okno připraven, je pouze 1 spuštění kanálu. | Integer | Celé číslo mezi 1 až 50 znaků. | Ano |
| **retryPolicy: počet** | Počet opakovaných pokusů před spuštěním kanálu je označena jako "Se nezdařilo."  | Integer | Celé číslo, kde výchozí hodnota je 0 (bez opakování). | Ne |
| **retryPolicy: intervalInSeconds** | Prodleva mezi pokusy o opakování zadávají v sekundách. | Integer | Počet sekund, kde výchozí hodnota je 30. | Ne |

### <a name="windowstart-and-windowend-system-variables"></a>WindowStart a WindowEnd systémové proměnné

Můžete použít **WindowStart** a **WindowEnd** systémové proměnné aktivační události přeskakující okno ve vaší **kanálu** definice (tedy pro část dotazu). Systémové proměnné předat jako parametry do kanálu v **aktivační událost** definice. Následující příklad ukazuje, jak tyto proměnné předat jako parametry:

```  
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline":
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "MyPipelineName"
                },
                "parameters": {
                    "MyWindowStart": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowStartTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    },
                    "MyWindowEnd": {
                        "type": "Expression",
                        "value": "@{concat('output',formatDateTime(trigger().outputs.windowEndTime,'-dd-MM-yyyy-HH-mm-ss-ffff'))}"
                    }
                }
            }
      }    
}
```  

Použít **WindowStart** a **WindowEnd** hodnoty proměnných systému v definici kanálu používat vaše parametry "MyWindowStart" a "MyWindowEnd" odpovídajícím způsobem.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Pořadí spuštění systému windows v případě obnovení dat
Po několika windows ke spuštění (zejména v případě obnovení dat) z nejstarší nejnovější intervalech je deterministická, pořadí zpracování pro systém windows. V současné době nelze toto chování změnit.

### <a name="existing-triggerresource-elements"></a>Existující TriggerResource elementy
Toto platí pro existující **TriggerResource** prvky:

* Pokud hodnota **frekvence** elementu (nebo velikost okna) aktivační události změny stavu systému windows, které jsou již zpracovány je *není* resetovat. Aktivační událost dál platit pro windows z poslední okno, které se provedla pomocí nové velikost okna.
* Pokud hodnota **endTime** element aktivační událost změny (přidán nebo aktualizován), stav systému windows, které jsou již zpracována je *není* resetovat. Aktivační událost ctí nové **endTime** hodnotu. Pokud nové **endTime** hodnota je před windows, které jsou spouštěny již, zastaví aktivační události. Jinak, aktivační události zastaví, když nové **endTime** zjištěna hodnota.

## <a name="sample-for-azure-powershell"></a>Ukázka pro prostředí Azure PowerShell
V této části se dozvíte, jak pomocí prostředí Azure PowerShell k vytvoření, spuštění a monitorování aktivační událost.

1. Vytvořte soubor JSON s názvem **MyTrigger.json** ve složce C:\ADFv2QuickStartPSH\ s následujícím obsahem:

   > [!IMPORTANT]
   > Před uložením souboru JSON, nastavte hodnotu **startTime** element na aktuální čas UTC. Nastavte hodnotu **endTime** element na jednu hodinu po aktuálním čase UTC.

    ```json   
    {
      "name": "PerfTWTrigger",
      "properties": {
        "type": "TumblingWindowTrigger",
        "typeProperties": {
          "frequency": "Minute",
          "interval": "15",
          "startTime": "2017-09-08T05:30:00Z",
          "delay": "00:00:01",
          "retryPolicy": {
            "count": 2,
            "intervalInSeconds": 30
          },
          "maxConcurrency": 50
        },
        "pipeline": {
          "pipelineReference": {
            "type": "PipelineReference",
            "referenceName": "DynamicsToBlobPerfPipeline"
          },
          "parameters": {
            "windowStart": "@trigger().outputs.windowStartTime",
            "windowEnd": "@trigger().outputs.windowEndTime"
          }
        },
        "runtimeState": "Started"
      }
    }
    ```  

2. Vytvořit aktivační událost pomocí **Set-AzureRmDataFactoryV2Trigger** rutiny:

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Zkontrolujte, zda je stav aktivační události **Zastaveno** pomocí **Get-AzureRmDataFactoryV2Trigger** rutiny:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Spusťte aktivační událost pomocí **Start-AzureRmDataFactoryV2Trigger** rutiny:

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Zkontrolujte, zda je stav aktivační události **Začínáme** pomocí **Get-AzureRmDataFactoryV2Trigger** rutiny:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Aktivační událost se spustí v prostředí Azure PowerShell Get **Get-AzureRmDataFactoryV2TriggerRun** rutiny. Chcete-li získat informace o spuštění aktivační události, spusťte následující příkaz pravidelně. Aktualizace **TriggerRunStartedAfter** a **TriggerRunStartedBefore** hodnoty k hodnotám ve vaší definice aktivační události:

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Monitorování spustí aktivační události a kanálu se spouští v portálu Azure najdete v tématu [spouští monitorování kanálu](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Další postup
Podrobné informace o aktivační události najdete v tématu [kanálu spouštěcí a aktivační události](concepts-pipeline-execution-triggers.md#triggers).
