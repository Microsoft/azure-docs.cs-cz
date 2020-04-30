---
title: Řešení potíží se vstupy pro Azure Stream Analytics
description: Tento článek popisuje techniky řešení potíží se vstupními připojeními v Azure Stream Analytics úlohách.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 68fe7da136d744e1efa76a89061afe6995a75051
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133261"
---
# <a name="troubleshoot-input-connections"></a>Řešení potíží se vstupními připojeními

Tento článek popisuje běžné problémy s Azure Stream Analytics vstupními připojeními, postupy při řešení problémů se vstupem a jak tyto problémy vyřešit. Mnoho kroků pro řešení potíží vyžaduje, aby byly pro vaši úlohu Stream Analytics Povolené protokoly prostředků. Pokud nemáte Povolené protokoly prostředků, přečtěte si téma [řešení potíží s Azure Stream Analytics pomocí protokolů prostředků](stream-analytics-job-diagnostic-logs.md).

## <a name="input-events-not-received-by-job"></a>Úloha nepřijala vstupní události. 

1.  Otestujte vstupní a výstupní připojení. Ověřte připojení ke vstupům a výstupům pomocí tlačítka **Testovat připojení** pro jednotlivé vstupy a výstupy.

2.  Prověřte vstupní data.

    1. Pro jednotlivé vstupy použijte tlačítko [**ukázková data**](stream-analytics-sample-data-input.md) . Stáhněte si vstupní ukázková data.
        
    1. Zkontrolujte ukázková data, abyste porozuměli schématu a [datovým typům](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics).
    
    1. Zkontrolujte [metriky centra událostí](../event-hubs/event-hubs-metrics-azure-monitor.md) a ujistěte se, že jsou události odesílány. Pokud Event Hubs přijímá zprávy, musí být metrika zpráv větší než nula.

3.  Ujistěte se, že jste ve vstupní verzi Preview vybrali časový rozsah. Zvolte **možnost vybrat časový rozsah**a pak před testováním dotazu zadejte dobu trvání vzorku.

## <a name="malformed-input-events-causes-deserialization-errors"></a>Poškozená vstupní událost způsobuje chyby deserializace 

Problémy s deserializací jsou způsobeny tím, že vstupní datový proud vaší Stream Analytics úlohy obsahuje poškozené zprávy. Poškozená zpráva může být například způsobena chybějícími závorkami nebo závorkami v objektu JSON nebo nesprávným formátem časového razítka v poli čas. 
 
Když úloha Stream Analytics obdrží ze vstupu poškozenou zprávu, zpráva se uvolní a upozorní vás upozorněním. Na dlaždici **vstupy** Stream Analytics úlohy se zobrazí výstražný symbol. Následující symbol upozornění existuje, dokud je úloha ve stavu spuštěno:

