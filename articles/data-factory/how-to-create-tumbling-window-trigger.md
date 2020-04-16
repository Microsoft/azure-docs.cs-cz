---
title: Vytvoření aktivačních událostí omílání oken v Azure Data Factory
description: Zjistěte, jak vytvořit aktivační událost v Azure Data Factory, která spouští kanál v omílání okna.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 09/11/2019
ms.openlocfilehash: 97c8f8a5bb2111264e9459a7d2128c1ab7c2503d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414431"
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-tumbling-window"></a>Vytvoření aktivační události, která spustí kanál v přeskakujícím okně
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek obsahuje kroky k vytvoření, spuštění a sledování aktivační události omílání okna. Obecné informace o aktivačních událostech a podporovaných typech naleznete v [tématu Pipeline execution and triggers](concepts-pipeline-execution-triggers.md).

Aktivační události pro přeskakující okno jsou typem aktivačních událostí, které se aktivuje v pravidelných časových intervalech od určeného počátečního okamžiku a které zachovávají stav. Přeskakující okna jsou řada nepřekrývajících se souvislých časových intervalů s pevnou velikostí. Aktivační událost omílání okna má vztah 1:1 s kanálem a může odkazovat pouze na singulární kanál.

## <a name="data-factory-ui"></a>Uživatelské rozhraní Data Factory

1. Chcete-li vytvořit aktivační událost omílání okna v uzdu datové **továrny,** vyberte kartu Aktivační události a pak vyberte **Nový**. 
1. Po otevření konfiguračního podokna aktivační události vyberte **možnost Přemístit okno**a pak definujte vlastnosti aktivační události okna omílání. 
1. Jakmile budete mít hotovo, vyberte **Uložit**.

![Vytvoření aktivační události omílání oken na webu Azure Portal](media/how-to-create-tumbling-window-trigger/create-tumbling-window-trigger.png)

## <a name="tumbling-window-trigger-type-properties"></a>Vlastnosti typu aktivační události okna omílání

