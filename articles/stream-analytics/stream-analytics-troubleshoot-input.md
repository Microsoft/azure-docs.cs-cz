---
title: Poradce při potížích se vstupy pro Azure Stream Analytics
description: Tento článek popisuje techniky řešení potíží se vstupními připojeními v azure stream analytics úlohy.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: 3d88123b3dd79e5707c5c19cbbae13c30cbdeb84
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409421"
---
# <a name="troubleshoot-input-connections"></a>Řešení potíží se vstupními připojeními

Tento článek popisuje běžné problémy se vstupními připojeními Azure Stream Analytics, jak řešit problémy se vstupem a jak problémy opravit. Mnoho kroků řešení potíží vyžaduje, aby byly pro úlohu Stream Analytics povoleny diagnostické protokoly. Pokud nemáte povolené diagnostické protokoly, [přečtěte si článek Poradce při potížích s Azure Stream Analytics pomocí diagnostických protokolů](stream-analytics-job-diagnostic-logs.md).

## <a name="input-events-not-received-by-job"></a>Vstupní události, které nebyly přijaty podle úlohy 

1.  Otestujte vstupní a výstupní připojení. Ověřte připojení ke vstupům a výstupům pomocí tlačítka **Testovat připojení** pro každý vstup a výstup.

2.  Zkontrolujte vstupní data.

    1. Pro každý vstup použijte tlačítko [**Ukázková data.**](stream-analytics-sample-data-input.md) Stáhněte si vstupní ukázková data.
        
    1. Zkontrolujte ukázková data, abyste porozuměli schématu a [datovým typům](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics).
    
    1. Zkontrolujte [metriky Centra událostí,](../event-hubs/event-hubs-metrics-azure-monitor.md) abyste zajistili, že se události budou odesílat. Metriky zpráv by měly být větší než nula, pokud centra událostí přijímá zprávy.

3.  Ujistěte se, že jste ve vstupním náhledu vybrali časový rozsah. Zvolte **Vybrat časový rozsah**a před testováním dotazu zadejte dobu trvání vzorku.

## <a name="malformed-input-events-causes-deserialization-errors"></a>Poškozená vstupní událost způsobuje chyby deserializace 

Problémy s deserialací jsou způsobeny, když vstupní datový proud úlohy Stream Analytics obsahuje poškozené zprávy. Například poškozená zpráva může být způsobena chybějící závorkou nebo složenou závorkou v objektu JSON nebo nesprávným formátem časového razítka v časovém poli. 
 
Když úloha Stream Analytics obdrží z oněcovanou zprávu ze vstupu, zahodí zprávu a upozorní vás upozorněním. Na dlaždici **Vstupy** v úloze Stream Analytics se zobrazí výstražný symbol. Následující symbol upozornění existuje tak dlouho, dokud je úloha ve spuštěném stavu:

![Dlaždice vstupů Azure Stream Analytics](media/stream-analytics-malformed-events/stream-analytics-inputs-tile.png)

