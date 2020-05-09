---
title: Metriky podporované aplikací Azure Event Grid
description: Tento článek poskytuje Azure Monitor metriky, které služba Azure Event Grid podporuje.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: spelluru
ms.openlocfilehash: 643df2f4cc6347e0fd56f9124b68f1888ab85e26
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2020
ms.locfileid: "82630132"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Metriky podporované aplikací Azure Event Grid
Tento článek obsahuje seznam Event Gridch metrik, které jsou zařazené do kategorií podle oborů názvů. 

## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/domény

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Téma|
|PublishFailCount|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|Téma, ErrorType, chyba|
|PublishSuccessLatencyInMs|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádné|
|MatchedEventCount|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName, chyba, ErrorType|
|DeliverySuccessCount|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|Téma, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|Téma, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/témata

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Žádné|
|PublishFailCount|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|UnmatchedEventCount|Nespárované události|Počet|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádné|
|PublishSuccessLatencyInMs|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádné|
|MatchedEventCount|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|EventSubscriptionName|
|DeliveryAttemptFailCount|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Chyba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|EventSubscriptionName|
|DestinationProcessingDurationInMs|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|EventSubscriptionName|
|DroppedEventCount|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|DropReason, EventSubscriptionName|
|DeadLetteredCount|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|DeadLetterReason, EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Žádné|
|PublishFailCount|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|UnmatchedEventCount|Nespárované události|Počet|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádné|
|PublishSuccessLatencyInMs|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádné|
|MatchedEventCount|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|EventSubscriptionName|
|DeliveryAttemptFailCount|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Chyba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|EventSubscriptionName|
|DestinationProcessingDurationInMs|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|EventSubscriptionName|
|DroppedEventCount|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|DropReason, EventSubscriptionName|
|DeadLetteredCount|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|DeadLetterReason, EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|MatchedEventCount|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|Žádné|
|DeliveryAttemptFailCount|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Chyba, ErrorType|
|DeliverySuccessCount|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|Žádné|
|DestinationProcessingDurationInMs|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|Žádné|
|DroppedEventCount|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|DropReason|
|DeadLetteredCount|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metrika|Zobrazovaný název metriky|Jednotka|Typ agregace|Popis|Dimenze|
|---|---|---|---|---|---|
|PublishSuccessCount|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Žádné|
|PublishFailCount|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|UnmatchedEventCount|Nespárované události|Počet|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádné|
|PublishSuccessLatencyInMs|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádné|

## <a name="next-steps"></a>Další kroky
Přečtěte si následující článek: [diagnostické protokoly](diagnostic-logs.md)
