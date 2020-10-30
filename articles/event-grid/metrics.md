---
title: Metriky podporované aplikací Azure Event Grid
description: Tento článek poskytuje Azure Monitor metriky, které služba Azure Event Grid podporuje.
ms.topic: conceptual
ms.date: 08/13/2020
ms.openlocfilehash: 87c91077c8eeca2134da53774979c212a82e3b7d
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93042134"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Metriky podporované aplikací Azure Event Grid
Tento článek obsahuje seznam Event Gridch metrik, které jsou zařazené do kategorií podle oborů názvů. 

## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/domény

|Metrika|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|No|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName, chyba, ErrorType|
|DeliverySuccessCount|Yes|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|Téma, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|No|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|Téma, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Yes|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Yes|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Yes|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|Téma, ErrorType, chyba|
|PublishSuccessCount|Yes|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Téma|
|PublishSuccessLatencyInMs|Yes|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádné dimenze|
| AdvancedFilterEvaluationCount | Yes | Vyhodnocení pokročilých filtrů | Počet | Celkem | Celkové Rozšířené filtry vyhodnocené napříč odběry událostí | EventSubscriptionName |



## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Metrika|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|DeadLetterReason|
|DeliveryAttemptFailCount|No|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Chyba, ErrorType|
|DeliverySuccessCount|Yes|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|Žádné dimenze|
|DestinationProcessingDurationInMs|No|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|Žádné dimenze|
|DroppedEventCount|Yes|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|DropReason|
|MatchedEventCount|Yes|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|Žádné dimenze|


## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metrika|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|PublishFailCount|Yes|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|PublishSuccessCount|Yes|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Žádné dimenze|
|PublishSuccessLatencyInMs|Yes|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádné dimenze|
|UnmatchedEventCount|Yes|Nespárované události|Počet|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádné dimenze|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Metrika|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Chyba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|EventSubscriptionName|
|DroppedEventCount|Yes|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|EventSubscriptionName|
|PublishFailCount|Yes|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|PublishSuccessCount|Yes|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Žádné dimenze|
|PublishSuccessLatencyInMs|Yes|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádné dimenze|
|UnmatchedEventCount|Yes|Nespárované události|Počet|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádné dimenze|
| AdvancedFilterEvaluationCount | Yes | Vyhodnocení pokročilých filtrů | Počet | Celkem | Celkové Rozšířené filtry vyhodnocené napříč odběry událostí | EventSubscriptionName |



## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témata

|Metrika|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Chyba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|EventSubscriptionName|
|DroppedEventCount|Yes|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|EventSubscriptionName|
|PublishFailCount|Yes|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|PublishSuccessCount|Yes|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Žádné dimenze|
|PublishSuccessLatencyInMs|Yes|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádné dimenze|
|UnmatchedEventCount|Yes|Nespárované události|Počet|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádné dimenze|
| AdvancedFilterEvaluationCount | Yes | Vyhodnocení pokročilých filtrů | Počet | Celkem | Celkové Rozšířené filtry vyhodnocené napříč odběry událostí | Téma, EventSubscriptionName, DomainEventSubscriptionName |

## <a name="next-steps"></a>Další kroky
Přečtěte si následující článek: [diagnostické protokoly](diagnostic-logs.md)
