---
title: Azure Event Grid doručování a opakované pokusy
description: Popisuje, jak Azure Event Grid zajišťují událostí a jak zpracovává nedoručených zpráv.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: spelluru
ms.openlocfilehash: 0945b06f78ac34500f0b16a4a419cff12d1a4734
ms.sourcegitcommit: af31deded9b5836057e29b688b994b6c2890aa79
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67812910"
---
# <a name="event-grid-message-delivery-and-retry"></a>Doručování zpráv Event Grid a zkuste to znovu

Tento článek popisuje, jak služby Azure Event Grid zpracovává události při doručení není potvrzeny.

Event gridu poskytuje odolné doručování. Poskytuje každou zprávu alespoň jednou pro každé předplatné. Události se posílají okamžitě registrované koncový bod každé předplatné. Pokud koncový bod nebude obdržel událost, služby Event Grid zopakuje pokus o doručení událostí.

V současné době služby Event Grid odesílá každé události jednotlivě odběratelům. Odběratel přijímá pole obsahující jednu událost.

## <a name="retry-schedule-and-duration"></a>Plán opakování a dobu trvání

Event Grid počká 30 sekund pro odpověď po doručení zprávy. Po 30 sekund Pokud ještě koncový bod odpověděl, zprávy do fronty pro opakování. Event Grid používá zásady opakování exponenciálního omezení rychlosti pro doručování událostí. Event Grid opakování doručování na jak kapacita systému dovolí podle následujícího plánu:

- 10 sekund
- 30 sekund
- 1 minuta
- 5 minut
- 10 minut
- 30 minut
- 1 hodina
- Každou hodinu po dobu až 24 hodin

Pokud koncový bod odpoví během 3 minut, služby Event Grid se pokusí o odebrání události z fronty opakování na jak kapacita systému dovolí, ale duplicitní hodnoty může i nadále přijímat.

Služby Event Grid přidá všechny kroky opakovat malé náhodné a může tj přeskočte určité opakovaných pokusů, pokud koncový bod je konzistentně není v pořádku, mimo provoz delší dobu, nebo se zdá být předešla zahlcení.

Deterministické chování, nastavte události time to live a maximální počet doručení pokusů [zásady opakování předplatné](manage-event-delivery.md).

Ve výchozím nastavení služby Event Grid vyprší platnost všech událostí, které nejsou doručeny do 24 hodin. Je možné [přizpůsobit zásady opakování](manage-event-delivery.md) při vytváření odběru událostí. Zadejte maximální počet pokusů o doručení (výchozí hodnota je 30) a události time to live (výchozí hodnota je 1440 minut).

## <a name="delayed-delivery"></a>Opožděné dodání

Jako koncový bod dojde k selhání doručování, začne zpoždění doručování a opakované pokusy událostí do tohoto koncového bodu služby Event Grid. Například pokud selže prvních deseti událostí, které publikuje do koncového bodu služby Event Grid bude předpokládají, že koncový bod se setkává s problémy a bude zpozdit všechny následné pokusy *a nové* doručení nechystáte nějakou dobu – v některých případech až několik hodin .

Opožděné dodání slouží funkční pro ochranu koncových bodů není v pořádku, jakož i systém Event Grid. Bez regresní a zpoždění doručování do koncových bodů není v pořádku zásady opakování služby Event Grid a možnosti svazku může snadno zaplnit celou systému.

## <a name="dead-letter-events"></a>Události onta nedoručených zpráv

Když Event Grid doručit událost, kterou může odesílat nedoručené událostí do účtu úložiště. Tento proces se označuje jako dead-lettering. Ve výchozím nastavení služby Event Grid nebude zapnout dead-lettering. Ho Pokud chcete povolit, musíte zadat účet úložiště pro uložení nedoručené události při vytváření odběru událostí. O přijetí změn události z tohoto účtu úložiště, chcete-li vyřešit doručení.

