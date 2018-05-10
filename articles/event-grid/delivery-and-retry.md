---
title: Azure mřížky události doručení a zkuste to znovu
description: Popisuje, jak Azure událostí mřížky doručí události a jak zpracovává nedoručených zpráv.
services: event-grid
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.topic: article
ms.date: 05/09/2018
ms.author: tomfitz
ms.openlocfilehash: 27ad4351bdcf9d7d655b19343dcf41d334f079f9
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="event-grid-message-delivery-and-retry"></a>Doručení zpráv událostí mřížky a zkuste to znovu 

Tento článek popisuje, jak Azure událostí mřížky zpracovává události, když není potvrdí doručení.

Událost mřížky poskytuje trvanlivý doručení. Zajišťuje každou zprávu alespoň jednou pro každé předplatné. Události se posílají okamžitě registrované webhooku každého předplatného. Webhook, jehož není potvrdil přijetí události do 60 sekund od první pokus o doručení, události mřížky opakuje doručení události. 

V současné době mřížky událostí odesílá všechny události jednotlivě odběratelům. Odběratel obdrží pole s jednu událost.

## <a name="message-delivery-status"></a>Stav doručení zpráv

Událost mřížky používá kódy odpovědi HTTP potvrdit k přijetí události. 

### <a name="success-codes"></a>Kódy úspěch

Následující kódy odpovědi HTTP znamenat, že událost byla doručena úspěšně vaší webhooku. Událost mřížky zvažuje doručení dokončení.

- 200 OK
- 202 platných

### <a name="failure-codes"></a>Selhání kódy

Následující kódy odpovědi HTTP znamenat, že události doručení pokus se nezdařil. 

- 400 – Chybný požadavek
- 401 unauthorized
- 404 – Nenalezeno
- 408 časový limit požadavku.
- 414 URI příliš dlouhý
- 500 – Interní chyba serveru
- 503 – Nedostupná služba
- 504 – Časový limit brány

Pokud události mřížky obdrží chybu, která označuje, že koncový bod není k dispozici, pokusí se znovu odeslat událost. 

## <a name="retry-intervals-and-duration"></a>Opakovat intervalech a doba trvání

Událost mřížky používá zásady opakování exponenciálního omezení rychlosti pro odeslání události. Pokud vaše webhooku neodpovídá nebo vrací kód chyby, opakování mřížky události doručení podle plánu, následující:

1. 10 sekund
2. 30 sekund
3. 1 minuta
4. 5 minut
5. 10 minut
6. 30 minut
7. 1 hodina

Událost mřížky přidá malé náhodné přeskupování všechny intervalech zkuste to znovu. Po jedné hodině události doručení proběhne jednou za hodinu.

Ve výchozím nastavení události mřížky vyprší platnost všechny události, které nejsou doručeny do 24 hodin.

## <a name="next-steps"></a>Další postup

* Chcete-li zobrazit stav události doručení, najdete v části [doručení zpráv monitorování událostí mřížky](monitor-event-delivery.md).
* Úvod k mřížce událostí, naleznete v části [o mřížky událostí](overview.md).
* Chcete-li rychle začít používat událostí mřížky, přečtěte si téma [vytvořit a směrování vlastních událostí s Azure událostí mřížky](custom-event-quickstart.md).