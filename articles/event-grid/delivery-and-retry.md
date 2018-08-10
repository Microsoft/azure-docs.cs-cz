---
title: Azure Event Grid doručování a opakované pokusy
description: Popisuje, jak Azure Event Grid zajišťují událostí a jak zpracovává nedoručených zpráv.
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: conceptual
ms.date: 08/08/2018
ms.author: tomfitz
ms.openlocfilehash: b34386a7b416d6f7d8b008a9cb5ef142948a370f
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005391"
---
# <a name="event-grid-message-delivery-and-retry"></a>Doručování zpráv Event Grid a zkuste to znovu 

Tento článek popisuje, jak služby Azure Event Grid zpracovává události při doručení není potvrzeny.

Event gridu poskytuje odolné doručování. Poskytuje každou zprávu alespoň jednou pro každé předplatné. Události se posílají okamžitě registrované webhooku každé předplatné. Pokud webhooku není obdržel událost během 60 sekund první pokusy o doručení, služby Event Grid zopakuje pokus o doručení události. 

V současné době služby Event Grid odesílá každé události jednotlivě odběratelům. Odběratel přijímá pole obsahující jednu událost.

## <a name="message-delivery-status"></a>Stav doručování zpráv

Event Grid používá kódy odpovědí protokolu HTTP na vědomí přijetí události. 

### <a name="success-codes"></a>Úspěch kódy

Následující kódy odpovědi HTTP označuje, že událost má úspěšně doručit do vašeho webhooku. Event Grid považovat doručování za dokončený.

- 200 OK
- 202 přijato

### <a name="failure-codes"></a>Kód selhání

Následující kódy odpovědi HTTP označuje, že pokus o doručení událostí se nezdařilo. 

- 400 – Chybný požadavek
- 401 Neautorizováno
- 404 – Nenalezeno
- 408 časový limit žádosti
- Identifikátor URI 414 příliš dlouhý
- 429 příliš mnoho požadavků
- 500 – Interní chyba serveru
- 503 – Nedostupná služba
- 504 – Časový limit brány

Pokud služby Event Grid přijme chybu, která určuje koncový bod je dočasně nedostupná nebo budoucí žádosti může proběhnout úspěšně, se pokusí znovu k odeslání události. Pokud služby Event Grid se zobrazí chyba, která určuje nikdy uspějí doručování a [nakonfigurovaný koncový bod onta nedoručených zpráv](manage-event-delivery.md), odesílá události do koncového bodu onta nedoručených zpráv. 

## <a name="retry-intervals-and-duration"></a>Intervaly opakování a dobu trvání

Event Grid používá zásady opakování exponenciálního omezení rychlosti pro doručování událostí. Pokud vaše webhooku nereaguje nebo vrací kód chyby, služby Event Grid se opakuje doručování následujícího plánu:

1. 10 sekund
2. 30 sekund
3. 1 minuta
4. 5 minut
5. 10 minut
6. 30 minut
7. 1 hodina

Event Grid přidá malé náhodné ke všem intervalům opakování. Doručování událostí po jedné hodině, proběhne jednou za hodinu.

Ve výchozím nastavení služby Event Grid vyprší platnost všech událostí, které nejsou doručeny do 24 hodin. Je možné [přizpůsobit zásady opakování](manage-event-delivery.md) při vytváření odběru událostí. Zadejte maximální počet pokusů o doručení (výchozí hodnota je 30) a události time to live (výchozí hodnota je 1440 minut).

## <a name="dead-letter-events"></a>Události onta nedoručených zpráv

Když Event Grid doručit událost, kterou může odesílat nedoručené událostí do účtu úložiště. Tento proces se označuje jako dead-lettering. Zobrazit nedoručené události, můžete tyto změny z umístění onta nedoručených zpráv. Další informace najdete v tématu [Dead písmeno a zásady opakování](manage-event-delivery.md).

## <a name="next-steps"></a>Další postup

* Chcete-li zobrazit stav doručení událostí, naleznete v tématu [doručování zpráv služby Event Grid monitorování](monitor-event-delivery.md).
* Přizpůsobit možnosti doručování událostí, přečtěte si článek [nastavení doručení spravovat služby Event Grid](manage-event-delivery.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Pokud chcete rychle začít používat služby Event Grid, přečtěte si téma [vytvoření a směrování vlastních událostí pomocí služby Azure Event Grid](custom-event-quickstart.md).