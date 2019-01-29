---
title: Izolační aplikace Azure Service Bus proti výpadků a havárií | Dokumentace Microsoftu
description: Techniky pro ochranu aplikací proti potenciální výpadek služby Service Bus.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: 2a51447f3d9f8e9e8bed41c47214d7784924c85a
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099828"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Osvědčené postupy pro izolační aplikace využívající službu Service Bus výpadků a havárií

Důležité podnikové aplikace musí průběžně fungovat i v případě výskytu neplánované výpadky nebo jiného problému ovlivňujícího. Tento článek popisuje postupy, které můžete použít k ochraně aplikací služby Service Bus proti potenciální výpadku nebo havárie.

Kvůli výpadku je definován jako dočasná nedostupnost služby Azure Service Bus. Výpadek může mít vliv na některé součásti služby Service Bus, jako je například úložiště, nebo dokonce celé datové centrum. Po vyřešení problému, Service Bus opět k dispozici. Kvůli výpadku obvykle nezpůsobí ztrátu zprávy nebo jiná data. Příklad selhání součásti je nedostupnost konkrétní úložiště. Příklad výpadek úrovni celého datového centra je výpadku datového centra nebo chybný datacenter síťový přepínač. Kvůli výpadku může trvat pár minut několik dní.

Havárii je definován jako trvalé ztrátě jednotka škálování služby Service Bus nebo datového centra. Datacentru může nebo nemusí opět k dispozici. Havárii obvykle způsobí ztrátu některých nebo všech zpráv nebo jiná data. Příkladem jiného problému ovlivňujícího jsou fire, zahlcení nebo zemětřesení.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Ochrana proti výpadků a havárií - Service Bus úrovně Premium
Vysoká dostupnost a zotavení po havárii koncepty jsou zabudovány do vrstvy Azure Service Bus úrovně Premium, oba ve stejné oblasti (prostřednictvím zón dostupnosti) a v různých oblastech (prostřednictvím geografického zotavení).

### <a name="geo-disaster-recovery"></a>Geografické zotavení po havárii

Service Bus úrovně Premium podporuje zotavení po havárii geograficky, na úrovni oboru názvů. Další informace najdete v tématu [Azure Service Bus Geo-zotavení po havárii](service-bus-geo-dr.md). Po havárii funkci obnovení, k dispozici pro [SKU úrovně Premium](service-bus-premium-messaging.md) pouze, implementuje zotavení po havárii metadata a spoléhá na obory názvů zotavení po havárii primární a sekundární.

### <a name="availability-zones"></a>Zóny dostupnosti

SKU služby Service Bus úrovně Premium podporuje [zóny dostupnosti](../availability-zones/az-overview.md), poskytuje umístění s izolací chyb v rámci stejné oblasti Azure.