Když se všechny jeho opakované pokusy služby Event Grid odešle událost do umístění onta nedoručených zpráv. Pokud služby Event Grid přijme 400 (Chybný požadavek) nebo 413 (příliš velký požadavek Entity) kód odpovědi, okamžitě se událost odešle do koncového bodu onta nedoručených zpráv. Tyto kódy odpovědí znamenat, že nikdy úspěšné dodání události.

Existuje pět minut, než mezi poslední pokus o události a kdy se doručí do umístění onta nedoručených zpráv. Toto zpoždění má snížit počet operací úložiště objektů Blob. Pokud umístění onta nedoručených zpráv není k dispozici čtyři hodiny, události se zahodí.

Před nastavením umístění onta nedoručených zpráv, musíte mít účet úložiště s kontejnerem. Zadejte koncový bod pro tento kontejner, při vytváření odběru událostí. Koncový bod je ve formátu: `/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>/blobServices/default/containers/<container-name>`

Můžete být upozorněni na události odeslala do umístění nedoručených zpráv. Použití služby Event Grid pro reakci na nedoručené události [vytvoření odběru událostí](../storage/blobs/storage-blob-event-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) pro úložiště objektů blob onta nedoručených zpráv. Pokaždé, když vaše úložiště objektů blob onta nedoručených zpráv přijme nedoručené událost, Event Grid oznámí obslužnou rutinu. Obslužné rutiny jsou reaguje s akcemi, které chcete provést pro vyřešení nedoručené události.

Příklad nastavení umístění nedoručených zpráv, najdete v části [Dead písmeno a zásady opakování](manage-event-delivery.md).

## <a name="message-delivery-status"></a>Stav doručování zpráv

Event Grid používá kódy odpovědí protokolu HTTP na vědomí přijetí události. 

### <a name="success-codes"></a>Úspěch kódy

Event Grid bude považovat za **pouze** následující kódy odpovědi HTTP jako úspěšných doručení. Všechny ostatní stavové kódy se považují za neúspěšné doručení a bude opakovat nebo deadlettered podle potřeby. Při přijetí kódu úspěšný stav služby Event Grid považovat za doručování dokončený.

- 200 OK
- 201 – vytvořeno
- 202 přijato
- 203 Bez určujících informací
- 204 žádný obsah.

### <a name="failure-codes"></a>Kód selhání

Všechny ostatní kódy nejsou v sadě výše (200 – 204) se považují za selhání a bude opakovat. Některé mají zásady konkrétní opakování spojený s je uvedeno níže, všechny ostatní použijte standardní exponenciální regresní model. Je důležité si pamatovat, že z důvodu vysoce paralelizované povaha služby Event Grid architektura chování opakování je Nedeterministický. 

| Kód stavu | Chování opakování |
| ------------|----------------|
| 400 – Chybný požadavek | Zkuste to znovu za 5 minut nebo déle (nedoručených zpráv okamžitě, pokud instalační program nedoručené zprávy) |
| 401 Neautorizováno | Zkuste to znovu za 5 minut nebo déle |
| 403 Zakázáno | Zkuste to znovu za 5 minut nebo déle |
| 404 – Nenalezeno | Zkuste to znovu za 5 minut nebo déle |
| 408 – Časový limit žádosti | Zkuste zopakovat, až 2 minut nebo déle |
| Entita 413 požadavku je moc velká | Zkuste to znovu za 10 sekund nebo více (nedoručených zpráv okamžitě, pokud instalační program nedoručené zprávy) |
| 503 – Nedostupná služba | Zkuste to znovu za 30 sekund nebo více |
| Všechny ostatní | Zkuste to znovu za 10 sekund nebo více |


## <a name="next-steps"></a>Další postup

* Chcete-li zobrazit stav doručení událostí, naleznete v tématu [doručování zpráv služby Event Grid monitorování](monitor-event-delivery.md).
* Přizpůsobit možnosti doručování událostí, přečtěte si článek [Dead písmeno a zásady opakování](manage-event-delivery.md).
