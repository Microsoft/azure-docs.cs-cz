---
title: Izolační aplikace Azure Service Bus proti výpadkům a havárií | Microsoft Docs
description: Techniky k ochraně aplikací proti potenciální výpadek služby Service Bus.
services: service-bus-messaging
author: sethmanheim
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 06/05/2018
ms.author: sethm
ms.openlocfilehash: 38aaf6d7ddad1527e113efa502ae47b82165b079
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34802302"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Osvědčené postupy pro izolační aplikace proti výpadkům Service Bus a havárií

Kritické aplikace musí fungovat nepřetržitě, ani za přítomnosti neplánované výpadky nebo havárie. Toto téma popisuje postupy, pomocí nichž můžete použít k ochraně aplikací Service Bus proti potenciální výpadek služby nebo po havárii.

Výpadek je definován jako dočasné nedostupnosti Azure Service Bus. Se výpadek může ovlivnit některé součásti Service Bus, jako je zasílání zpráv úložiště nebo i celého datového centra. Po napravení problému, Service Bus opět k dispozici. Výpadek obvykle nezpůsobí ztrátě zpráv nebo jiná data. Je například selhání součásti nedostupnost konkrétní úložišti pro přenos zpráv. Příkladem výpadku celou datacenter je výpadku napájení datovém centru nebo vadný datacenter síťový přepínač. Výpadek může trvat několik minut na několik dní.

Havárie je definován jako trvalé ztrátě jednotky škálování služby Service Bus nebo datacenter. Datacentru může nebo nemusí opět k dispozici. Havárie obvykle způsobí ztrátu některé nebo všechny zprávy nebo jiná data. Příklady havárie se ještě efektivněji, zahlcení nebo zemětřesení.

## <a name="current-architecture"></a>Aktuální architektura
Service Bus používá několik úložišť pro přenos zpráv k uložení zpráv, které jsou odesílány do fronty nebo témata. Bez oddílů fronta nebo téma je přiřazený k jedné úložišti pro přenos zpráv. Pokud toto úložiště zasílání zpráv není k dispozici, všechny operace v tomto fronta nebo téma se nezdaří.

Všechny služby Service Bus entit pro zasílání zpráv (fronty, témata, předávání) se nacházejí v oboru názvů služby, který je přidružen k datacentru. Service Bus nyní podporuje [ *geograficky havárii* a *geografická replikace* ](service-bus-geo-dr.md) na úrovni oboru názvů.

## <a name="protecting-queues-and-topics-against-messaging-store-failures"></a>Ochrana fronty a témata proti selhání úložiště pro zasílání zpráv
Bez oddílů fronta nebo téma je přiřazený k jedné úložišti pro přenos zpráv. Pokud toto úložiště zasílání zpráv není k dispozici, všechny operace v tomto fronta nebo téma se nezdaří. Oddílů fronty, na druhé straně, se skládá z více fragmentů. Každý fragment je uložen v jiném úložišti zasílání zpráv. Pro odeslání zprávy do oddílů fronta nebo téma sběrnice přiřadí zpráva jedním z fragmentů. Pokud odpovídající úložišti pro přenos zpráv není k dispozici, Service Bus zapíše zprávu do různých fragment, pokud je to možné. Dělené entity již nejsou podporovány v [skladová položka Premium](service-bus-premium-messaging.md). 

Další informace o dělené entity najdete v tématu [segmentované entity zasílání zpráv][Partitioned messaging entities].

## <a name="protecting-against-datacenter-outages-or-disasters"></a>Ochrana proti výpadkům datacenter nebo havárie
Povolit pro převzetí služeb při selhání dvou Datacenter, můžete vytvořit obor názvů služby Service Bus v každé datové centrum. Například oboru názvů služby Service Bus **contosoPrimary.servicebus.windows.net** může nacházet v oblasti USA, Severní a střední a **contosoSecondary.servicebus.windows.net**může nacházet v oblasti USA – jih a střední. Pokud Service Bus entity pro zasílání zpráv musí zůstat přístupný v případě výpadku datového centra, můžete vytvořit dané entity v oba obory názvů.

Další informace najdete v části "Service Bus v rámci datového centra Azure selhání" v [asynchronní vzory a vysoká dostupnost pro zasílání zpráv][Asynchronous messaging patterns and high availability].

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Ochranu koncových bodů předávání proti výpadkům datacenter nebo havárie
Geografická replikace koncových bodů předávání umožňuje služba, která zveřejňuje koncový bod předávání být dostupná v případě výpadku služby Service Bus. K dosažení geografická replikace, musíte službu vytvořit dva koncové body předávání v různých oborech názvů. Obory názvů se musí nacházet v různých datových centrech a dva koncové body musí mít odlišné názvy. Například můžete v části dostupný primární koncový bod **contosoPrimary.servicebus.windows.net/myPrimaryService**při jeho protějšku sekundární dostupný v části **contosoSecondary.servicebus.windows.net /mySecondaryService**.

Služba pak naslouchá na obou koncových bodů a klient vyvolat službu přes koncový bod. Klientská aplikace náhodně vybere jeden z předávání jako primární koncový bod a odešle požadavku aktivní koncový bod. Pokud operace selže s kódem chyby, toto selhání naznačuje, že předávání přes koncový bod není k dispozici. Aplikace se otevře kanál ke koncovému bodu zálohy a znovu vydá požadavek. V tomto bodě zálohování koncové body a aktivní přepínač role: klientská aplikace považovat staré aktivní koncový bod nový koncový bod zálohování a původní zálohování koncový bod jako nové aktivní koncový bod. Pokud obě odeslat operace selže, zůstanou nezměněny role dvě entity a vrátí se chyba.

