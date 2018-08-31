---
title: zahrnout soubor
description: zahrnout soubor
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 08/29/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 481ae07ae9f8877ff93b2fee948849076c054906
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2018
ms.locfileid: "43184666"
---
V následující tabulce jsou uvedeny informace o kvótě specifické pro zasílání zpráv Service Bus. Informace o cenách a další kvóty pro služby Service Bus, najdete v článku [ceny služby Service Bus](https://azure.microsoft.com/pricing/details/service-bus/) Přehled.

| Název kvóty | Rozsah | Poznámky | Hodnota |
| --- | --- | --- | --- | --- |
| Maximální počet základní / standardní obory názvů na předplatné Azure |Obor názvů |Odeslání dalších žádostí o další obory názvů basic / standard odmítnula na portálu. |100|
| Maximální počet názvových prostorů premium za předplatné Azure |Obor názvů |Odeslání dalších žádostí o oborech názvů úrovně premium další odmítnula na portálu. |10 |
| Velikost fronty nebo tématu |Entita |Definován při vytvoření fronty nebo tématu. <br/><br/> Další příchozí zprávy budou odmítnuty a volající kód obdrží výjimku. |1, 2, 3, 4 nebo 5 GB.<br /><br />V SKU úrovně Premium a Standard s [dělení](/azure/service-bus-messaging/service-bus-partitioning) povolena, fronty nebo tématu maximální velikost je 80 GB. |
| Počet souběžných připojení na obor názvů |Obor názvů |Odeslání dalších žádostí o další připojení budou odmítnuty a volající kód obdrží výjimku. Operace REST není započítávat souběžná připojení TCP. |NetMessaging: 1 000<br /><br />AMQP: 5 000 |
| Počet souběžných přijímání požadavků na entitu, fronty nebo tématu/odběru |Entita |Zobrazit další požadavky byly zamítnuty a obdrží výjimku ve volajícím kódu. Tato kvóta se vztahuje na kombinovaná počet souběžných operací příjmu napříč všemi předplatnými v tématu. |5 000 |
| Počet témat nebo front na obor názvů |Obor názvů |Odeslání dalších žádostí o vytvoření nového tématu nebo fronty v oboru názvů odmítají. V důsledku toho pokud je nakonfigurovaný pomocí [webu Azure portal][Azure portal], je vygenerována chybová zpráva. Pokud je volána z rozhraní API pro správu, volající kód přijme výjimku. |10 000 (úroveň basic nebo Standard). Celkový počet témat a front v oboru názvů musí být menší než nebo rovna 10 000. <br/><br/>Pro úroveň premium, 1000 za unit(MU) zasílání zpráv. Maximální limit je 4000. |
| Počet [rozdělit na oddíly témat nebo front](/azure/service-bus-messaging/service-bus-partitioning) na obor názvů |Obor názvů |Odeslání dalších žádostí o vytváření nových oddílů tématu nebo fronty v oboru názvů odmítají. V důsledku toho pokud je nakonfigurovaný pomocí [webu Azure portal][Azure portal], je vygenerována chybová zpráva. Pokud volání z rozhraní API, správy **QuotaExceededException** přijme volající kód výjimky. |Úrovně Basic a Standard – 100<br/><br/>Dělené entity se nepodporují v [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) vrstvy.<br/><br />Každý dělená fronta nebo téma započítává kvóta 10 000 entity na obor názvů. |
| Maximální velikost žádným entity zasílání zpráv: fronty nebo tématu |Entita |- |260 znaků |
| Maximální velikost jakékoli zasílání zpráv název entity: pravidlo oboru názvů, předplatného nebo předplatného |Entita |- |50 znaků. |
| Maximální velikost zprávy [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Entita |- | 128 |
| Velikost zprávy do fronty/tématu/odběru entity |Entita |Odmítne příchozí zprávy, které přesahují tyto kvóty a obdrží výjimku ve volajícím kódu. |Maximální velikost zprávy: 256 KB ([úrovně Standard](../articles/service-bus-messaging/service-bus-premium-messaging.md)) / 1 MB ([úroveň Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md)). <br /><br />Z důvodu režie systému je tento limit menší než tyto hodnoty.<br /><br />Záhlaví maximální velikost: 64 KB<br /><br />Maximální počet vlastnosti záhlaví v kontejneru objektů a dat: **bajtů/int. Hodnota MaxValue**<br /><br />Maximální velikost vlastnosti v kontejneru objektů a dat: žádné explicitní omezení. Omezeno daty, která záhlaví maximální velikost. |
| Velikost vlastnosti zprávy do fronty/tématu/odběru entity |Entita |A **SerializationException** vygenerována výjimka. |Maximální velikost vlastností pro každou vlastnost je, že 32 K. Celková velikost všech vlastností nesmí být delší než 64 K. Toto omezení platí pro celý záhlaví [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), který má obě vlastnosti uživatele, jakož i vlastnosti systému (například [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [popisek](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), [ ID zprávy](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid), a tak dále). |
| Počet odběrů na téma |Entita |Odeslání dalších žádostí o vytvoření další předplatná tématu odmítají. V důsledku toho pokud je nakonfigurovaný pomocí portálu, se zobrazí chybová zpráva. Pokud volání z rozhraní API pro správu přijme volající kód výjimky. |2,000 |
| Počet filtrů SQL na téma |Entita |Odeslání dalších žádostí o vytvoření další filtry k danému tématu odmítají a volající kód obdrží výjimku. |2,000 |
| Počet filtrů korelace jednotlivých tématech |Entita |Odeslání dalších žádostí o vytvoření další filtry k danému tématu odmítají a volající kód obdrží výjimku. |100 000 |
| Velikost SQL filtry a akcí |Obor názvů |Odeslání dalších žádostí o vytvoření další filtry jsou zamítnuty, obdrží výjimku volající kód. |Maximální délka řetězce podmínka filtru: 1024 (1 kB).<br /><br />Maximální délka řetězce akce pravidla: 1024 (1 kB).<br /><br />Maximální počet výrazů za akce pro pravidlo: 32. |
| Počet [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) pravidel na obor názvů, fronty nebo tématu |Entity, obor názvů |Odeslání dalších žádostí o vytvoření dalších pravidel jsou zamítnuty, obdrží výjimku volající kód. |Maximální počet pravidel: 12. <br /><br /> Pravidla, které jsou nakonfigurované na oboru názvů služby Service Bus se vztahují na všechny fronty a témata v tomto oboru názvů. |
| Počet zpráv na transakci | Transakce | Další příchozí zprávy budou odmítnuty a k výjimce s oznámením "nelze odeslat více než 100 zprávy v rámci jedné transakce" neobdrží volajícímu kódu. | 100 <br /><br /> U obou **Send()** a **SendAsync()** operace. |

[Azure portal]: https://portal.azure.com