> [!NOTE]
> Podpora zóny dostupnosti Azure Service Bus úrovně Premium je k dispozici pouze [oblastí Azure](../availability-zones/az-overview.md#regions-that-support-availability-zones) kde zóny dostupnosti jsou k dispozici.

Zóny dostupnosti můžete povolit na pouze nové obory názvů pomocí webu Azure portal. Service Bus nepodporuje migraci z existující oborů názvů. Po povolení na váš obor názvů nejde zakázat redundanci zón.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Ochrana proti výpadků a havárií - Service Bus na úrovni Standard
Zajistit odolnost proti výpadkům datového centra při použití standardní cenová úroveň zasílání zpráv Service Bus podporuje dva přístupy: *aktivní* a *pasivní* replikace. Pro každý přístup Pokud do dané fronty nebo tématu musí zůstat přístupný za přítomnosti k výpadku datového centra je můžete jej vytvořit v obou oborů názvů. Obě entit může mít stejný název. Například můžete do primární fronty dostupný v části **contosoPrimary.servicebus.windows.net/myQueue**, ale jeho protějšek sekundární dostupné v rámci **contosoSecondary.servicebus.windows.net/myQueue**.

>[!NOTE]
> **Aktivní replikace** a **pasivní replikace** instalace jsou řešení pro obecné účely a není konkrétních funkcí služby Service Bus. Logiky replikace (odeslání na 2 různé obory názvů) se nachází na aplikace odesílatele a příjemce musí mít vlastní logiku pro zjišťování duplicit.

Pokud aplikace nevyžaduje komunikaci trvalé odesílatele příjemce, aplikace může implementovat trvalé fronty na straně klienta, aby se zabránilo ztrátě zpráv a chránit stíněním odesílatele z jakékoli přechodných chyb služby Service Bus.

### <a name="active-replication"></a>Aktivní replikace
Aktivní replikace používá entity v oba obory názvů pro všechny operace. Libovolného klienta, který odešle zprávu odesílá dvě kopie stejné zprávy. První kopie se odesílá do primární entity (například **contosoPrimary.servicebus.windows.net/sales**), a druhé kopie zprávy jsou odeslána do sekundární entity (například  **contosoSecondary.servicebus.windows.net/sales**).

Klient přijímá zprávy z front s oběma. Příjemce zpracovává první kopie zprávy a druhá kopie je potlačeno. Pro potlačení duplicitních zpráv, musíte označit odesílatel každá zpráva má jedinečný identifikátor. Obě kopie zprávy musí být označené se stejným identifikátorem. Můžete použít [BrokeredMessage.MessageId] [ BrokeredMessage.MessageId] nebo [BrokeredMessage.Label] [ BrokeredMessage.Label] vlastnosti nebo vlastní vlastnost má zpráva označit. Příjemce musí udržovat seznam zpráv, které již byl přijat.

[Geografické replikace ve standardní úrovni služby Service Bus] [ Geo-replication with Service Bus Standard Tier] ukázce aktivní replikace z entity pro zasílání zpráv.

> [!NOTE]
> Počet operací zdvojnásobuje přístup aktivní replikace, proto tento přístup může mít za následek vyšší náklady.
> 
> 

### <a name="passive-replication"></a>Pasivní replikace
V případě selhání bez pasivní replikace používá pouze jeden z těchto dvou entitách zasílání zpráv. Klient odešle zprávu do aktivní entity. Pokud operace na aktivní entity selhalo s kódem chyby, která určuje datové centrum, který je hostitelem aktivní entity mohou být k dispozici, klient odešle kopii zprávy zálohování entity. V tomto okamžiku aktivní a entity zálohování přepnout role: odesílání klienta považuje za starý aktivní entita, která má být nové entity zálohování a staré záložní entita je nová entita aktivní. Pokud obě odeslání nezdaří, zůstanou beze změny role dvě entity, a vrátí chybu.

Klient přijímá zprávy z front s oběma. Vzhledem k tomu, může se stát, že příjemce obdrží dvě kopie stejnou zprávu, příjemce musí potlačit duplicitní zprávy. Duplicitní položky můžete potlačit stejným způsobem, jak je popsáno pro aktivní replikace.

Obecně je levnější než replikace služby active pasivní replikace, protože ve většině případů je prováděna pouze jedna operace. Latence, propustnosti a peněžní náklady jsou stejné jako nereplikované scénář.

Při použití pasivní replikace v následujících scénářích zprávy ztráta nebo přijatých dvakrát:

* **Zpráva zpoždění nebo ztrátou**: Předpokládejme, že odesílatel zprávy m1 úspěšně odeslán do primární fronty, a potom fronty není k dispozici před příjemce dostane m1. Odesílatel odešle m2 následnou zprávu do sekundární fronty. Pokud se primární fronta je dočasně nedostupná, příjemce dostane m1 po fronty opět k dispozici. V případě havárie příjemce nikdy nedostane m1.
* **Duplicitní příjem**: Předpokládejme, že odesílatel odešle zprávu m do primární fronty. Service Bus úspěšně zpracuje m, ale selže k odeslání odpovědi. Jakmile vyprší časový limit operace send, odesílatel odešle totožnou kopii m sekundární fronty. Pokud je příjemce schopný přijímat prvního kopírování m předtím, než primární fronty přestane být k dispozici, příjemce obdrží obou kopiích m v přibližně ve stejnou dobu. Pokud příjemce nemůže přijímat prvního kopírování m předtím, než primární fronty přestane být k dispozici, příjemce zpočátku přijímá pouze druhé kopie m, ale pak obdrží kopii m druhý, jakmile je k dispozici primární fronty.

[Geografické replikace ve standardní úrovni služby Service Bus] [ Geo-replication with Service Bus Standard Tier] ukázce pasivní replikaci entity pro zasílání zpráv.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Ochrana koncových bodů přenosu proti výpadku datového centra nebo jiného problému ovlivňujícího
Geografická replikace koncových bodů přenosu umožňuje službu, která zpřístupňuje koncový bod předávání být dostupná za přítomnosti výpadky služby Service Bus. K dosažení geografickou replikaci, musíte službu vytvořit dva koncové body služby relay v různých oborech názvů. Obory názvů se musí nacházet v různých datových centrech a dva koncové body musí mít jedinečné názvy. Například můžete v části dostupný primární koncový bod **contosoPrimary.servicebus.windows.net/myPrimaryService**, ale jeho protějšek sekundární dostupné v rámci **contosoSecondary.servicebus.windows.net /mySecondaryService**.

Služba potom naslouchá na oba koncové body a klienta můžete vyvolat službu prostřednictvím buď koncový bod. Klientská aplikace náhodně vybere jeden z přenosy jako primární koncový bod a odešle žádost aktivní koncový bod. Pokud operace selže s chybovým kódem, tento selhání naznačuje, přenosový koncového bodu není k dispozici. Aplikace otevře kanál do záložního koncového bodu a znovu vydá požadavek. Od tohoto okamžiku aktivní a zálohování koncové body role přepnout: klientská aplikace bude považovat za původní aktivní koncový bod bude nový záložního koncového bodu a staré záložního koncového bodu na nový aktivní koncový bod. Pokud obě odeslání nezdaří, zůstanou beze změny role dvě entity, a vrátí chybu.

## <a name="next-steps"></a>Další postup
Další informace o zotavení po havárii, najdete v těchto článcích:

* [Azure Service Bus Geo-zotavení po havárii](service-bus-geo-dr.md)
* [Kontinuita podnikových procesů Azure SQL Database][Azure SQL Database Business Continuity]
* [Návrh aplikací Azure odolných proti chybám][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_MessageId
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage#Microsoft_ServiceBus_Messaging_BrokeredMessage_Label
[Geo-replication with Service Bus Standard Tier]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png