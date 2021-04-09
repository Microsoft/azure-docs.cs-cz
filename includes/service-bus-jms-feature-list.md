---
title: zahrnout soubor
description: zahrnout soubor
services: service-bus-messaging
author: axisc
ms.service: service-bus-messaging
ms.topic: include
ms.date: 6/9/2020
ms.author: aschhab
ms.custom: include file
ms.openlocfilehash: 574507fcc6a3c05919c441bd6d0ec9c573d4b6ae
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "100652597"
---
V následující tabulce jsou uvedené funkce JMS (Java Message Service), které Azure Service Bus aktuálně podporuje. Zobrazuje také funkce, které nejsou podporovány.


| Funkce | Rozhraní API |Status |
|---|---|---|
| Fronty   | <ul> <li> JMSContext. createQueue (řetězec ve frontě) </li> </ul>| **Podporováno** |
| Témata   | <ul> <li> JMSContext. createTopic (řetězec v tématu String) </li> </ul>| **Podporováno** |
| Dočasné fronty |<ul> <li> JMSContext.createTemporaryQueue() </li> </ul>| **Podporováno** |
| Dočasná témata |<ul> <li> JMSContext.createTemporaryTopic() </li> </ul>| **Podporováno** |
| Producent zprávy/<br/> JMSProducer |<ul> <li> JMSContext.createProducer() </li> </ul>| **Podporováno** |
| Prohlížeče front |<ul> <li> JMSContext. createBrowser (fronta fronty) </li> <li> JMSContext. createBrowser (Queue Queue; String messageSelector) </li> </ul> | **Podporováno** |
| Příjemce zprávy/ <br/> JMSConsumer | <ul> <li> JMSContext. createConsumer (cílový cíl) </li> <li> JMSContext. createConsumer (cílový cíl, String messageSelector) </li> <li> JMSContext. createConsumer (cílový cíl; String messageSelector; Boolean Local)</li> </ul>  <br/> Místní není momentálně podporovaná. | **Podporováno** |
| Sdílená trvalá předplatná | <ul> <li> JMSContext. createSharedDurableConsumer (téma, název řetězce) </li> <li> JMSContext. createSharedDurableConsumer (téma, název řetězce, řetězec messageSelector) </li> </ul>| **Podporováno**|
| Nesdílené trvalé odběry | <ul> <li> JMSContext. createDurableConsumer (téma, název řetězce) </li> <li> createDurableConsumer (téma téma; název řetězce; String messageSelector; Boolean Local) </li> </ul> <br/> Lokální není aktuálně podporován a měl by být nastaven na hodnotu false. | **Podporováno** |
| Sdílená netrvalá předplatná |<ul> <li> JMSContext. createSharedConsumer (téma téma; String sharedSubscriptionName) </li> <li> JMSContext. createSharedConsumer (téma téma; String sharedSubscriptionName; String messageSelector) </li> </ul> | **Podporováno** |
| Nesdílené odběry, které nejsou trvalé |<ul> <li> JMSContext. createConsumer (cílový cíl) </li> <li> JMSContext. createConsumer (cílový cíl, String messageSelector) </li> <li> JMSContext. createConsumer (cílový cíl; String messageSelector; Boolean Local) </li> </ul> <br/> Lokální není aktuálně podporován a měl by být nastaven na hodnotu false. | **Podporováno** |
| Selektory zpráv | závisí na vytvořeném spotřebiteli. | **Podporováno** |
| Zpoždění doručení (naplánované zprávy) | <ul> <li> JMSProducer. setDeliveryDelay (Long deliveryDelay) </li> </ul>|**Podporováno**|
| Zpráva vytvořena |<ul> <li> JMSContext.createMessage() </li> <li> JMSContext.createBytesMessage() </li> <li> JMSContext.createMapMessage() </li> <li> JMSContext. createObjectMessage (serializovatelný objekt) </li> <li> JMSContext.createStreamMessage() </li> <li> JMSContext.createTextMessage() </li> <li> JMSContext. createTextMessage (text řetězce) </li> </ul>| **Podporováno** |
| Transakce mezi entitami |<ul> <li> Connection. createSession (true, Session.SESSION_TRANSACTED) </li> </ul> | **Podporováno** |
| Distribuované transakce || Nepodporováno |
