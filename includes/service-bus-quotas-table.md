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
ms.openlocfilehash: b19dc7a85fafa1a4d875c84db9bbefabb3cd5a7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651678"
---
V následující tabulce jsou uvedeny informace o kvótě specifické pro zasílání zpráv služby Azure Service Bus. Informace o cenách a dalších kvótách pro službu Service Bus naleznete v [tématu Service Bus pricing](https://azure.microsoft.com/pricing/details/service-bus/).

| Název kvóty | Rozsah | Poznámky | Hodnota |
| --- | --- | --- | --- |
| Maximální počet základních nebo standardních oborů názvů na jedno předplatné Azure |Obor názvů |Následné požadavky na další základní nebo standardní obory názvů jsou odmítnuty portálem Azure. |100|
| Maximální počet oborů názvů Premium na jedno předplatné Azure |Obor názvů |Další požadavky na další obory názvů Premium jsou odmítnuty portálem. |100 |
| Velikost fronty nebo tématu |Entita |Definováno při vytvoření fronty nebo tématu. <br/><br/> Následné příchozí zprávy jsou odmítnuty a volající kód přijímá výjimku. |1, 2, 3, 4 GB nebo 5 GB.<br /><br />Ve skladové jednotce Premium a standardní skladové jednotce s [povoleným dělením](/azure/service-bus-messaging/service-bus-partitioning) je maximální velikost fronty nebo tématu 80 GB. |
| Počet souběžných připojení v oboru názvů |Obor názvů |Následné požadavky na další připojení jsou odmítnuty a volající kód přijímá výjimku. Operace REST se nezapočítávají do souběžných připojení TCP. |NetMessaging: 1 000.<br /><br />AMQP: 5000. |
| Počet souběžných požadavků na příjem ve frontě, tématu nebo entitě předplatného |Entita |Následné žádosti o přijetí jsou odmítnuty a volající kód obdrží výjimku. Tato kvóta se vztahuje na kombinovaný počet souběžných operací příjmu ve všech předplatných na téma. |5 000 |
| Počet témat nebo front na obor názvů |Obor názvů |Následné požadavky na vytvoření nového tématu nebo fronty v oboru názvů jsou odmítnuty. V důsledku toho pokud nakonfigurovaný prostřednictvím [portálu Azure][Azure portal], je generována chybová zpráva. Pokud volána z rozhraní API pro správu, je přijata výjimka volající kód. |10 000 pro úroveň Basic nebo Standard. Celkový počet témat a front v oboru názvů musí být menší nebo roven 10 000. <br/><br/>Pro úroveň Premium 1 000 na jednotku zasílání zpráv (MU). Maximální limit je 4 000. |
| Počet [dělených témat nebo front](/azure/service-bus-messaging/service-bus-partitioning) na obor názvů |Obor názvů |Následné požadavky na vytvoření nového děleného tématu nebo fronty v oboru názvů jsou odmítnuty. V důsledku toho pokud nakonfigurovaný prostřednictvím [portálu Azure][Azure portal], je generována chybová zpráva. Pokud je volána z rozhraní API pro správu, **výjimka QuotaExceededException** je přijata volajícím kódem. |Základní a standardní úrovně: 100.<br/><br/>Dělené entity nejsou podporovány ve vrstvě [Premium.](../articles/service-bus-messaging/service-bus-premium-messaging.md)<br/><br />Každá rozdělená fronta nebo téma se započítává do kvóty 1 000 entit na obor názvů. |
| Maximální velikost libovolné cesty k entitě zasílání zpráv: fronta nebo téma |Entita |- |260 znaků. |
| Maximální velikost libovolného názvu entity zasílání zpráv: obor názvů, odběr nebo pravidlo předplatného |Entita |- |50 znaků. |
| Maximální velikost [ID zprávy](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Entita |- | 128 |
| Maximální velikost [ID relace](/dotnet/api/microsoft.azure.servicebus.message.sessionid) zprávy | Entita |- | 128 |
| Velikost zprávy pro entitu fronty, tématu nebo předplatného |Entita |Příchozí zprávy, které překračují tyto kvóty, jsou odmítnuty a volající kód přijímá výjimku. |Maximální velikost zprávy: 256 KB pro [úroveň Standard](../articles/service-bus-messaging/service-bus-premium-messaging.md), 1 MB pro [úroveň Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />Z důvodu režie systému je toto omezení menší než tyto hodnoty.<br /><br />Maximální velikost záhlaví: 64 KB.<br /><br />Maximální počet vlastností záhlaví v vaku vlastností: **bajt/int. MaxValue**.<br /><br />Maximální velikost vlastnosti v sáčku vlastností: Žádné explicitní omezení. Omezeno maximální velikostí záhlaví. |
| Velikost vlastnosti zprávy pro entitu fronty, tématu nebo předplatného |Entita | Výjimka **SerializationException** je generována. |Maximální velikost vlastnosti zprávy pro každou vlastnost je 32 000. Kumulativní velikost všech vlastností nesmí překročit 64 000. Toto omezení platí pro celé záhlaví [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), který má vlastnosti uživatele a vlastnosti systému, například [SequenceNumber](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [Label](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)a [MessageId](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Počet odběrů na téma |Entita |Následné požadavky na vytvoření další odběry pro toto téma jsou odmítnuty. V důsledku toho, pokud je nakonfigurován prostřednictvím portálu, zobrazí se chybová zpráva. Pokud volána z rozhraní API pro správu, je přijata výjimka volající kód. |2 000 na téma pro úroveň Standard. |
| Počet filtrů SQL na téma |Entita |Následné požadavky na vytvoření dalších filtrů na toto téma jsou odmítnuty a volající kód přijímá výjimku. |2 000 |
| Počet korelačních filtrů na téma |Entita |Následné požadavky na vytvoření dalších filtrů na toto téma jsou odmítnuty a volající kód přijímá výjimku. |100 000 |
| Velikost filtrů nebo akcí SQL |Obor názvů |Následné požadavky na vytvoření dalších filtrů jsou odmítnuty a volající kód přijímá výjimku. |Maximální délka řetězce podmínky filtru: 1 024 (1 K).<br /><br />Maximální délka řetězce akce pravidla: 1 024 (1 K).<br /><br />Maximální počet výrazů na akci pravidla: 32. |
| Počet pravidel [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) pro obor názvů, frontu nebo téma |Entita, obor názvů |Následné požadavky na vytvoření dalších pravidel jsou odmítnuty a volající kód přijímá výjimku. |Maximální počet pravidel pro jeden typ entity: 12. <br /><br /> Pravidla, která jsou nakonfigurována v oboru názvů service bus, platí pro všechny typy: fronty, témata. |
| Počet zpráv na transakci | Transakce | Další příchozí zprávy jsou odmítnuty a volající kód přijímá výjimku "Nelze odeslat více než 100 zpráv v jedné transakci". | 100 <br /><br /> Pro operace **Send()** i **SendAsync().** |
| Počet pravidel filtru virtuální sítě a IP adres | Obor názvů | &nbsp; | 128 | 

[Azure portal]: https://portal.azure.com