Povolit protokoly diagnostiky chcete zobrazit podrobnosti o chybě a zprávy (datové části), která způsobila chybu. Existuje několik důvodů, proč může dojít k chybám deserializace. Další informace týkající se konkrétních chyb deserializace naleznete [v tématu Chyby vstupních dat](data-errors.md#input-data-errors). Pokud diagnostické protokoly nejsou povolené, krátké oznámení bude k dispozici na webu Azure Portal.

![Upozornění na vstupní podrobnosti](media/stream-analytics-malformed-events/warning-message-with-offset.png)

V případech, kdy je datová část zprávy větší než 32 kB nebo je v binárním formátu, spusťte CheckMalformedEvents.cs kód, který je k dispozici v [úložišti ukázek GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/Samples/CheckMalformedEventsEH). Tento kód přečte ID oddílu, posun a vytiskne data, která je umístěna v tomto posunu. 

## <a name="job-exceeds-maximum-event-hub-receivers"></a>Úloha překračuje maximální počet přijímačů centra událostí.

Osvědčeným postupem pro použití centra událostí je použití více skupin spotřebitelů pro škálovatelnost úloh. Počet čtenářů v úloze Stream Analytics pro konkrétní vstup ovlivňuje počet čtenářů v jedné skupině spotřebitelů. Přesný počet přijímačů je založen na podrobnostech interní implementace pro logiku topologie horizontálního navýšení kapacity a není vystaven externě. Počet čtenářů se může změnit při spuštění úlohy nebo při upgradu úlohy.

Při překročení maximálního počtu přijímačů se zobrazí tato chyba: 

`The streaming job failed: Stream Analytics job has validation errors: Job will exceed the maximum amount of Event Hub Receivers.`

> [!NOTE]
> Při změně počtu čtenářů během upgradu úlohy jsou do protokolů auditu zapsána přechodná upozornění. Úlohy Stream Analytics se automaticky zotavují z těchto přechodných problémů.

### <a name="add-a-consumer-group-in-event-hubs"></a>Přidání skupiny spotřebitelů do centra událostí

Pokud chcete do instance Event Hubs přidat novou skupinu spotřebitelů, postupujte takto:

1. Přihlaste se k portálu Azure.

2. Vyhledejte centrum událostí.

3. Pod nadpisem **Entity** **vyberte Centra událostí.**

4. Vyberte Centrum událostí podle názvu.

5. Na stránce **Instance Centra událostí** vyberte pod nadpisem **Entity** **skupiny spotřebitelů**. Je uvedena skupina spotřebitelů s názvem **$Default.**

6. Výběrem **možnosti + skupina spotřebitelů** přidáte novou skupinu spotřebitelů. 

   ![Přidání skupiny spotřebitelů do centra událostí](media/stream-analytics-event-hub-consumer-groups/new-eh-consumer-group.png)

7. Když jste vytvořili vstup v úloze Stream Analytics tak, aby přecšlákem na Centrum událostí, zadali jste tam skupinu spotřebitelů. **$Default** se používá, pokud není zadán žádný. Po vytvoření nové skupiny spotřebitelů upravte vstup centra událostí v úloze Stream Analytics a zadejte název nové skupiny spotřebitelů.

## <a name="readers-per-partition-exceeds-event-hubs-limit"></a>Čtenáři na oddíl překročí limit centra událostí

Pokud syntaxe dotazu streamování odkazuje na stejný vstupní prostředek centra událostí vícekrát, modul úloh může použít více čtenářů na dotaz ze stejné skupiny spotřebitelů. Pokud existuje příliš mnoho odkazů na stejnou skupinu příjemce, úloha může překročit limit pět a vyvolat chybu. Za těchto okolností můžete dále rozdělit pomocí více vstupů napříč více skupin spotřebitelů pomocí řešení popsaného v následující části. 

Scénáře, ve kterých počet čtenářů na oddíl překročí limit Centra událostí pět patří následující:

* Více příkazů SELECT: Pokud použijete více příkazů SELECT, které odkazují na **stejný** vstup centra událostí, každý příkaz SELECT způsobí vytvoření nového příjemce.

* UNIE: Při použití UNION, je možné mít více vstupů, které odkazují na **stejné** centrum událostí a skupiny spotřebitelů.

* VLASTNÍ SPOJENÍ: Při použití operace SELF JOIN je možné odkazovat na **stejné** centrum událostí vícekrát.

Následující osvědčené postupy mohou pomoci zmírnit scénáře, ve kterých počet čtenářů na oddíl překračuje limit centra událostí pět.

### <a name="split-your-query-into-multiple-steps-by-using-a-with-clause"></a>Rozdělení dotazu do několika kroků pomocí klauzule WITH

Klauzule WITH určuje dočasnou pojmenovanou sadu výsledků, na kterou lze odkazovat klauzulí FROM v dotazu. Definujete with klauzule v oboru provádění jednoho příkazu SELECT.

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

Použijte tento dotaz:

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

### <a name="ensure-that-inputs-bind-to-different-consumer-groups"></a>Ujistěte se, že vstupy se vážou na různé skupiny spotřebitelů

Pro dotazy, ve kterých jsou tři nebo více vstupů připojeny ke stejné skupině spotřebitelů Centra událostí, vytvořte samostatné skupiny spotřebitelů. To vyžaduje vytvoření dalších vstupů Stream Analytics.

## <a name="get-help"></a>Podpora

Další pomoc našlápneme na fórum [Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další kroky

* [Úvod do Azure Stream Analytics](stream-analytics-introduction.md)
* [Začínáme používat službu Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování služby Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k jazyku Azure Stream Analytics Query Language](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční příručka k rozhraní REST API pro správu služby Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
