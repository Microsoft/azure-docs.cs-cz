---
title: zahrnout soubor
description: zahrnout soubor
services: service-bus-messaging
author: sethmanheim
ms.service: service-bus-messaging
ms.topic: include
ms.date: 06/04/2018
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 0ff1e31e52c7db5c41f92cb9e4cb1a17f28dea6f
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34806396"
---
Následující tabulka uvádí informace o kvótě specifické pro zasílání zpráv Service Bus. Informace o cenách a dalších kvóty pro Service Bus, najdete v článku [sběrnice Service – ceny](https://azure.microsoft.com/pricing/details/service-bus/) Přehled.

| Název kvóty | Rozsah | Poznámky | Hodnota |
| --- | --- | --- | --- | --- |
| Maximální počet základní / standardní obory názvů za předplatné Azure |Obor názvů |Odeslání dalších žádostí o další obory názvů základní / standardní odmítne portálu. |100|
| Maximální počet oborů názvů premium za předplatné Azure |Obor názvů |Odeslání dalších žádostí o další premium obory názvů odmítne portálu. |10 |
| Velikost fronty nebo tématu |Entita |Definován při vytvoření fronty nebo téma. <br/><br/> Následné příchozí zprávy budou odmítnuty a volající kód přijme výjimku. |1, 2, 3, 4 GB nebo 5 GB.<br /><br />V skladová položka Premium a Standard s [dělení](/azure/service-bus-messaging/service-bus-partitioning) povoleno, fronta nebo téma maximální velikost je 80 GB. |
| Počet souběžných připojení na obor názvů |Obor názvů |Odeslání dalších žádostí o další připojení odmítnuty a volající kód přijme výjimku. Operace REST není započítávat souběžných připojení TCP. |NetMessaging: 1 000<br /><br />AMQP: 5 000 |
| Počet souběžných přijímat požadavky na entity fronta nebo téma/odběr |Entita |Přijímat další požadavky byly zamítnuty a volající kód přijme výjimku. Tato kvóta se vztahuje na kombinovaná počet souběžných obdrží operace ve všech předplatných na téma. |5 000 |
| Počet témat/front na obor názvů |Obor názvů |Pro vytvoření nové téma nebo fronty na obor názvů následné požadavky byly zamítnuty. V důsledku toho, pokud nakonfigurovaný pomocí [portál Azure][Azure portal], je generována chybovou zprávu. Pokud volání z rozhraní API pro správu, je kód volání přijatých výjimku. |10 000<br /><br />Celkový počet témata a fronty v oboru názvů musí být menší než nebo rovno 10 000. |
| Počet [oddíly témata/fronty](/azure/service-bus-messaging/service-bus-partitioning) na obor názvů |Obor názvů |Pro vytvoření nové téma oddílů nebo fronty na obor názvů následné požadavky byly zamítnuty. V důsledku toho, pokud nakonfigurovaný pomocí [portál Azure][Azure portal], je generována chybovou zprávu. Pokud volání z rozhraní API, správy **quotaexceededexception –** výjimka je přijatých volající kód. |Úrovně Basic a Standard - 100<br/><br/>Dělené entity nejsou podporovány v [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) vrstvy.<br/><br />Každý oddílů fronta nebo téma započítává kvótu 10 000 entit na obor názvů. |
| Maximální velikost všech zasílání zpráv entity cesty: fronta nebo téma |Entita |- |260 znaků |
| Maximální velikost všech zasílání zpráv název entity: obor názvů, předplatné nebo předplatné pravidlo |Entita |- |50 znaků |
| Maximální velikost zprávy [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) | Entita |- | 128 |
| Velikost zprávy pro entitu fronta nebo téma/odběr |Entita |Odmítne příchozí zprávy, které překračují těchto kvót a volající kód přijme výjimku. |Maximální velikost zprávy: 256 KB ([úrovně Standard](../articles/service-bus-messaging/service-bus-premium-messaging.md)) nebo 1 MB ([úroveň Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md)). <br /><br />Z důvodu režijní náklady na systém tento limit je menší než tyto hodnoty.<br /><br />Velikost maximální záhlaví: 64 KB<br /><br />Maximální počet vlastností hlavičky v kontejneru objektů a dat: **bajtů/int. MaxValue**<br /><br />Maximální velikost vlastnosti v kontejneru objektů a dat: žádné explicitní omezení. Omezené velikost maximální záhlaví. |
| Velikost zprávy vlastnost pro entity fronta nebo téma/odběr |Entita |A **serializationexception –** se vygeneruje výjimka. |Maximální velikost vlastnosti pro každou vlastnost je 32 K. Celková velikost všech vlastností nesmí překročit 64 K. Toto omezení se vztahují na celou záhlaví [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), která má oba vlastnosti uživatele a také vlastnosti systému (například [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [popisek](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label), [ MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid)a tak dále). |
| Počet odběrů za tématu |Entita |Pro vytvoření další odběr tématu následné požadavky byly zamítnuty. Výsledkem je pokud nakonfigurovaný pomocí portálu, se zobrazí chybová zpráva. Pokud volání z rozhraní API pro správu je kód volání přijatých výjimku. |2,000 |
| Počet filtrů SQL za tématu |Entita |Odeslání dalších žádostí o vytvoření dalších filtrů v sekci odmítnuty a volající kód přijme výjimku. |2,000 |
| Počet filtrů korelace za tématu |Entita |Odeslání dalších žádostí o vytvoření dalších filtrů v sekci odmítnuty a volající kód přijme výjimku. |100,000 |
| Velikost SQL filtry nebo akce |Obor názvů |Odeslání dalších žádostí o vytvoření další filtry odmítnuty a volající kód přijme výjimku. |Maximální délka řetězce podmínku filtru: 1024 (1 kB).<br /><br />Maximální délka řetězce akce pravidla: 1024 (1 kB).<br /><br />Maximální počet výrazů za akce pravidla: 32. |
| Počet [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) pravidel na obor názvů, fronta nebo téma |Entita, obor názvů |Odeslání dalších žádostí o vytvoření dalších pravidlech odmítnuty a volající kód přijme výjimku. |Maximální počet pravidel: 12. <br /><br /> Pravidla, které jsou nakonfigurované na oboru názvů Service Bus platí pro všechny fronty a témata v tomto oboru názvů. |
| Počet zpráv na transakci | Transakce | Další příchozí zprávy budou odmítnuty a k výjimce s oznámením "nelze odeslat více než 100 zprávy v rámci jedné transakce" přijme kód volání. | 100 <br /><br /> Pro obě **Send()** a **SendAsync()** operace. |

[Azure portal]: https://portal.azure.com