## <a name="protecting-queues-and-topics-against-datacenter-outages-or-disasters"></a>Ochrana fronty a témata proti výpadkům datacenter nebo havárie
Zajistit odolnost proti výpadkům datového centra při pomocí zprostředkovaného zasílání zpráv Service Bus podporuje dva přístupy: *active* a *pasivní* replikace. Pro každý přístup Pokud se daný fronta nebo téma musí zůstat přístupný v případě výpadku datového centra můžete vytvořit ji v oba obory názvů. Obě entit může mít stejný název. Například dostupný primární fronty pod **contosoPrimary.servicebus.windows.net/myQueue**při jeho protějšku sekundární dostupný v části **contosoSecondary.servicebus.windows.net/myQueue**.

Pokud aplikace nevyžaduje komunikaci trvalé odesílatele k příjemce, můžete aplikaci implementovat trvalé fronty klienta předchází se tak ztrátě zpráv a pro stínění odesílatel z jakékoli přechodné chyby Service Bus.

## <a name="active-replication"></a>Replikace služby Active
Replikace služby Active používá entity v oba obory názvů pro všechny operace. Libovolného klienta, který odešle zprávu odešle dvě kopie stejné zprávy. První kopie se odesílá do primární entity (například **contosoPrimary.servicebus.windows.net/sales**), a druhé kopie zprávy jsou odeslána do sekundární entitou (například  **contosoSecondary.servicebus.windows.net/sales**).

Klient obdrží z obou front zpráv. Příjemce procesy první kopie zprávy, a druhé kopie potlačeno. K potlačení duplicitních zpráv, musíte označit odesílatel každou zprávu s jedinečným identifikátorem. Obě kopie zprávy musí být označené se stejným identifikátorem. Můžete použít [BrokeredMessage.MessageId] [ BrokeredMessage.MessageId] nebo [BrokeredMessage.Label] [ BrokeredMessage.Label] vlastnosti nebo vlastní vlastnost k označení zprávy. Příjemce musí zachovat seznam zpráv, které již přijal.

[Geografická replikace se Service Bus zprostředkované zprávy] [ Geo-replication with Service Bus Brokered Messages] příklad znázorňuje active replikaci entity pro zasílání zpráv.

> [!NOTE]
> Replikace služby active přístup zdvojnásobí počet operací, proto tento přístup může vést k vyšší náklady.
> 
> 

## <a name="passive-replication"></a>Pasivní replikace
V případě selhání bez pasivní replikace používá jenom jeden dva entit pro zasílání zpráv. Klient odešle zprávu do active entity. Pokud operace u active entity selže s kódem chyby, která určuje, že datové centrum, který je hostitelem aktivní entita může být k dispozici, klient odešle kopii zprávy do zálohování entity. V tomto bodě aktivní a entity zálohování přepínač role: odesílání klienta považuje staré active entity, která má být nové entity zálohování a původní zálohování entita je nové aktivní entity. Pokud obě odeslat operace selže, zůstanou nezměněny role dvě entity a vrátí se chyba.

Klient obdrží z obou front zpráv. Protože je pravděpodobné, že příjemce obdrží dvě kopie stejné zprávy, musí příjemce potlačení duplicitních zpráv. Duplicitní položky můžete potlačit stejným způsobem, jak je popsáno pro replikace služby active.

Obecně platí je levnější než replikace služby active pasivní replikace, protože ve většině případů je prováděna pouze jednu operaci. Latence, propustnosti a náklady na peněžní totožné s nereplikované scénář.

Při použití pasivní replikace, v následujících scénářích zprávy můžou ke ztrátě nebo přijímat dvakrát:

* **Zpráva zpoždění nebo ztrátě**: předpokládají, že odesílatel zprávu m1 úspěšně odeslán do primární fronty, a pak nedostupný fronty než příjemce obdrží m1. Odesílatel odešle následnou zprávu m2 sekundární fronty. Pokud primární fronta je dočasně nedostupný, obdrží příjemce m1 po fronty opět k dispozici. V případě havárie příjemce obdržet nikdy m1.
* **Duplicitní příjem**: předpokládá, že odesílatel odešle zprávu m do primární fronty. Service Bus úspěšně zpracuje m, ale selže k odeslání odpovědi. Po operaci odeslání vypršení časového limitu, odesílatel odešle identické kopii m sekundární fronty. Pokud je příjemce schopný přijímat první kopii m, než bude k dispozici primární fronty, příjemce obdrží obě kopie m přibližně ve stejnou dobu. Pokud příjemce není schopný přijímat první kopii m, než bude k dispozici primární fronty, nejprve obdrží druhé kopie m příjemce, ale pak obdrží druhé kopie m, když primární fronty je dostupná.

[Geografická replikace se Service Bus zprostředkované zprávy] [ Geo-replication with Service Bus Brokered Messages] příklad znázorňuje pasivní replikaci entity pro zasílání zpráv.

## <a name="geo-replication"></a>Geografická replikace

Service Bus podporuje havárii geografické obnovení a geografická replikace, na úrovni oboru názvů. Další informace najdete v tématu [Azure Service Bus Geo-havárii](service-bus-geo-dr.md). Po havárii funkci obnovení, k dispozici pro [skladová položka Premium](service-bus-premium-messaging.md) pouze implementuje zotavení po havárii metadata a spoléhá na obory názvů pro zotavení po havárii primární a sekundární.

## <a name="next-steps"></a>Další postup
Další informace o zotavení po havárii, najdete v těchto článcích:

* [Azure Service Bus Geo-havárii](service-bus-geo-dr.md)
* [Kontinuita podnikových procesů Azure SQL Database][Azure SQL Database Business Continuity]
* [Návrh aplikací Azure odolných proti chybám][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label
[Geo-replication with Service Bus Brokered Messages]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency
