---
title: Ladění založené na datech ve Službě Azure Stream Analytics
description: Tento článek popisuje, jak řešit potíže s úlohou Azure Stream Analytics pomocí diagramu úloh a metrik na webu Azure Portal.
author: jseb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 3c0c29e1793e56efae8d13cb01d57faf257d8805
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75426080"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Ladění založené na datech s využitím diagramu úloh

Diagram úloh na okně **Monitorování** na portálu Azure vám může pomoct vizualizovat váš kanál úloh. Zobrazuje vstupy, výstupy a kroky dotazu. Diagram úloh můžete použít ke zkoumání metrik pro jednotlivé kroky, abyste při řešení potíží mohli rychleji izolovat zdroj problému.

## <a name="using-the-job-diagram"></a>Použití diagramu úlohy

Na webu Azure Portal v části Stream Analytics vyberte v části **PODPORA + PORADCE PŘI POTÍŽÍCH**diagram **úloh**:

![Diagram práce s metrikami - umístění](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Výběrem každého kroku dotazu zobrazíte odpovídající oddíl v podokně úprav dotazu. V dolním podokně na stránce se zobrazí metrický graf kroku.

![Diagram práce s metrikami - základní úloha](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Oddíly vstupu centra událostí Azure zobrazíte **tak,** že vyberete . . . Zobrazí se místní nabídka. Můžete také zobrazit vstupní fúzi.

![Diagram úloh s metrikami – rozbalte oddíl](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Chcete-li zobrazit graf metriky pouze pro jeden oddíl, vyberte uzel oddílu. Metriky jsou zobrazeny v dolní části stránky.

![Diagram úloh s metrikami – více metrik](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Chcete-li zobrazit graf metrik pro fúzi, vyberte uzel fúze. Následující graf ukazuje, že žádné události byly vynechány nebo upraveny.

![Diagram úloh s metrikami - mřížka](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Chcete-li zobrazit podrobnosti o hodnotě metriky a čase, přejděte na graf.

![Diagram úloh s metrikami – najetí přes](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Poradce při potížích s použitím metrik

Metrika **QueryLastProcessedTime** označuje, kdy přijatý krok přijal data. Při pohledu na topologii můžete pracovat zpět od výstupního procesoru a zjistit, který krok nepřijímá data. Pokud krok nezískává data, přejděte na krok dotazu těsně před ním. Zkontrolujte, zda předchozí krok dotazu obsahuje časové okno a zda uplynul dostatek času pro výstup dat. (Všimněte si, že časová okna jsou přichycena k hodině.)
 
Pokud je předchozím krokem dotazu vstupní procesor, použijte vstupní metriky, které vám pomohou odpovědět na následující cílené otázky. Mohou vám pomoci určit, zda úloha získává data ze vstupních zdrojů. Pokud je dotaz dělený, zkontrolujte všechny oddíly.
 
### <a name="how-much-data-is-being-read"></a>Kolik dat se čte?

*   **InputEventsSourcesTotal** je počet přečtených datových jednotek. Například počet objektů BLOB.
*   **InputEventsTotal** je počet přečtených událostí. Tato metrika je dostupná pro jednotlivé oddíly.
*   **InputEventsInBytesTotal** je počet přečtených bajtů.
*   **InputEventsLastArrivalTime** je aktualizován s každou přijatou událost je zařazen čas.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Pohybuje se čas kupředu? V případě načtení skutečných událostí nemuselo dojít k přerušení.

*   **InputEventsLastPunctuationTime** udává, kdy došlo k přerušení pro zajištění dopředného běhu času. Pokud interpunkce není vydána, tok dat může získat blokovány.
 
### <a name="are-there-any-errors-in-the-input"></a>Existují nějaké chyby ve vstupu?

*   **InputEventsEventsDataDataNullTotal** je počet událostí, které mají nulová data.
*   **InputEventsSerializerErrorsTotal** je počet událostí, které nelze správně rekonstruovat.
*   **InputEventsDegradedTotal** je počet událostí, které měly problém než s rekonstrukcí.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Jsou události vynechány nebo upraveny?

*   **InputEventsEarlyTotal** je počet událostí, které mají časové razítko aplikace před vysokým vodoznakem.
*   **InputEventsLateTotal** je počet událostí, které mají časové razítko aplikace za vysokým vodoznakem.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** je počet událostí vyřazených před časem zahájení úlohy.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Zaostáváme ve čtení dat?

*   **Funkce Vstupní události backlogged (Celkem)** vám řekne, kolik dalších zpráv je potřeba číst pro centra událostí a vstupy Azure IoT Hub. Pokud je toto číslo větší než 0, znamená to, že vaše úloha nemůže zpracovat data tak rychle, jak přichází. V takovém případě může být nutné zvýšit počet jednotek streamování a/nebo se ujistit, že vaše úloha může být paralelizována. Další informace o tomto tématu naleznete na [stránce paralelizace dotazu](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). 


## <a name="get-help"></a>Podpora
Další pomoc našlápnej na našem [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Další kroky
* [Úvod do streamové analýzy](stream-analytics-introduction.md)
* [Začínáme se Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování úloh Stream Analytics](stream-analytics-scale-jobs.md)
* [Odkaz na dotaz ového jazyka Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Odkaz na rozhraní REST API správy Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
