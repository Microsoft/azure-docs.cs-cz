---
title: zahrnout soubor
description: zahrnout soubor
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 07/15/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 764d75872eb1bad0647235cd70f9984ebd7789bd
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027366"
---
Následující tabulka uvádí informace o kvótě, které jsou specifické pro Azure Service Bus zasílání zpráv. Informace o cenách a dalších kvótách pro Service Bus najdete v tématu [Service Bus ceny](https://azure.microsoft.com/pricing/details/service-bus/).

| Název kvóty | Rozsah | Poznámky | Hodnota |
| --- | --- | --- | --- |
| Maximální počet základních nebo standardních oborů názvů na předplatné Azure |Obor názvů |Následné žádosti o další obory názvů Basic nebo Standard jsou Azure Portal odmítnuty. |100|
| Maximální počet oborů názvů úrovně Premium na předplatné Azure |Obor názvů |Následné žádosti o další obory názvů úrovně Premium jsou na portálu odmítnuty. |100 |
| Velikost fronty nebo tématu |Entita |Definováno při vytváření fronty nebo tématu. <br/><br/> Následné příchozí zprávy jsou odmítnuty a volající kód obdrží výjimku. |1, 2, 3, 4 GB nebo 5 GB.<br /><br />V SKU úrovně Premium a standardní SKU s povoleným [rozdělením na oddíly](../articles/service-bus-messaging/service-bus-partitioning.md) je maximální velikost fronty nebo tématu 80 GB. |
| Počet souběžných připojení na obor názvů |Obor názvů |Následné žádosti o další připojení jsou odmítnuty a volající kód obdrží výjimku. Operace REST se nepočítají směrem k souběžným připojením TCP. |Síťové zasílání zpráv: 1 000.<br /><br />AMQP: 5 000. |
| Počet souběžných žádostí o přijetí změn ve frontě, tématu nebo entitě předplatného |Entita |Následné žádosti o přijetí byly zamítnuty a volající kód obdrží výjimku. Tato kvóta se vztahuje na kombinovaný počet souběžných operací příjmu napříč všemi předplatnými v tématu. |5 000 |
| Počet témat nebo front na obor názvů |Obor názvů |Následné požadavky na vytvoření nového tématu nebo fronty v oboru názvů se odmítnou. V důsledku toho se při konfiguraci pomocí [Azure Portal][Azure portal]vygeneruje chybová zpráva. Pokud je volána z rozhraní API pro správu, je vyvolána výjimka volajícím kódem. |10 000 pro úroveň Basic nebo Standard. Celkový počet témat a front v oboru názvů musí být menší nebo roven 10 000. <br/><br/>Pro úroveň Premium 1 000 za jednotku zasílání zpráv (MU). Maximální limit je 4 000. |
| Počet [rozdělených témat nebo front](../articles/service-bus-messaging/service-bus-partitioning.md) na obor názvů |Obor názvů |Následné požadavky na vytvoření nového oddílu nebo fronty v oboru názvů se odmítnou. V důsledku toho se při konfiguraci pomocí [Azure Portal][Azure portal]vygeneruje chybová zpráva. Pokud je volána z rozhraní API pro správu, výjimka **QuotaExceededException** je přijata volajícím kódem. |Úrovně Basic a Standard: 100.<br/><br/>Dělené entity nejsou podporovány na úrovni [Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md) .<br/><br />Každá dělená fronta nebo téma se počítá s kvótou 1 000 entit na obor názvů. |
| Maximální velikost jakékoli cesty entit zasílání zpráv: fronta nebo téma |Entita |- |260 znaků. |
| Maximální velikost všech názvů entit pro zasílání zpráv: obor názvů, předplatné nebo pravidlo předplatného |Entita |- |50 znaků. |
| Maximální velikost [ID zprávy](/dotnet/api/microsoft.azure.servicebus.message.messageid) | Entita |- | 128 |
| Maximální velikost [ID relace](/dotnet/api/microsoft.azure.servicebus.message.sessionid) zprávy | Entita |- | 128 |
| Velikost zprávy pro entitu, téma nebo předplatného |Entita |Příchozí zprávy, které překračují tyto kvóty, se odmítnou a volající kód obdrží výjimku. |Maximální velikost zprávy: 256 KB pro [úroveň Standard](../articles/service-bus-messaging/service-bus-premium-messaging.md), 1 MB pro [úroveň Premium](../articles/service-bus-messaging/service-bus-premium-messaging.md). <br /><br />Kvůli režii systému je toto omezení menší než tyto hodnoty.<br /><br />Maximální velikost hlavičky: 64 KB.<br /><br />Maximální počet vlastností záhlaví v kontejneru objektů a dat: **Byte/int MaxValue**.<br /><br />Maximální velikost vlastnosti v kontejneru objektů a dat: žádné explicitní omezení Omezeno maximální velikostí hlavičky. |
| Velikost vlastnosti zprávy pro entitu, téma nebo předplatného |Entita | Výjimka `SerializationException` je vygenerována. |Maximální velikost vlastnosti zprávy pro každou vlastnost je 32 000. Kumulativní velikost všech vlastností nemůže být větší než 64 000. Toto omezení se vztahuje na celé záhlaví zprostředkované [zprávy](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage), která má vlastnosti uživatele i systémové vlastnosti, například [pořadové číslo](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sequencenumber), [popisek](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.label)a [ID zprávy](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid). |
| Počet předplatných na téma |Entita |Následné žádosti o vytvoření dalších předplatných pro téma jsou odmítnuty. V důsledku toho se zobrazí chybová zpráva, pokud je nakonfigurována prostřednictvím portálu. Pokud je volána z rozhraní API pro správu, je vyvolána výjimka volajícím kódem. |2 000 na téma pro úroveň Standard a úroveň Premium. |
| Počet filtrů SQL na téma |Entita |Následné žádosti o vytvoření dalších filtrů v tématu jsou odmítnuty a volající kód obdrží výjimku. |2 000 |
| Počet filtrů korelace na téma |Entita |Následné žádosti o vytvoření dalších filtrů v tématu jsou odmítnuty a volající kód obdrží výjimku. |100 000 |
| Velikost filtrů nebo akcí SQL |Obor názvů |Následné žádosti o vytvoření dalších filtrů jsou odmítnuty a volající kód obdrží výjimku. |Maximální délka řetězce podmínky filtru: 1 024 (1 KB).<br /><br />Maximální délka řetězce akce pravidla: 1 024 (1 KB).<br /><br />Maximální počet výrazů na akci pravidla: 32. |
| Počet pravidel [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) na obor názvů, frontu nebo téma |Entita, obor názvů |Následné žádosti o vytvoření dalších pravidel jsou odmítnuty a volající kód obdrží výjimku. |Maximální počet pravidel na typ entity: 12. <br /><br /> Pravidla konfigurovaná na Service Bus oboru názvů se vztahují na všechny typy: fronty, témata. |
| Počet zpráv na transakci | Transakce | Další příchozí zprávy jsou odmítnuty a výjimka "nelze odeslat více než 100 zpráv v rámci jedné transakce" je přijímána volajícím kódem. | 100 <br /><br /> Pro operace **Send ()** i **SendAsync ()** . |
| Počet pravidel virtuální sítě a filtru IP | Obor názvů | &nbsp; | 128 | 

[Azure portal]: https://portal.azure.com