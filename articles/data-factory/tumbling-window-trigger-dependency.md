---
title: Vytvoření aktivační události pro přeskakující okno aktivační událost závislostí ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit závislost na přeskakující okno ve službě Azure Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
editor: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: douglasl
ms.openlocfilehash: c51c1056869cbd7741fae2ed1a554a7c794d1a39
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967150"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Vytvoření triggeru závislosti aktivační událost pro přeskakující okno

Tento článek popisuje kroky vytvoření závislosti na přeskakující okno. Obecné informace o aktivačních událostech Přeskakujícího okna najdete v tématu [vytvoření událost pro přeskakující okno](how-to-create-tumbling-window-trigger.md).

Pokud chcete vytvořit řetěz závislostí a ujistěte se, že aktivační událost se provádí pouze po úspěšném spuštění další trigger v datové továrně, tato funkce Rozšířené vytvoření aktivační události pro přeskakující okno závislosti.

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Vytvoření závislosti v Uživatelském rozhraní služby Data Factory

Vytvoření závislosti na aktivační událost, vyberte **aktivační událost > Upřesnit > New**a zvolte aktivační událost závisí na odpovídající posun a velikost. Vyberte **Dokončit** a publikovat změny data factory pro závislosti se projeví.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency01.png)

## <a name="tumbling-window-dependency-properties"></a>Vlastnosti závislostí aktivační událost pro přeskakující okno

Závislost aktivační událost pro přeskakující okno aktivační události má následující vlastnosti:

```json
{
    "name": "DemoTrigger",
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
            "startTime": "2018-10-04T00:00:00.000Z",
            "delay": "00:00:00",
            "maxConcurrency": 50,
            "retryPolicy": {
                "intervalInSeconds": 30
            },
            "dependsOn": [
                {
                    "type": "TumblingWindowTriggerDependencyReference",
                    "size": "-02:00:00",
                    "offset": "-01:00:00",
                    "referenceTrigger": {
                        "referenceName": "DemoDependency1",
                        "type": "TriggerReference"
                    }
                }
            ]
        }
    }
}
```

Následující tabulka obsahuje seznam atributy, které jsou potřebné k definování závislostí Přeskakujícího okna.

| **Název vlastnosti** | **Popis**  | **Typ** | **Požadováno** |
|---|---|---|---|
| Trigger  | Všechny existující aktivační událost pro přeskakující okno aktivační události se zobrazí v této rozevírací dolů. Zvolte trigger se závislostí.  | TumblingWindowTrigger | Ano |
| Posun | Posun Aktivátor závislostí. Zadejte hodnotu ve formátu rozpětí času a jsou povoleny posunutí záporné i kladné. Tento parametr je povinný, že pokud se aktivační událost se v závislosti na samotný a ve všech ostatních případech je volitelné. Vlastní závislost by měla být vždy záporný posun. | Časový interval | Vlastní závislost: Ano, jiné: Ne |
| Velikost okna | Velikost aktivační událost pro přeskakující okno závislostí. Zadejte hodnotu ve formátu rozpětí času. Tento parametr je volitelný. | Časový interval | Ne  |
|||||

## <a name="tumbling-window-self-dependency-properties"></a>Aktivační událost pro přeskakující okno závislost vlastní vlastnosti

Ve scénářích, kde by neměl aktivační událost přejít k další okno předchozího okna je úspěšně dokončen vytvářejte vlastní závislost. Vlastní závislost aktivace bude mít následující vlastnosti:

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

Níže jsou uvedeny ilustrace scénáře a využití aktivační událost pro přeskakující okno vlastnosti závislosti.

## <a name="dependency-offset"></a>Posun závislostí

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency02.png)

## <a name="dependency-size"></a>Velikost závislostí

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency03.png)

## <a name="self-dependency"></a>Vlastní závislost

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency04.png)

## <a name="usage-scenarios"></a>Scénáře použití

### <a name="dependency-on-another-tumbling-window-trigger"></a>Závislost na jiné pro přeskakující okno

Každodenní úlohu zpracování telemetrických dat v závislosti na jiná úloha denní agregace posledních sedmi dnů, výstupní a generuje sedm dní postupné okno datové proudy:

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency05.png)

### <a name="dependency-on-itself"></a>Závislost na sobě

Každodenní úlohy bez mezer ve výstupní datové proudy úlohy:

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency06.png)

## <a name="monitor-dependencies"></a>Monitorovat závislosti

Můžete monitorovat řetězec závislostí a odpovídající windows z triggeru spusťte monitorování stránku. Přejděte do **monitorování > spouštět testy**.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency07.png)

Klikněte na skla looking chcete zobrazit všechny závislé aktivační událost se spustí z vybrané okno.

![](media/tumbling-window-trigger-dependency/tumbling-window-dependency08.png)

## <a name="next-steps"></a>Další postup

Kontrola [vytvoření přeskakující okno](how-to-create-tumbling-window-trigger.md).