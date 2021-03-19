---
title: Metriky podporované aplikací Azure Event Grid
description: Tento článek poskytuje Azure Monitor metriky, které služba Azure Event Grid podporuje.
ms.topic: conceptual
ms.date: 03/17/2021
ms.openlocfilehash: 321e318f9dab87fde20b33a6a3a906b020ada622
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104588728"
---
# <a name="metrics-supported-by-azure-event-grid"></a>Metriky podporované aplikací Azure Event Grid
Tento článek obsahuje seznam Event Gridch metrik, které jsou zařazené do kategorií podle oborů názvů. 

## <a name="system-topics"></a>Systémová témata

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Vyhodnocení pokročilých filtrů|Počet|Celkem|Celkové Rozšířené filtry vyhodnocené napříč odběry událostí pro toto téma.|EventSubscriptionName|
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


## <a name="custom-topics"></a>Vlastní témata

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Vyhodnocení pokročilých filtrů|Počet|Celkem|Celkové Rozšířené filtry vyhodnocené napříč odběry událostí pro toto téma.|EventSubscriptionName|
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

## <a name="domains"></a>Domény

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Vyhodnocení pokročilých filtrů|Počet|Celkem|Celkové Rozšířené filtry vyhodnocené napříč odběry událostí pro toto téma.|Téma, EventSubscriptionName, DomainEventSubscriptionName|
|DeadLetteredCount|Yes|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|No|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName, chyba, ErrorType|
|DeliverySuccessCount|Yes|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|Téma, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|No|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|Téma, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Yes|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Yes|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|Téma, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Yes|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|Téma, ErrorType, chyba|
|PublishSuccessCount|Yes|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Téma|
|PublishSuccessLatencyInMs|Yes|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádné dimenze|

## <a name="event-subscriptions"></a>Odběry událostí

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|DeadLetterReason|
|DeliveryAttemptFailCount|No|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Chyba, ErrorType|
|DeliverySuccessCount|Yes|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|Žádné dimenze|
|DestinationProcessingDurationInMs|No|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|Žádné dimenze|
|DroppedEventCount|Yes|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|DropReason|
|MatchedEventCount|Yes|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|Žádné dimenze|


## <a name="extension-topics"></a>Témata rozšíření

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|PublishFailCount|Yes|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|PublishSuccessCount|Yes|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Žádné dimenze|
|PublishSuccessLatencyInMs|Yes|Latence úspěšného publikování|Milisekund|Celkem|Latence úspěšného publikování v milisekundách|Žádné dimenze|
|UnmatchedEventCount|Yes|Nespárované události|Počet|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádné dimenze|

## <a name="partner-namespaces"></a>Obory názvů partnerů

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
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


## <a name="partner-topics"></a>Témata pro partnery

|Metric|Exportovatelné přes nastavení diagnostiky?|Zobrazovaný název metriky|Jednotka|Typ agregace|Description|Dimenze|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Vyhodnocení pokročilých filtrů|Počet|Celkem|Celkové Rozšířené filtry vyhodnocené napříč odběry událostí pro toto téma.|EventSubscriptionName|
|DeadLetteredCount|Yes|Nedoručené události s písmeny|Počet|Celkem|Celkový počet nedoručených událostí, které odpovídají tomuto odběru události|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Doručení – neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných doručování do tohoto odběru události|Chyba, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Doručené události|Počet|Celkem|Celkový počet událostí doručených do tohoto předplatného události|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Doba trvání zpracování cíle|Milisekund|Průměr|Doba zpracování cíle v milisekundách|EventSubscriptionName|
|DroppedEventCount|Yes|Vyřazené události|Počet|Celkem|Celkový počet vyřazených událostí, které odpovídají tomuto odběru události|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Spárované události|Počet|Celkem|Celkový počet událostí odpovídajících tomuto odběru události|EventSubscriptionName|
|PublishFailCount|Yes|Publikovat neúspěšné události|Počet|Celkem|Celkový počet událostí neúspěšných publikování do tohoto tématu|ErrorType, chyba|
|PublishSuccessCount|Yes|Publikované události|Počet|Celkem|Celkový počet událostí publikovaných na toto téma|Žádné dimenze|
|UnmatchedEventCount|Yes|Nespárované události|Počet|Celkem|Celkový počet událostí nevyhovujících žádnému z odběrů událostí pro toto téma|Žádné dimenze|


## <a name="next-steps"></a>Další kroky
Přečtěte si následující článek: [diagnostické protokoly](diagnostic-logs.md)
