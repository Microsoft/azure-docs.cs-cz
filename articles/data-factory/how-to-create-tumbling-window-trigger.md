---
title: Vytvořit aktivační událost pro přeskakující okno aktivačních událostí ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak ve službě Azure Data Factory, který spouští kanál na aktivační událost pro přeskakující okno Vytvoření aktivační události.
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/27/2018
ms.author: shlo
ms.openlocfilehash: 1a24079292ce8fdd6a514a85484fc10b77491ba6
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868324"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Vytvoření aktivační události, který spouští kanál na aktivační událost pro přeskakující okno
Tento článek popisuje kroky k vytvoření, spuštění a monitorování přeskakující okno. Obecné informace o aktivačních událostech a podporovaných typů najdete v tématu [spouštění kanálů a triggery](concepts-pipeline-execution-triggers.md).

Aktivační události pro přeskakující okno jsou typem aktivačních událostí, které se aktivuje v pravidelných časových intervalech od určeného počátečního okamžiku a které zachovávají stav. Přeskakující okna jsou řada nepřekrývajících se souvislých časových intervalů s pevnou velikostí. Přeskakující okno má relaci s kanálem a mohou odkazovat pouze na jednotný kanálu.

## <a name="data-factory-ui"></a>Uživatelské rozhraní Data Factory

Chcete-li vytvořit přeskakující okno na webu Azure Portal, vyberte **aktivační událost > aktivační událost pro Přeskakující okno > Další**a potom nakonfigurujte vlastnosti, které definují aktivační událost pro přeskakující okno.

