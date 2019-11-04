---
title: Vytvořit závislosti triggerů bubnového okna v Azure Data Factory | Microsoft Docs
description: Naučte se vytvořit závislost na triggeru bubnového okna v Azure Data Factory.
services: data-factory
documentationcenter: ''
author: djpmsft
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: daperlov
ms.openlocfilehash: 24a1a5d132990db2aa10b7860774eecafb4b4edb
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2019
ms.locfileid: "73520507"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Vytvoření závislosti aktivační události pro přeskakující okno

Tento článek popisuje kroky pro vytvoření závislosti pro aktivační událost bubnového okna. Obecné informace o aktivačních událostech pro bubny v okně najdete v tématu [Vytvoření aktivační události pro bubnové okno](how-to-create-tumbling-window-trigger.md).

Pokud chcete vytvořit řetěz závislostí a ujistit se, že se Trigger spustí až po úspěšném provedení jiného triggeru v datové továrně, použijte tuto pokročilou funkci a vytvořte si bubnovou závislost okna.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Vytvoření závislosti v uživatelském rozhraní Data Factory

Pokud chcete vytvořit závislost na triggeru, vyberte **trigger > upřesnit > nový**a potom zvolte Trigger, který bude záviset na odpovídajícím posunu a velikosti. Vyberte **Dokončit** a publikujte změny objektu pro vytváření dat, aby se závislosti projevily.

![Vytvoření závislosti](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png "Vytvoření závislosti")

## <a name="tumbling-window-dependency-properties"></a>Vlastnosti závislosti bubnového okna

Aktivační událost bubnového okna se závislostí má následující vlastnosti:

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

Následující tabulka uvádí seznam atributů potřebných k definování závislosti bubnu okna.

| **Název vlastnosti** | **Popis**  | **Typ** | **Požadovanou** |
|---|---|---|---|
| type  | V tomto rozevíracím seznamu se zobrazí všechny existující triggery bubnového okna. Vyberte aktivační událost, u které se má provést závislost.  | TumblingWindowTriggerDependencyReference nebo SelfDependencyTumblingWindowTriggerReference | Ano |
| polohy | Posun triggeru závislosti. Zadejte hodnotu ve formátu časového rozsahu a jsou povoleny záporné i kladné posuny. Tato vlastnost je povinná, pokud je Trigger závislý sám na sobě a ve všech ostatních případech je nepovinný. Samostatná závislost by měla být vždy záporný posun. Pokud není zadána žádná hodnota, bude okno stejné jako Trigger sám. | Časový interval<br/>(hh: mm: SS) | Samostatná závislost: Ano<br/>Jiné: ne |
| Hodnota | Velikost bubnového okna závislosti Zadejte kladnou hodnotu TimeSpan. Tato vlastnost je nepovinná. | Časový interval<br/>(hh: mm: SS) | Ne  |

> [!NOTE]
> Aktivační událost bubnového okna může záviset na maximálním počtu dvou dalších triggerů.

## <a name="tumbling-window-self-dependency-properties"></a>Vlastnosti samoobslužné závislosti bubnového okna

Ve scénářích, kdy by aktivační událost neměla pokračovat k dalšímu oknu, dokud se předchozí okno úspěšně nedokončí, sestavte samostatnou závislost. Trigger samoobslužné závislosti, který je závislý na úspěšnosti předchozích spuštění v rámci předchozího HR, bude mít následující vlastnosti:

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

Níže najdete ilustrace scénářů a využití vlastností závislosti bubnu okna.

### <a name="dependency-offset"></a>Posun závislosti

![Příklad posunutí](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png "Příklad posunutí")

### <a name="dependency-size"></a>Velikost závislosti

![Příklad velikosti](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png "Příklad velikosti")

### <a name="self-dependency"></a>Samostatná závislost

![Samostatná závislost](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png "Samostatná závislost")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Závislost na jiném triggeru bubnového okna

Každodenní úloha zpracování telemetrie v závislosti na dalších denních úlohách, které agreguje výstup posledních sedmi dnů a vygeneruje sedm dní postupných datových proudů oken:

![Příklad závislosti](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png "Příklad závislosti")

### <a name="dependency-on-itself"></a>Závislá na sobě

Denní úloha bez mezer ve výstupních streamech úlohy:

![Příklad samostatné závislosti](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png "Příklad samostatné závislosti")

## <a name="monitor-dependencies"></a>Monitorování závislostí

Řetěz závislostí a odpovídající okna můžete monitorovat na stránce monitorování spuštění aktivační události. Přejděte na **monitorování > spuštění aktivační události**. Ve sloupci akce můžete Trigger spustit znovu nebo zobrazit jeho závislosti.

![Monitorování spuštění aktivační události](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png "Monitorování spuštění aktivační události")

Pokud kliknete na možnost zobrazit závislosti triggeru, uvidíte stav závislostí. Pokud se jedna z triggerů závislosti nezdaří, je nutné ji úspěšně znovu spustit, aby se spustila závislá aktivační událost. Aktivační událost bubnového okna počká na závislosti po dobu sedmi dnů, než se dokončí časový limit.

![Monitorování závislostí](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png "Monitorování závislostí")

Pokud chcete zobrazit více vizuálů pro zobrazení plánu závislosti triggeru, vyberte zobrazení Ganttova diagramu.

![Monitorování závislostí](media/tumbling-window-trigger-dependency/tumbling-window-dependency09.png "Monitorování závislostí")

## <a name="next-steps"></a>Další kroky

* Přečtěte si [, jak vytvořit aktivační událost bubnového okna](how-to-create-tumbling-window-trigger.md) .
