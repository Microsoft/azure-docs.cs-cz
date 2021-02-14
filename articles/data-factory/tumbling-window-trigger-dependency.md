---
title: Vytvořit závislosti aktivačních signálů pro bubnový interval
description: Naučte se vytvořit závislost na triggeru bubnového okna v Azure Data Factory.
ms.author: chez
author: chez-charlie
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/03/2020
ms.openlocfilehash: f969c06a3419a8017cfc5ebc0de19caa67c8dc68
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361464"
---
# <a name="create-a-tumbling-window-trigger-dependency"></a>Vytvoření závislosti aktivační události pro přeskakující okno
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Tento článek popisuje kroky pro vytvoření závislosti pro aktivační událost bubnového okna. Obecné informace o aktivačních událostech pro bubny v okně najdete v tématu [Vytvoření aktivační události pro bubnové okno](how-to-create-tumbling-window-trigger.md).

Pokud chcete vytvořit řetěz závislostí a ujistit se, že se Trigger spustí až po úspěšném provedení jiného triggeru v datové továrně, použijte tuto pokročilou funkci a vytvořte si bubnovou závislost okna.

Ukázku, jak vytvořit závislé kanály v Azure Data Factory pomocí triggeru bubnového okna, najdete v následujícím videu:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Create-dependent-pipelines-in-your-Azure-Data-Factory/player]

## <a name="create-a-dependency-in-the-data-factory-ui"></a>Vytvoření závislosti v uživatelském rozhraní Data Factory

Pokud chcete vytvořit závislost na triggeru, vyberte **trigger > upřesnit > nový** a potom zvolte Trigger, který bude záviset na odpovídajícím posunu a velikosti. Vyberte **Dokončit** a publikujte změny objektu pro vytváření dat, aby se závislosti projevily.

![Vytvoření závislosti](media/tumbling-window-trigger-dependency/tumbling-window-dependency-01.png "Vytvoření závislosti")

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

| **Název vlastnosti** | **Popis**  | **Typ** | **Povinné** |
|---|---|---|---|
| typ  | V tomto rozevíracím seznamu se zobrazí všechny existující triggery bubnového okna. Vyberte aktivační událost, u které se má provést závislost.  | TumblingWindowTriggerDependencyReference nebo SelfDependencyTumblingWindowTriggerReference | Yes |
| posun | Posun triggeru závislosti. Zadejte hodnotu ve formátu časového rozsahu a jsou povoleny záporné i kladné posuny. Tato vlastnost je povinná, pokud je Trigger závislý sám na sobě a ve všech ostatních případech je nepovinný. Samostatná závislost by měla být vždy záporný posun. Pokud není zadána žádná hodnota, bude okno stejné jako Trigger sám. | Časový interval<br/>(hh: mm: SS) | Samostatná závislost: Ano<br/>Jiné: ne |
| size | Velikost bubnového okna závislosti Zadejte kladnou hodnotu TimeSpan. Tato vlastnost je nepovinná. | Časový interval<br/>(hh: mm: SS) | No  |

> [!NOTE]
> Aktivační událost bubnového okna může záviset na maximálně pět dalších triggerů.

## <a name="tumbling-window-self-dependency-properties"></a>Vlastnosti samoobslužné závislosti bubnového okna

Ve scénářích, kdy Trigger by neměl pokračovat k dalšímu oknu, dokud se předchozí okno úspěšně nedokončí, sestavte samostatnou závislost. Trigger samoobslužné závislosti závislý na úspěšnosti předchozích spuštění během předchozí hodiny budou mít vlastnosti, které jsou uvedeny v následujícím kódu.

> [!NOTE]
> Pokud se vaše aktivované kanály spoléhá na výstup kanálů v dřív aktivovaných oknech, doporučujeme použít jenom vlastní ovládací okno s triggerem. Pokud chcete omezit spuštění paralelní triggeru, nastavte souběžnost triggeru maximimum.

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