![Vytvoření aktivační události aktivační událost pro přeskakující okno na webu Azure Portal](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Přeskakujícího okna vlastnosti typu triggeru
Aktivační událost pro přeskakující okno má následující vlastnosti typ aktivační události:

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

Následující tabulka obsahuje přehled hlavních elementů JSON, které jsou souvisejících s opakováním a plánováním aktivační události aktivační událost pro přeskakující okno:

| Element JSON | Popis | Typ | Povolené hodnoty | Požaduje se |
|:--- |:--- |:--- |:--- |:--- |
| **type** | Typ aktivační události. Typ je pevná hodnota "TumblingWindowTrigger." | Řetězec | "TumblingWindowTrigger" | Ano |
| **runtimeState** | Aktuální stav čas spuštění aktivační události.<br/>**Poznámka:**: Tento element má \<jen pro čtení >. | Řetězec | "Spustit", "zastavena," "Zakázáno" | Ano |
| **frequency** | Řetězec, který představuje jednotku frekvence (minuty nebo hodiny), ve kterém se aktivační událost opakuje. Pokud **startTime** hodnot data jsou podrobnější než **frekvence** hodnota, **startTime** data jsou považovány za, kdy se zpracovávají hranice okna. Například pokud **frekvence** hodnota je po hodinách a **startTime** hodnotu 2017-09 – 01T10:10:10Z, je první okno (2017-09 – 01T10:10:10Z 2017-09 – 01T11:10:10Z). | Řetězec | "minute", "hour"  | Ano |
| **interval** | Kladné celé číslo označující interval pro hodnotu **frequency**, která určuje, jak často se má aktivační událost spouštět. Například pokud **interval** 3 a **frekvence** je "hodina", aktivační událost se opakuje každé 3 hodiny. | Integer | Kladné celé číslo. | Ano |
| **startTime**| První výskyt, což může být v minulosti. První interval aktivační událost (**startTime**, **startTime** + **interval**). | DateTime | Hodnota data a času. | Ano |
| **endTime**| Poslední výskyt, což může být v minulosti. | DateTime | Hodnota data a času. | Ano |
| **delay** | Množství času zpoždění spuštění zpracování dat pro okno. Spuštění kanálu je spuštěna za očekávanou dobu spuštění plus velikost **zpoždění**. **Zpoždění** definuje, jak dlouho čekat aktivační událost po vypršení platnosti čase před aktivací nové spuštění. **Zpoždění** nemění v okně **startTime**. Například **zpoždění** hodnotu 00:10:00 znamená trvat 10 minut. | Časový interval  | Čas, kdy výchozí hodnota je 00:00:00. | Ne |
| **maxConcurrency** | Počet spuštění souběžných aktivační události, které se aktivuje například pro windows, které jsou připravené. Například výplň pozadí každou hodinu spouštění pro výsledky včera v systému windows 24. Pokud **maxConcurrency** = 10, aktivační události jsou vyvolávány jen u prvních 10 windows (00:00-01:00 - 09:00-10:00). Po dokončení prvních 10 aktivovaných spuštění kanálu se spuštění aktivační události se aktivuje například pro dalších 10 systému windows (10:00-11:00 – 19:00 – 20:00). Pokračujte v tomto příkladu z **maxConcurrency** = 10, pokud existují 10 windows budete mít, existují 10 spuštění celkový kanálu. Pokud je pouze 1 okno připravený, je pouze 1 spuštění kanálu. | Integer | Celé číslo mezi 1 až 50 znaků. | Ano |
| **retryPolicy: počet** | Počet opakování před spuštění kanálu je označena jako "Se nezdařilo."  | Integer | Celé číslo, kde výchozí hodnota je 0 (žádná opakování). | Ne |
| **retryPolicy: intervalInSeconds** | Zpoždění mezi opakovanými pokusy zadávají v sekundách. | Integer | Počet sekund, kde výchozí hodnota je 30. | Ne |

### <a name="windowstart-and-windowend-system-variables"></a>WindowStart a WindowEnd systémové proměnné

Můžete použít **WindowStart** a **WindowEnd** systémové proměnné přeskakující okno v vaše **kanálu** definice (to znamená pro součást dotazu). Předat do kanálu v systémové proměnné jako parametry **aktivační událost** definice. Následující příklad ukazuje, jak tyto proměnné předat jako parametry:

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

Použít **WindowStart** a **WindowEnd** hodnoty proměnných systému v definici kanálu používat parametry "MyWindowStart" a "MyWindowEnd", odpovídajícím způsobem.

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Pořadí zpracování systému windows ve scénáři obnovení dat
Pokud existuje více oken pro spuštění (zejména v případě obnovení dat), je z intervalu od nejstarších k nejnovějším deterministická, pořadí zpracování pro windows. V současné době nelze toto chování změnit.

### <a name="existing-triggerresource-elements"></a>Stávající elementy TriggerResource
Platí následující body k existujícím **TriggerResource** prvky:

* Pokud hodnota **frekvence** elementu (nebo velikost okna) aktivační události změny stavu systému windows, které již zpracovává je *není* resetovat. Aktivační událost i nadále platit pro windows z posledního okna, který je spuštěn s použitím nové velikosti okna.
* Pokud hodnota **endTime** prvek aktivační událost změny (Přidání nebo aktualizaci), stav systému windows, které jsou již zpracovány je *není* resetovat. Aktivační událost respektuje nové **endTime** hodnotu. Pokud nový **endTime** hodnotu před windows, které jsou již spuštěny, aktivační událost zarážky. V opačném případě se aktivační událost zastaví, když nové **endTime** zjištěna hodnota.

## <a name="sample-for-azure-powershell"></a>Ukázky Azure powershellu
Tato část ukazuje, jak pomocí prostředí Azure PowerShell k vytvoření, spuštění a monitorování aktivační události.

1. Vytvořte soubor JSON s názvem **MyTrigger.json** ve složce C:\ADFv2QuickStartPSH\ s následujícím obsahem:

   > [!IMPORTANT]
   > Předtím, než jste uložili soubor JSON, nastavte hodnotu **startTime** prvek na aktuální čas UTC. Nastavte hodnotu **endTime** element na jednu hodinu, posledních do aktuálního času UTC.

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

2. Vytvoření aktivační události pomocí **Set-AzureRmDataFactoryV2Trigger** rutiny:

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Ověřte, zda je stav triggeru **Zastaveno** pomocí **Get-AzureRmDataFactoryV2Trigger** rutiny:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Spusťte trigger pomocí **Start-AzureRmDataFactoryV2Trigger** rutiny:

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Ověřte, zda je stav triggeru **spuštěno** pomocí **Get-AzureRmDataFactoryV2Trigger** rutiny:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Aktivační událost spouští v prostředí Azure PowerShell pomocí Get **Get-AzureRmDataFactoryV2TriggerRun** rutiny. Pokud chcete získat informace o spuštění aktivační události, spusťte následující příkaz pravidelně. Aktualizace **TriggerRunStartedAfter** a **TriggerRunStartedBefore** hodnoty odpovídají hodnotám v definici aktivační události:

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Monitorování spuštění aktivační události a kanál se spouští na webu Azure Portal, najdete v článku [monitorování spuštění kanálu](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Další postup
Podrobné informace o aktivačních událostech najdete v tématu [spouštění kanálů a triggery](concepts-pipeline-execution-triggers.md#triggers).
