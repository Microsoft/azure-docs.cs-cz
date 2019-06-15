---
title: S daty ladění ve službě Azure Stream Analytics
description: Tento článek popisuje postup řešení potíží s vaší úlohy Azure Stream Analytics s využitím metriky a diagram úloh na webu Azure Portal.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/01/2017
ms.openlocfilehash: 4a6d359b27b9a2e52d71ed5f8547041645147605
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "61479914"
---
# <a name="data-driven-debugging-by-using-the-job-diagram"></a>S daty ladění s použitím diagram úloh

Diagram úloh na **monitorování** okna na webu Azure Portal můžete vizualizovat váš kanál úlohy. Zobrazuje vstupy, výstupy a kroky dotazu. Diagram úloh můžete použít ke zkoumání metrik pro jednotlivé kroky, abyste při řešení potíží mohli rychleji izolovat zdroj problému.

## <a name="using-the-job-diagram"></a>Pomocí diagram úloh

Na webu Azure Portal zatímco v rámci úlohy Stream Analytics v části **podpora a řešení potíží**vyberte **diagram úloh**:

![Diagram úloh s metrikami – umístění](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-1.png)

Vyberte každému kroku dotazu zobrazíte odpovídající oddíl v dotazu podokna úprav. V dolním podokně na stránce se zobrazí grafu metrik pro krok.

![Diagram úloh s metrikami – základní úlohy](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-2.png)

Pokud chcete zobrazit oddíly vstup Azure Event Hubs, vyberte **...** Zobrazí se kontextová nabídka. Taky uvidíte vstupní spojení.

![Diagram úloh s metrikami - Rozbalit oddíl](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-3.png)

Pokud chcete zobrazit grafu metrik pro pouze jeden oddíl, vyberte uzel oddílu. Metriky se zobrazí v dolní části stránky.

![Diagram úloh s metrikami – další metriky](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-4.png)

Zobrazit graf metrik pro spojení, vyberte uzel spojení. Následující graf ukazuje, že nebyly žádné události vynechané nebo upravené.

![Diagram úloh s metrikami - mřížky](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-5.png)

Můžete zobrazit podrobnosti o hodnota metriky a čas, přejděte do grafu.

![Úlohy diagramu s metrikami – při najetí myší](./media/stream-analytics-job-diagram-with-metrics/stream-analytics-job-diagram-with-metrics-portal-6.png)

## <a name="troubleshoot-by-using-metrics"></a>Řešení potíží pomocí metrik

**QueryLastProcessedTime** Metrika udává, kdy konkrétní krok obdržel data. Podle topologie, můžete pracovat zpětně od procesoru výstupu zobrazíte, který krok nedostává data. Pokud nějaký krok nedostává data, přejděte ke kroku dotazu před ní. Zkontrolujte, jestli předchozího kroku dotazu odpovídá časový interval, a pokud je na výstupní data pro něj uplynul dostatek času. (Poznámka: Tento čas windows jsou přichycená k hodinám.)
 
Pokud je zpracování vstupu předchozím kroku dotazu, použijte vstupní metriky, které vám pomohou zodpovědět následující cílené dotazy. Mohou pomoci určit, zda je úloha získání dat z příslušných zdrojů. Pokud je dotaz dělený, zkontrolujte všechny oddíly.
 
### <a name="how-much-data-is-being-read"></a>Kolik dat se čte?

*   **InputEventsSourcesTotal** se počet jednotek data číst. Například počet objektů BLOB.
*   **InputEventsTotal** je počet načtených událostí. Tato metrika je dostupná pro jednotlivé oddíly.
*   **InputEventsInBytesTotal** je počet přečtených bajtů.
*   **InputEventsLastArrivalTime** je aktualizován každé přijaté události čas zařazení do fronty.
 
### <a name="is-time-moving-forward-if-actual-events-are-read-punctuation-might-not-be-issued"></a>Posouvá se čas dopředu? Pokud načtení skutečných událostí nemuselo dojít.

*   **InputEventsLastPunctuationTime** udává, kdy došlo k přerušení pro zajištění dopředného běhu času. Pokud interpunkce není vystaven, můžete získat tok dat zablokovat.
 
### <a name="are-there-any-errors-in-the-input"></a>Jsou ve vstupu nějaké chyby?

*   **InputEventsEventDataNullTotal** je počet událostí s nulovými daty.
*   **InputEventsSerializerErrorsTotal** je počet událostí, které nebylo možné správně deserializovat.
*   **InputEventsDegradedTotal** je počet událostí, které měly jiné potíže než s deserializací.
 
### <a name="are-events-being-dropped-or-adjusted"></a>Jsou právě vynechané nebo upravené události?

*   **InputEventsEarlyTotal** je počet událostí, které mají razítkem aplikace před dosažení horní meze.
*   **InputEventsLateTotal** je počet událostí, které mají razítkem aplikace po dosažení horní meze.
*   **InputEventsDroppedBeforeApplicationStartTimeTotal** je počet událostí odstraněna dříve, než čas spuštění úlohy.
 
### <a name="are-we-falling-behind-in-reading-data"></a>Jsme padají při čtení dat?

*   **Vstupní události v Backlogu (celkem)** zjistíte, kolik dalších zpráv je potřeba načíst pro vstupy služby Event Hubs a Azure IoT Hub. Když tento počet je větší než 0, znamená to, že úlohy nelze zpracovat data tak rychle, jak hned. V tomto případě budete muset zvýšit počet jednotek streamování a ujistěte se, že vaše úloha může být paralelizována. Zobrazí se další informace o to na [stránku paralelizace dotazů](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization). 


## <a name="get-help"></a>Podpora
Další pomoc, vyzkoušejte naše [fóru Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics). 

## <a name="next-steps"></a>Další postup
* [Úvod do služby Stream Analytics](stream-analytics-introduction.md)
* [Začínáme se Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Škálovat úlohy Stream Analytics](stream-analytics-scale-jobs.md)
* [Referenční příručka k Stream Analytics query language](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Stream Analytics správu reference k rozhraní REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