![Příklad posunutí](media/tumbling-window-trigger-dependency/tumbling-window-dependency-02.png "Příklad posunutí")

### <a name="dependency-size"></a>Velikost závislosti

![Příklad velikosti](media/tumbling-window-trigger-dependency/tumbling-window-dependency-03.png "Příklad velikosti")

### <a name="self-dependency"></a>Samostatná závislost

![Samostatná závislost](media/tumbling-window-trigger-dependency/tumbling-window-dependency-04.png "Samostatná závislost")

### <a name="dependency-on-another-tumbling-window-trigger"></a>Závislost na jiném triggeru bubnového okna

Každodenní úloha zpracování telemetrie v závislosti na dalších denních úlohách, které agreguje výstup posledních sedmi dnů a vygeneruje sedm dní postupných datových proudů oken:

![Příklad závislosti](media/tumbling-window-trigger-dependency/tumbling-window-dependency-05.png "Příklad závislosti")

### <a name="dependency-on-itself"></a>Závislá na sobě

Denní úloha bez mezer ve výstupních streamech úlohy:

![Příklad samostatné závislosti](media/tumbling-window-trigger-dependency/tumbling-window-dependency-06.png "Příklad samostatné závislosti")

## <a name="monitor-dependencies"></a>Monitorování závislostí

Řetěz závislostí a odpovídající okna můžete monitorovat na stránce monitorování spuštění aktivační události. Přejděte na  **monitorování > spuštění aktivační události**. Pokud má aktivační událost u bubnového okna závislosti, bude název aktivační události opatřen hypertextovým odkazem na zobrazení monitorování závislostí.  

![Monitorování spuštění aktivační události](media/tumbling-window-trigger-dependency/tumbling-window-dependency-07.png "Monitorovat spuštění aktivačních událostí – celá okna pro zobrazení závislostí na bubnu")

Kliknutím na název aktivační události zobrazíte závislosti aktivačních událostí. Panel na pravé straně zobrazuje podrobné informace o spuštění aktivační události, například RunID, čas okna, stav atd.

![Monitorovat zobrazení seznamu závislostí](media/tumbling-window-trigger-dependency/tumbling-window-dependency-08.png "Monitorovat zobrazení seznamu závislostí")

Můžete zobrazit stav závislostí a okna jednotlivých závislých triggerů. Pokud jedna z triggerů závislostí neproběhne úspěšně, je nutné ji znovu spustit, aby se spustila závislá aktivační událost.

Aktivační událost bubnového okna počká na závislosti po dobu _sedmi dnů_ , než se dokončí časový limit. Po sedmi dnech se spuštění triggeru nezdaří.

Pokud chcete zobrazit více vizuálů pro zobrazení plánu závislosti triggeru, vyberte zobrazení Ganttova diagramu.

![Monitorování závislostí Ganttova diagramu](media/tumbling-window-trigger-dependency/tumbling-window-dependency-09.png "Zobrazení Ganttova diagramu závislosti monitorování")

Průhledná pole zobrazují okna závislostí pro každou z těchto triggerů závislých na proudu, zatímco plná barevná pole výše ukazují, že se spouští jednotlivá okna. Zde jsou některé tipy pro interpretaci zobrazení Ganttova diagramu:

* Transparentní pole kreslí modrou, když jsou závislá okna v nedokončeném nebo spuštěném stavu.
* Po úspěšném spuštění všech oken u závislé triggeru se průhledné pole změní na zelenou.
* Transparentní pole vykreslí červenou, když některé závislé okno selžou. Pokud chcete identifikovat spuštění okna selhání, vyhledejte pevné červené pole.

Chcete-li znovu spustit okno v zobrazení Ganttova diagramu, zaškrtněte políčko plná barva okna a panel akce se zobrazí s podrobnostmi a možností opětovného spuštění

## <a name="next-steps"></a>Další kroky

* Přečtěte si [, jak vytvořit aktivační událost bubnového okna](how-to-create-tumbling-window-trigger.md) .
