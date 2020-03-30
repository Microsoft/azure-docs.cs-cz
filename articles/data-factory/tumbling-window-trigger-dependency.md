---
title: Vytvořit závislosti aktivační události omílání okna
description: Zjistěte, jak vytvořit závislost na aktivační události omílání okna v Azure Data Factory.
services: data-factory
ms.author: daperlov
author: djpmsft
manager: anandsub
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/29/2019
ms.openlocfilehash: 0557c9b9eb65654c4a11c1389ace4776ab60a61d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532566"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Vytvoření závislosti aktivační události pro přeskakující okno

Tento článek obsahuje kroky k vytvoření závislosti na aktivační události omílání okna. Obecné informace o aktivačních událostech omílání okna naleznete v tématu [Jak vytvořit aktivační událost omílání okna](how-to-create-tumbling-window-trigger.md).

Chcete-li vytvořit řetěz závislostí a ujistěte se, že aktivační událost je spuštěna pouze po úspěšném spuštění jiné aktivační události v datové továrně, použijte tuto pokročilou funkci k vytvoření závislosti okna omílání.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Vytvoření závislosti v unovém nastavení datové továrny

Chcete-li vytvořit závislost na aktivační události, vyberte **možnost Aktivační > Upřesnit > Nová**a pak zvolte aktivační událost, na které se bude záviset s příslušným posunem a velikostí. Vyberte **Dokončit** a publikujte změny datové továrny, aby se závislosti projevily.

![Vytvoření závislostí](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "Vytvoření závislostí")

## <a name="tumbling-window-dependency-properties"></a>Vlastnosti závislostí okna omílání

Aktivační událost omílání okna se závislostí má následující vlastnosti:

```json
{
    "name": "MyTriggerName",
    "properties": {
        "type": "TumblingWindowTrigger",
        "runtimeState": <<Started/Stopped/Disabled - readonly>>,
        "typeProperties": {
            "frequency": <<Minute/Hour>>,
            "interval": <<int>>,
            "startTime": <<datetime>>,
            "endTime": <<datetime – optional>>,
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
        }
    }
}
```

V následující tabulce je uveden seznam atributů potřebných k definování závislosti okna omílání.

| **Název vlastnosti** | **Popis**  | **Typ** | **Požadováno** |
|---|---|---|---|
| type  | V této rozevírací části se zobrazí všechny existující aktivační události omílání oken. Zvolte aktivační událost, na které chcete převzít závislost.  | TumblingWindowTriggerDependencyReference reference nebo selfdependencyTumblingWindowTriggerReference Reference | Ano |
| posun | Posun aktivační události závislosti. Zadejte hodnotu ve formátu časového rozpětí a jsou povoleny záporné i kladné posuny. Tato vlastnost je povinná, pokud aktivační událost závisí na sobě a ve všech ostatních případech je volitelná. Sebezávislost by měla být vždy záporný posun. Pokud není zadána žádná hodnota, okno je stejné jako samotná aktivační událost. | Časový interval<br/>(hh:mm:ss) | Sebezávislost: Ano<br/>Ostatní: Ne |
| velikost | Velikost okna omílání závislostí. Zadejte kladnou hodnotu timespan. Tato vlastnost je nepovinná. | Časový interval<br/>(hh:mm:ss) | Ne  |

> [!NOTE]
> Aktivační událost omílání okna může záviset na maximálně dvou dalších aktivačních událostech.

## <a name="tumbling-window-self-dependency-properties"></a>Vlastnosti sebezávislosti v okně

Ve scénářích, kde aktivační událost by neměla pokračovat do dalšího okna, dokud není úspěšně dokončeno předchozí okno, vytvořte závislost na sebe. Aktivační událost vlastní závislosti, která je závislá na úspěchu dřívějších spuštění v rámci předchozího hr, bude mít níže uvedené vlastnosti:

```json
{
    "name": "DemoSelfDependency",
    "properties": {
        "runtimeState": "Started",
        "pipeline": {
            "pipelineReference": {
                "referenceName": "Demo",
                "type": "PipelineReference"
            }
        },
        "type": "TumblingWindowTrigger",
        "typeProperties": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2018-10-04T00:00:00Z",
            "delay": "00:01:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "SelfDependencyTumblingWindowTriggerReference",
                    "size": "01:00:00",
                    "offset": "-01:00:00"
                }
            ]
        }
    }
}
```
## <a name="usage-scenarios-and-examples"></a>Scénáře použití a příklady

Níže jsou uvedeny ilustrace scénářů a použití vlastností závislostí omílání okna.

### <a name="dependency-offset"></a>Posun závislostí

![Příklad odsazení](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Příklad odsazení")

### <a name="dependency-size"></a>Velikost závislosti

![Příklad velikosti](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "Příklad velikosti")

### <a name="self-dependency"></a>Sebezávislost

![Sebezávislost](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "Sebezávislost")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Závislost na jiné aktivační události omílání okna

Denní úloha zpracování telemetrie v závislosti na jiné denní úloze agregující výstup za posledních sedm dní a generuje sedmidenní datové proudy svislých oken:

![Příklad závislosti](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "Příklad závislosti")

### <a name="dependency-on-itself"></a>Závislost na sobě

Denní práce bez mezer ve výstupních tocích úlohy:

![Příklad vlastní závislosti](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "Příklad vlastní závislosti")

Ukázku toho, jak vytvořit závislé kanály ve vaší Azure Data Factory pomocí aktivační události omílání okna, podívejte se na následující video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="monitor-dependencies"></a>Sledování závislostí

Můžete sledovat řetěz závislostí a odpovídající okna ze stránky monitorování spuštění aktivační události. Přejděte na **> spuštění aktivační události .** Ve sloupci akce můžete znovu spustit aktivační událost nebo zobrazit její závislosti.

![Monitorování spuštění aktivační události](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "Monitorování spuštění aktivační události")

Pokud kliknete na "Zobrazit závislosti aktivačních událostí", uvidíte stav závislostí. Pokud se jeden z aktivačních událostí závislostí nezdaří, je nutné jej úspěšně znovu spustit, aby byla spuštěna závislá aktivační událost. Aktivační událost omílání okna bude čekat na závislosti sedm dní před vypršením časového limitu.

![Sledování závislostí](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "Sledování závislostí")

Chcete-li zobrazit plán závislostí aktivační události, vyberte zobrazení Ganttova diagramu.

![Sledování závislostí](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "Sledování závislostí")

## <a name="next-steps"></a>Další kroky

* Recenze [Jak vytvořit aktivační událost omílání okna](how-to-create-tumbling-window-trigger.md)
