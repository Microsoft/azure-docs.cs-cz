---
title: zahrnout soubor
description: zahrnout soubor
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 12/13/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: f48ad6ca74e6ce10148d66549fea16bc74015b2a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66171222"
---
V následující tabulce jsou uvedeny informace o kvótě specifické pro zasílání zpráv Azure Service Bus. Informace o cenách a další kvóty pro služby Service Bus, najdete v části [cenách služby Service Bus](https://azure.microsoft.com/pricing/details/service-bus/).

| Název kvóty | Scope | Poznámky | Hodnota |
| --- | --- | --- | --- |
| Maximální počet obory názvů Basic nebo Standard na předplatné Azure |Obor názvů |Na webu Azure portal odmítne odeslání dalších žádostí o další obory názvů Basic nebo Standard. |100|
| Maximální počet názvových prostorů Premium za předplatné Azure |Obor názvů |Odeslání dalších žádostí o další názvové prostory úrovně Premium se odmítnul na portálu. |25 |
| Velikost fronty nebo tématu |Entita |Definován při vytvoření fronty nebo tématu. <br/><br/> Další příchozí zprávy budou odmítnuty a volající kód obdrží výjimku. |1, 2, 3, 4 nebo 5 GB.<br /><br />V SKU úrovně Premium a standardních SKU s [dělení](/azure/service-bus-messaging/service-bus-partitioning) povolena, je maximální velikost fronty nebo tématu 80 GB. |
| Počet souběžných připojení na obor názvů |Obor názvů |Odeslání dalších žádostí o další připojení budou odmítnuty, a volající kód obdrží výjimku. Operace REST se nepočítají směrem k souběžná připojení TCP. |NetMessaging: 1,000.<br /><br />AMQP: 5,000. |
| Počet souběžných přijímání požadavků na entitu, fronty, tématu nebo odběru |Entita |Zobrazit další požadavky byly zamítnuty a obdrží výjimku ve volajícím kódu. Tato kvóta se vztahuje na kombinovaná počet souběžných operací příjmu napříč všemi předplatnými v tématu. |5 000 |
| Počet témat nebo front na obor názvů |Obor názvů |Odeslání dalších žádostí o vytvoření nového tématu nebo fronty v oboru názvů odmítají. V důsledku toho pokud je nakonfigurovaný pomocí [webu Azure portal][Azure portal], je vygenerována chybová zpráva. Pokud je volána z rozhraní API pro správu, volající kód přijme výjimku. |1 000 pro úroveň Basic nebo Standard. Celkový počet témat a front v oboru názvů musí být menší než nebo rovno 1 000. <br/><br/>Pro úroveň Premium, 1000 za jednotku zasílání zpráv (MU). Maximální limit je 4 000. |
| Počet [rozdělit na oddíly témat nebo front](/azure/service-bus-messaging/service-bus-partitioning) na obor názvů |Obor názvů |Odeslání dalších žádostí o vytváření nových oddílů tématu nebo fronty v oboru názvů odmítají. V důsledku toho pokud je nakonfigurovaný pomocí [webu Azure portal][Azure portal], je vygenerována chybová zpráva. Pokud volání z rozhraní API, výjimku pro správu **QuotaExceededException** přijme volajícímu kódu. |Základní a standardní úrovní: 100.<br/><br/>Dělené entity se nepodporují v [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) vrstvy.<br/><br />Každý dělená fronta nebo téma se počítá směrem k kvóty 1 000 entity na obor názvů. |
| Maximální velikost žádným entity zasílání zpráv: fronty nebo tématu |Entita |- |260 znaků. |
| Maximální velikost jakékoli zasílání zpráv název entity: pravidlo oboru názvů, předplatného nebo předplatného |Entita |- |50 znaků. |
| Maximální velikost [ID zprávy](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Entita |- | 128 |
| Maximální velikost zprávy [ID relace](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Entita |- | 128 |
| Velikost zprávy do fronty, tématu nebo odběru entity |Entita |Odmítne příchozí zprávy, které přesahují tyto kvóty a volající kód obdrží výjimku. |Maximální velikost zprávy: 256 KB pro [úrovně Standard](../articles/service-bus-messaging/service-bus-premium-messaging.md), 1 MB pro [úroveň Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />Z důvodu režie systému je tento limit menší než tyto hodnoty.<br /><br />Záhlaví maximální velikost: 64 KB.<br /><br />Maximální počet vlastnosti záhlaví v kontejneru objektů a dat: **bajtů/int. Hodnota MaxValue**.<br /><br />Maximální velikost vlastnosti v kontejneru objektů a dat: Žádné explicitní omezení. Omezeno daty, která záhlaví maximální velikost. |
| Velikost vlastnosti zprávy do fronty, tématu nebo odběru entity |Entita | Výjimka **SerializationException** je generován. |Maximální velikost vlastností pro každou vlastnost je 32 000. Celková velikost všech vlastností nesmí být delší než 64 000. Toto omezení platí pro celý záhlaví [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), který má například uživatel vlastnosti a vlastnosti systému [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [popisek](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)a [ ID zprávy](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Počet odběrů na téma |Entita |Odeslání dalších žádostí o vytvoření další předplatná tématu odmítají. V důsledku toho pokud je nakonfigurovaný pomocí portálu, se zobrazí chybová zpráva. Pokud je volána z rozhraní API pro správu, volající kód přijme výjimku. |Standard a Premium: Každé předplatné se počítá kvóty 1 000 entity, to znamená, fronty, témata a odběry na obor názvů. |
| Počet filtrů SQL na téma |Entita |Odeslání dalších žádostí o vytvoření další filtry k danému tématu odmítají a volající kód obdrží výjimku. |2 000 |
| Počet filtrů korelace jednotlivých tématech |Entita |Odeslání dalších žádostí o vytvoření další filtry k danému tématu odmítají a volající kód obdrží výjimku. |100 000 |
| Velikost filtry SQL nebo akce |Obor názvů |Odeslání dalších žádostí o vytvoření další filtry odmítají a volající kód obdrží výjimku. |Maximální délka řetězce podmínka filtru: 1,024 (1 K).<br /><br />Maximální délka řetězce akce pravidla: 1,024 (1 K).<br /><br />Maximální počet výrazů za akce pravidla: 32. |
| Počet [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) pravidel na obor názvů, fronty nebo tématu |Entity, obor názvů |Odeslání dalších žádostí o vytvoření dalších pravidel odmítají a volající kód obdrží výjimku. |Maximální počet pravidel: 12. <br /><br /> Pravidla, které jsou nakonfigurované na oboru názvů služby Service Bus se vztahují na všechny fronty a témata v tomto oboru názvů. |
| Počet zpráv na transakci | Transakce | Další příchozí zprávy budou odmítnuty, a k výjimce s oznámením "nelze odeslat více než 100 zprávy v rámci jedné transakce" neobdrží volající kód. | 100 <br /><br /> U obou **Send()** a **SendAsync()** operace. |

[Azure portal]: https://portal.azure.com