Omílání okno má následující vlastnosti typu aktivační události:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": "<<Started/Stopped/Disabled - readonly>>",
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": "<<datetime>>",
            "endTime: <<datetime – optional>>,
            "delay": <<timespan – optional>>,
            "maxConcurrency": <<int>> (required, max allowed: 50),
            "retryPolicy": {
                "count": <<int - optional, default: 0>>,
                "intervalInSeconds": <<int>>,
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan – optional>>,
                    "referenceTrigger": {
                        "referenceName": "MyTumblingWindowDependency1",
                        "type": "TriggerReference"
                    }
                },
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": <<timespan – optional>>,
                    "offset": <<timespan>>
                }
            ]
        },
        "pipeline": {
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

Následující tabulka obsahuje přehled na vysoké úrovni hlavních prvků JSON, které souvisejí s opakováním a plánováním aktivační události omílání:

| Element JSON | Popis | Typ | Povolené hodnoty | Požaduje se |
|:--- |:--- |:--- |:--- |:--- |
| **Typ** | Typ aktivační události. Typ je pevná hodnota "TumblingWindowTrigger". | Řetězec | "TumblingWindowTrigger" | Ano |
| **runtimeState** | Aktuální stav běhu aktivační události.<br/>**Poznámka**: Tento \<prvek je pouze pro čtení>. | Řetězec | "Spuštěno", Zastaveno, Zakázáno. | Ano |
| **Frekvence** | Řetězec, který představuje frekvenční jednotku (minuty nebo hodiny), ve které se aktivační událost opakuje. Pokud jsou hodnoty data **startTime** podrobnější než hodnota **frekvence,** data **startTime** jsou při výpočtu hranic okna zohledněna. Například pokud je hodnota **frekvence** hodinová a hodnota **startTime** je 2017-09-01T10:10:10Z, první okno je (2017-09-01T10:10:10Z, 2017-09-01T11:10:10Z). | Řetězec | "minuta", "hodina"  | Ano |
| **interval** | Kladné celé číslo označující interval pro hodnotu **frequency**, která určuje, jak často se má aktivační událost spouštět. Pokud je například **interval** 3 a **frekvence** je "hodina", aktivační událost se opakuje každé 3 hodiny. <br/>**Poznámka:** Minimální interval okna je 5 minut. | Integer | Kladné celé číslo. | Ano |
| **startTime**| První výskyt, který může být v minulosti. První aktivační interval je (**startTime**, **startTime** + **interval**). | DateTime | A DateTime hodnota. | Ano |
| **endTime**| Poslední výskyt, který může být v minulosti. | DateTime | A DateTime hodnota. | Ano |
| **Zpoždění** | Doba zpoždění zahájení zpracování dat pro okno. Spuštění kanálu je spuštěna po očekávané době spuštění plus množství **zpoždění**. **Zpoždění** definuje, jak dlouho aktivační událost čeká po termínu před spuštěním nového spuštění. **Zpoždění** nemění **okno startTime**. Například hodnota **zpoždění** 00:10:00 znamená zpoždění 10 minut. | Časový interval<br/>(hh:mm:ss)  | Hodnota timespan, kde je výchozí hodnota 00:00:00. | Ne |
| **maxSouběžná měna** | Počet spuštění souběžných aktivačních událostí, které jsou aktivovány pro okna, která jsou připravena. Například pro zpětnou výplň hodinová spuštění pro včera výsledky ve 24 oknech. Pokud **maxConcurrency** = 10, aktivační události jsou aktivovány pouze pro prvních 10 oken (00:00-01:00 - 09:00-10:00). Po dokončení prvních 10 spuštění kanálu spouštění jsou aktivovány pro dalších 10 oken (10:00-11:00 - 19:00-20:00). Pokračování v tomto příkladu **maxConcurrency** = 10, pokud jsou připraveny 10 oken, je 10 celkový počet spuštění kanálu. Pokud je připraveno pouze 1 okno, je spuštěn pouze 1 kanál. | Integer | Celé číslo mezi 1 a 50. | Ano |
| **retryPolicy: Počet** | Počet opakování před spuštěním kanálu je označen jako "Nezdařilo se".  | Integer | Celé číslo, kde je výchozí hodnota 0 (žádné opakování). | Ne |
| **retryPolicy: intervalInSeconds** | Prodleva mezi pokusy o opakování zadané v sekundách. | Integer | Počet sekund, kde je výchozí 30. | Ne |
| **dependsOn: typ** | Typ TumblingWindowTriggerReference. Povinné, pokud je nastavena závislost. | Řetězec |  "TumblingWindowTriggerDependencyReferenceReference", "SelfDependencyTumblingWindowTriggerReference" | Ne |
| **dependsOn: velikost** | Velikost okna omílání závislostí. | Časový interval<br/>(hh:mm:ss)  | Kladná hodnota timespan, kde je výchozí velikost okna podřízené aktivační události  | Ne |
| **dependsOn: posun** | Posun aktivační události závislosti. | Časový interval<br/>(hh:mm:ss) |  Hodnota časový mj. Pokud není zadána žádná hodnota, okno je stejné jako samotná aktivační událost. | Sebezávislost: Ano<br/>Ostatní: Ne  |

### <a name="windowstart-and-windowend-system-variables"></a>Systémové proměnné WindowStart a WindowEnd

Systémové proměnné **WindowStart** a **WindowEnd** aktivační události omílání okna v definici **kanálu** (to znamená pro část dotazu). Předejte systémové proměnné jako parametry kanálu v definici **aktivační události.** Následující příklad ukazuje, jak předat tyto proměnné jako parametry:

```
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
            ...
        "pipeline": {
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

Chcete-li použít hodnoty systémových proměnných **WindowStart** a **WindowEnd** v definici kanálu, použijte podle toho parametry "MyWindowStart" a "MyWindowEnd".

### <a name="execution-order-of-windows-in-a-backfill-scenario"></a>Pořadí spuštění oken ve scénáři backfill
Pokud existuje více oken pro spuštění (zejména ve scénáři backfill), pořadí provádění pro windows je deterministický, od nejstarších po nejnovější intervaly. Toto chování v současné době není možné změnit.

### <a name="existing-triggerresource-elements"></a>Existující prvky TriggerResource
Následující body platí pro existující prvky **TriggerResource:**

* Pokud se změní hodnota prvku **frekvence** (nebo velikost okna) aktivační události, stav oken, které jsou již zpracovány, *není* resetován. Aktivační událost pokračuje v aktivaci oken z posledního okna, které byla spuštěna pomocí nové velikosti okna.
* Pokud se změní hodnota prvku **endTime** aktivační události (přidána nebo aktualizována), stav oken, které jsou již *zpracovány, není* resetován. Aktivační událost respektuje novou hodnotu **endTime.** Pokud je nová hodnota **endTime** před okny, která jsou již spuštěna, aktivační událost se zastaví. V opačném případě se aktivační událost zastaví, když dojde k nové hodnotě **endTime.**

### <a name="tumbling-window-trigger-dependency"></a>Závislost aktivační události omílání okna

Pokud se chcete ujistit, že aktivační událost omílání okna je spuštěna pouze po úspěšném spuštění jiného aktivační události omílání okna v datové továrně, [vytvořte závislost aktivační události omílání okna](tumbling-window-trigger-dependency.md). 

## <a name="sample-for-azure-powershell"></a>Ukázka pro Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Tato část ukazuje, jak pomocí Azure PowerShellu vytvořit, spustit a monitorovat aktivační událost.

1. Vytvořte soubor JSON s názvem **MyTrigger.json** ve složce C:\ADFv2QuickStartPSH\ s následujícím obsahem:

    > [!IMPORTANT]
    > Před uložením souboru JSON nastavte hodnotu elementu **startTime** na aktuální čas UTC. Nastavte hodnotu elementu **endTime** na jednu hodinu po aktuálním čase UTC.

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

2. Vytvořte aktivační událost pomocí rutiny **Set-AzDataFactoryV2Trigger:**

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
    
3. Zkontrolujte, zda je stav aktivační události **zastaven** pomocí rutiny **Get-AzDataFactoryV2Trigger:**

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Spusťte aktivační událost pomocí rutiny **Start-AzDataFactoryV2Trigger:**

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Zkontrolujte, zda je stav aktivační události **spuštěn** pomocí rutiny **Get-AzDataFactoryV2Trigger:**

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6. Získejte aktivační události běží v Azure PowerShell pomocí **Rutina Get-AzDataFactoryV2TriggerRun.** Chcete-li získat informace o spuštění aktivační události, spouštějte pravidelně následující příkaz. Aktualizujte hodnoty **TriggerRunStartedAfter** a **TriggerRunStartedBefore** tak, aby odpovídaly hodnotám v definici aktivační události:

    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
Co se snaží monitorovat spuštění aktivačních událostí a spuštění kanálu na webu Azure Portal, přečtěte si informace [o spuštění kanálu monitorování](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="next-steps"></a>Další kroky

* Podrobné informace o aktivačních událostech naleznete v [tématu Pipeline execution and triggers](concepts-pipeline-execution-triggers.md#trigger-execution).
* [Vytvoření závislosti aktivační události pro přeskakující okno](tumbling-window-trigger-dependency.md)