![Dlaždice Azure Stream Analytics vstupy](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Povolte protokoly prostředků, abyste zobrazili podrobnosti o chybě a zprávě (datové části), která způsobila chybu. Existuje několik důvodů, proč může docházet k chybám deserializace. Další informace o konkrétních chybách deserializace najdete v tématu [chyby vstupních dat](data-errors.md#input-data-errors). Pokud nejsou povoleny protokoly prostředků, bude v Azure Portal k dispozici krátké oznámení.

![Upozornění na zadání podrobností o vstupu](media/stream-analytics-malformed-events/warning-message-with-offset.png)

V případech, kdy je datová část zprávy větší než 32 KB nebo v binárním formátu, spusťte kód CheckMalformedEvents.cs, který je k dispozici v [úložišti ukázek GitHubu](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Tento kód přečte ID oddílu, posun a vytiskne data, která jsou umístěna v tomto posunu. 

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Úloha překročila maximální počet přijímačů centra událostí.

Osvědčeným postupem použití Event Hubs je použití více skupin uživatelů pro škálovatelnost úloh. Počet čtenářů v Stream Analytics úlohy pro konkrétní vstup ovlivňuje počet čtenářů v jedné skupině příjemců. Přesný počet přijímačů je založený na podrobnostech o interní implementaci logiky topologie se škálováním na více instancí a nezveřejňuje se externě. Počet čtenářů se může změnit při spuštění úlohy nebo během upgradování úlohy.

Při překročení maximálního počtu přijímačů se zobrazí tato chyba: 

`The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Když se během upgradu úlohy změní počet čtenářů, do protokolů auditu se zapisují přechodná upozornění. Stream Analytics úlohy se automaticky Obnovují z těchto přechodných problémů.

### <a name="add-a-consumer-group-in-event-hubs"></a>Přidat skupinu příjemců v Event Hubs

Pokud chcete do instance Event Hubs přidat novou skupinu příjemců, postupujte takto:

1. Přihlaste se k portálu Azure.

2. Vyhledejte centrum událostí.

3. V záhlaví **entity** vyberte **Event Hubs** .

4. Vyberte centrum událostí podle názvu.

5. Na stránce **Event Hubs instance** pod nadpisem **entity** vyberte **skupiny uživatelů**. V seznamu je uvedena skupina uživatelů s názvem **$Default** .

6. Vyberte **+ Skupina uživatelů** a přidejte novou skupinu příjemců. 

   ![Přidat skupinu příjemců v Event Hubs](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Když jste vytvořili vstup v úloze Stream Analytics, abyste odkazovali na centrum událostí, zadali jste skupinu příjemců. **$Default** se používá v případě, že není zadán parametr None. Jakmile vytvoříte novou skupinu příjemců, upravte vstup centra událostí v úloze Stream Analytics a zadejte název nové skupiny příjemců.

## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Počet čtenářů na oddíl překračuje limit Event Hubs.

Pokud syntaxe dotazu streamování odkazuje na stejný prostředek centra událostí vstupu víckrát, může modul úloh použít více čtenářů na dotaz ze stejné skupiny příjemců. Pokud existuje příliš mnoho odkazů na stejnou skupinu uživatelů, může úloha překročit limit pět a vyvolala chybu. V těchto případech můžete dál dělit pomocí více vstupů napříč více skupinami příjemců pomocí řešení popsaného v následující části. 

Scénáře, ve kterých počet čtenářů na oddíl přesáhne Event Hubs limitu pěti, zahrnují následující:

* Vícenásobné příkazy SELECT: Pokud použijete více příkazů SELECT, které odkazují na **stejný** vstup centra událostí, každý příkaz SELECT způsobí vytvoření nového příjemce.

* SJEDNOCENí: když použijete SJEDNOCENí, je možné mít několik vstupů, které odkazují na **stejné** centrum událostí a skupinu uživatelů.

* Připojovat se k sobě: když použijete operaci připojovat do sebe, je možné, že se na **stejné** centrum událostí odkazuje víckrát.

Následující osvědčené postupy mohou přispět ke zmírnění scénářů, ve kterých počet čtenářů na oddíl překračuje limit Event Hubs 5.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Rozdělit dotaz do více kroků pomocí klauzule WITH

Klauzule WITH určuje dočasnou pojmenovanou sadu výsledků, na kterou může odkazovat klauzule FROM v dotazu. Definujte klauzuli WITH v oboru provádění jednoho příkazu SELECT.

Například místo tohoto dotazu:

```SQL
SELECT foo 
INTO output1
FROM inputEventHub

SELECT bar
INTO output2
FROM inputEventHub 
…
```

Použít tento dotaz:

```SQL
WITH data AS (
   SELECT * FROM inputEventHub
)

SELECT foo
INTO output1
FROM data

SELECT bar
INTO output2
FROM data
…
```

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Zajistěte, aby vstupy byly vázány na různé skupiny příjemců.

Pro dotazy, ve kterých jsou tři nebo více vstupů připojeni ke stejné Event Hubs skupině uživatelů, vytvořte samostatné skupiny příjemců. To vyžaduje vytvoření dalších vstupů Stream Analytics.

## <a name="get-help"></a>Podpora

Pokud potřebujete další pomoc, vyzkoušejte naši [Azure Stream Analytics Fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
