---
title: Ladění řízené daty v Azure Stream Analytics
description: Tento článek popisuje, jak řešit úlohu Azure Stream Analytics pomocí diagramu úloh a metrik v Azure Portal.
author: jseb225
ms.author: jeanb
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/01/2017
ms.openlocfilehash: 6d20454515088ccca87665d9b3b27c0d82c3cdf9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98020396"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Ladění založené na datech s využitím diagramu úloh

Diagram úlohy v okně **sledování** v Azure Portal vám může pomáhat s vizualizací kanálu úloh. Zobrazuje vstupy, výstupy a kroky dotazu. Diagram úloh můžete použít ke zkoumání metrik pro jednotlivé kroky, abyste při řešení potíží mohli rychleji izolovat zdroj problému.

## <a name="using-the-job-diagram"></a>Používání diagramu úloh

V Azure Portal v rámci úlohy Stream Analytics v části **Podpora a řešení potíží** vyberte **diagram úlohy**:

![Diagram úloh s metrikami – umístění](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Výběrem jednotlivých kroků dotazu zobrazíte odpovídající oddíl v podokně pro úpravu dotazu. Graf metriky pro krok se zobrazí v dolním podokně na stránce.

![Diagram úlohy se metrikami – základní úloha](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Pokud chcete zobrazit oddíly vstupu v Azure Event Hubs, vyberte **...** Zobrazí se místní nabídka. Můžete také zobrazit sloučení vstupu.

![Diagram úlohy s metrikami – Rozbalit oddíl](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Pokud chcete zobrazit graf metriky jenom pro jeden oddíl, vyberte uzel oddílu. Metriky se zobrazují v dolní části stránky.

![Diagram úloh s metrikami – další metriky](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Chcete-li zobrazit graf metrik pro fúze, vyberte uzel fúze. Následující graf ukazuje, že nebyly vyřazeny nebo upraveny žádné události.

![Diagram úlohy s metrikami – mřížka](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Chcete-li zobrazit podrobnosti o hodnotě metriky a času, nastavte ukazatel na graf.

![Diagram úlohy s metrikami – najeďte myší](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Řešení potíží pomocí metrik

Metrika **QueryLastProcessedTime** označuje, kdy určitý krok přijal data. Pohledem na topologii můžete sledovat, který krok nepřijímá data, směrem zpět od výstupního procesoru. Pokud některý z kroků nezískává data, přečtěte si krok dotazu těsně před ním. Zkontroluje, jestli předchozí krok dotazu obsahuje časový interval, a pokud pro něj uplynul dostatek času pro výstup dat. (Všimněte si, že časová okna jsou přichycena k hodině.)
 
Pokud je předchozí krok dotazu vstupní procesor, použijte vstupní metriky, které vám pomůžou zodpovědět následující cílené otázky. Můžou vám pomůžou určit, jestli úloha získává data ze svých vstupních zdrojů. Pokud je dotaz dělený, zkontrolujte všechny oddíly.
 
### <a name="how-much-data-is-being-read"></a>Kolik dat se čte?

*   **InputEventsSourcesTotal** je počet čtených datových jednotek. Například počet objektů BLOB.
*   **InputEventsTotal** je počet přečtených událostí. Tato metrika je dostupná pro jednotlivé oddíly.
*   **InputEventsInBytesTotal** je počet čtených bajtů.
*   **InputEventsLastArrivalTime** se aktualizuje s každým přijatým časem události ve frontě.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Je čas na přechod? V případě načtení skutečných událostí nemuselo dojít k přerušení.

*   **InputEventsLastPunctuationTime** udává, kdy došlo k přerušení pro zajištění dopředného běhu času. Pokud se interpunkce nevydá, tok dat se může zablokovat.
 
### <a name="are-there-any-errors-in-the-input"></a>Existují ve vstupu nějaké chyby?

*   **InputEventsEventDataNullTotal** je počet událostí, které mají data s hodnotou null.
*   **InputEventsSerializerErrorsTotal** je počet událostí, které nebylo možné správně deserializovat.
*   **InputEventsDegradedTotal** je počet událostí, u kterých došlo k potížím, než s deserializací.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Jsou události vyhozeny nebo upravovány?

*   **InputEventsEarlyTotal** je počet událostí, které mají časové razítko aplikace před horní mezí.
*   **InputEventsLateTotal** je počet událostí, které mají časové razítko aplikace za horním limitem.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** je počet událostí vynechaných před časem spuštění úlohy.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Zavedli jsme za čtení dat?

*   **Nevyřízené události vstupu (celkem)** oznamuje, kolik dalších zpráv je potřeba přečíst pro Event Hubs a Azure IoT Hub vstupy. Pokud je toto číslo větší než 0, znamená to, že vaše úloha nemůže zpracovat data jako rychlou, jak je připravujeme v. V takovém případě možná budete muset zvýšit počet jednotek streamování a/nebo zajistit, aby se vaše úloha mohla paralelně rozrůstat. Další informace najdete na  [stránce věnované paralelnímu dotazování](./stream-analytics-parallelization.md). 


## <a name="get-help"></a>Získání pomoci
Pokud potřebujete další pomoc, vyzkoušejte si naši [stránku Microsoft Q&Azure Stream Analytics](/answers/topics/azure-stream-analytics.html). 

## <a name="next-steps"></a>Další kroky
* [Úvod do Stream Analytics](stream-analytics-introduction.md)
* [Začínáme se Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování úloh Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční dokumentace jazyka Stream Analytics dotazů](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referenční informace o REST API správy Stream Analytics](/rest/api/streamanalytics/)