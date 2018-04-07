---
title: Řízené daty ladění v Azure Stream Analytics
description: Tento článek popisuje postup řešení potíží s Azure Stream Analytics úlohu pomocí úlohy diagram a metriky na portálu Azure.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: e6b2b7f4c01a5280c2fb3a5c17158b8dcc2797d4
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>Řízené daty ladění pomocí diagram úlohy

Diagram úlohy na **monitorování** okno na portálu Azure můžete vizualizovat vaše úlohy kanálu. Zobrazuje vstupy, výstupy a kroky dotazu. Diagram úlohy můžete použít k prozkoumání metriky pro každý krok, abyste rychleji izolovat zdroj problému při odstraňování potíží.

## <a name="using-the-job-diagram"></a>Pomocí úlohy diagram

Na portálu Azure při v úloze Stream Analytics, v části **podporu + Poradce při potížích s**, vyberte **úlohy diagram**:

![Diagram úlohy s metriky - umístění](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Vyberte každý dotaz kroku najdete v části odpovídající v podokně úpravy dotazu. V dolním podokně na stránce se zobrazí metriky graf kroku.

![Diagram úlohy s metriky – základní úlohy](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Pokud chcete zobrazit oddíly vstupu Azure Event Hubs, vyberte **...** Zobrazí se kontextová nabídka. Také můžete zobrazit vstupních fúzí.

![Diagram úlohy s metriky - rozbalte oddíl](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Se zobrazí metriky graf pro pouze jeden oddíl, vyberte uzel oddílu. Metriky se zobrazí v dolní části stránky.

![Diagram úlohy s metriky - další metriky](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Pokud chcete zobrazit graf metriky pro spojení, vyberte uzel fúze. Následující tabulka ukazuje, že žádné události byly zrušené nebo upravena.

![Diagram úlohy s metriky - mřížky](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Pokud chcete zobrazit podrobnosti hodnota metriky a času, přejděte na grafu.

![Úloha diagram s metriky – ukazatele](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Řešení potíží pomocí metrik

**QueryLastProcessedTime** Metrika udává, kdy konkrétní krok přijal data. Pohledem na topologii, můžete použít pracovat zpátky z výstupu procesoru zobrazíte, který krok nepřijímá data. Pokud krok není získávání dat, přejděte ke kroku dotazu před ním. Zkontrolujte, jestli má časový interval v předchozím kroku dotazu, a pokud uplynutí dostatek času pro něj na výstupní data. (Poznámka: Tento čas k hodinu jsou přichytávány systému windows.)
 
Pokud v předchozím kroku dotazu je vstupní procesoru, využít vstupní metriky odpovědí na tyto cílové otázky. Můžou pomoct určit, zda je úloha získání dat z jeho zadávání. Pokud je dotaz dělený, zkontrolujte všechny oddíly.
 
### <a name="how-much-data-is-being-read"></a>Kolik dat se čte?

*   **InputEventsSourcesTotal** je počet jednotek dat pro čtení. Například počet objektů BLOB.
*   **InputEventsTotal** je počet událostí pro čtení. Tato metrika je dostupná pro jednotlivé oddíly.
*   **InputEventsInBytesTotal** je počet přečtených bajtů.
*   **InputEventsLastArrivalTime** je aktualizováno každých přijatou událost čas zařazených do fronty.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Je čas postoupíte? Pokud se čtou skutečné události, nemusí být vydaný interpunkce.

*   **InputEventsLastPunctuationTime** udává, kdy došlo k přerušení pro zajištění dopředného běhu času. Pokud není objeví interpunkční znaménka, může zablokování datový tok.
 
### <a name="are-there-any-errors-in-the-input"></a>Dochází k chybám ve vstupu?

*   **InputEventsEventDataNullTotal** je počet událostí, které mají hodnotu null data.
*   **InputEventsSerializerErrorsTotal** je počet událostí, které nelze deserializovat správně.
*   **InputEventsDegradedTotal** je počet událostí, které se za potíže než s deserializace.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Jsou vyřazeny nebo upravena události?

*   **InputEventsEarlyTotal** je počet událostí, které mají časové razítko aplikace před horní meze.
*   **InputEventsLateTotal** je počet událostí, které mají časové razítko aplikace po horní meze.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** je číslo události vyřazen dříve, než čas spuštění úlohy.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Jsme padají při čtení dat?

*   **InputEventsSourcesBackloggedTotal** zjistíte, kolik další zprávy nutné čtení pro službu Event Hubs a Azure IoT Hub vstupy.


## <a name="get-help"></a>Podpora
Pro další pomoc, vyzkoušejte naše [fórum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Další postup
* [Úvod do služby Stream Analytics](stream-analytics-introduction.md)
* [Začínáme s Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálování úlohy Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka jazyka dotazů Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics správu odkazu k REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
