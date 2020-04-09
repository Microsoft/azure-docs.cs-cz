---
title: Izolujte aplikace Azure Service Bus proti výpadkům a katastrofám
description: Tento článek poskytuje techniky k ochraně aplikací před potenciálním výpadkem služby Azure Service Bus.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 01/27/2020
ms.author: aschhab
ms.openlocfilehash: 07b071b0e8efc5d664dada133a214d778c6531d0
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984942"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Osvědčené postupy pro ochranu aplikací před haváriemi a výpadky služby Service Bus

Kritické aplikace musí fungovat nepřetržitě, a to i za přítomnosti neplánovaných výpadků nebo katastrof. Tento článek popisuje techniky, které můžete použít k ochraně aplikací service bus proti možnému výpadku služby nebo havárii.

Výpadek je definován jako dočasná nedostupnost Azure Service Bus. Výpadek může ovlivnit některé součásti service bus, jako je například úložiště zpráv nebo dokonce celé datové centrum. Po ohlášení problému service bus bude znovu k dispozici. Výpadek obvykle nezpůsobí ztrátu zpráv nebo jiných dat. Příkladem selhání součásti je nedostupnost konkrétního úložiště zpráv. Příkladem výpadku celého datového centra je výpadek napájení datového centra nebo vadný síťový přepínač datového centra. Výpadek může trvat několik minut až několik dní.

Po havárii je definována jako trvalá ztráta jednotky škálování service bus nebo datového centra. Datové centrum může nebo nemusí být znovu k dispozici. Obvykle způsobit ztrátu některých nebo všech zpráv nebo jiných dat. Příkladem katastrof jsou požár, záplavy nebo zemětřesení.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Ochrana před výpadky a katastrofami - Service Bus Premium
Koncepty vysoké dostupnosti a zotavení po havárii jsou integrované přímo do úrovně Azure Service Bus Premium, a to jak ve stejné oblasti (prostřednictvím zón dostupnosti), tak napříč různými oblastmi (prostřednictvím geografického zotavení po havárii).

### <a name="geo-disaster-recovery"></a>Obnova geo-katastrofy

Service Bus Premium podporuje geografické zotavení po havárii na úrovni oboru názvů. Další informace najdete [v tématu Azure Service Bus Geo-disaster recovery](service-bus-geo-dr.md). Funkce zotavení po havárii, která je k dispozici pouze pro [výhodnou položku sku premium,](service-bus-premium-messaging.md) implementuje obnovení metadat po havárii a spoléhá na primární a sekundární obory názvů zotavení po havárii.

### <a name="availability-zones"></a>Zóny dostupnosti

Skladová položka Service Bus Premium podporuje [zóny dostupnosti](../availability-zones/az-overview.md)a poskytuje umístění izolovaná po chybám v rámci stejné oblasti Azure. Service Bus spravuje tři kopie úložiště zpráv (1 primární a 2 sekundární). Service Bus udržuje všechny tři kopie synchronizovány pro operace správy dat a správy. Pokud se primární kopie nezdaří, jedna ze sekundárních kopií je povýšena na primární bez vnímaných prostojů. Pokud aplikace vidět přechodné odpojí od Service Bus, logika opakování v sdk se automaticky znovu připojit k service bus. 

> [!NOTE]
> Podpora zón dostupnosti pro Azure Service Bus Premium je k dispozici jenom v [oblastech Azure,](../availability-zones/az-overview.md#services-support-by-region) kde jsou k dispozici zóny dostupnosti.

Zóny dostupnosti můžete povolit pouze v nových oborech názvů pomocí portálu Azure. Service Bus nepodporuje migraci existujících oborů názvů. Redundanci zóny nelze zakázat po povolení v oboru názvů.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Ochrana před výpadky a katastrofami - Standard servisní chod
K dosažení odolnosti proti výpadkům datového centra při použití standardní cenové úrovně zasílání zpráv service bus podporuje dva přístupy: *aktivní* a *pasivní* replikace. Pro každý přístup, pokud daná fronta nebo téma musí zůstat přístupné v přítomnosti výpadku datového centra, můžete jej vytvořit v obou oborech názvů. Obě entity mohou mít stejný název. Například primární fronty lze dosáhnout pod **contosoPrimary.servicebus.windows.net/myQueue**, zatímco jeho sekundární protějšek lze dosáhnout pod **contosoSecondary.servicebus.windows.net/myQueue**.

>[!NOTE]
> Nastavení **Aktivní replikace** a **Pasivní replikace** jsou řešení pro obecné účely a nikoli specifické funkce služby Service Bus. Logika replikace (odesílání do 2 různých oborů názvů) žije v aplikacích odesílatele a příjemce musí mít vlastní logiku pro vyhledávání duplicit.

Pokud aplikace nevyžaduje trvalou komunikaci odesílatele k příjemci, aplikace může implementovat trvalou frontu na straně klienta, aby se zabránilo ztrátě zprávy a chránilo odesílatele před chybami přechodné sběrnice.

### <a name="active-replication"></a>Aktivní replikace
Aktivní replikace používá entity v obou oborech názvů pro každou operaci. Každý klient, který odešle zprávu odešle dvě kopie stejné zprávy. První kopie je odeslána primární entitě (například **contosoPrimary.servicebus.windows.net/sales**) a druhá kopie zprávy je odeslána sekundární entitě (například **contosoSecondary.servicebus.windows.net/sales**).

Klient přijímá zprávy z obou front. Příjemce zpracuje první kopii zprávy a druhá kopie je potlačena. Chcete-li potlačit duplicitní zprávy, musí odesílatel označit každou zprávu jedinečným identifikátorem. Obě kopie zprávy musí být označeny stejným identifikátorem. Můžete použít [BrokeredMessage.MessageId][BrokeredMessage.MessageId] nebo [BrokeredMessage.Label][BrokeredMessage.Label] vlastnosti nebo vlastní vlastnost označit zprávu. Příjemce musí udržovat seznam zpráv, které již obdržel.

Ukázka [geografické replikace se standardní vrstvou služby Service Bus][Geo-replication with Service Bus Standard Tier] ukazuje aktivní replikaci entit zasílání zpráv.

> [!NOTE]
> Aktivní replikace přístup zdvojnásobí počet operací, proto tento přístup může vést k vyšším nákladům.
> 
> 

### <a name="passive-replication"></a>Pasivní replikace
V případě bez selhání pasivní replikace používá pouze jednu ze dvou entit zasílání zpráv. Klient odešle zprávu aktivní entitě. Pokud operace aktivní entity selže s kódem chyby, který označuje datové centrum, které hostuje aktivní entitu, může být nedostupné, klient odešle kopii zprávy do entity zálohování. V tomto okamžiku aktivní a záložní entity přepnout role: odesílající klient považuje staré aktivní entity za novou entitu zálohování a stará entita zálohování je nová aktivní entita. Pokud obě operace odesílání nezdaří, role dvou entit zůstávají beze změny a je vrácena chyba.

Klient přijímá zprávy z obou front. Vzhledem k tomu, že je pravděpodobné, že příjemce obdrží dvě kopie stejné zprávy, příjemce musí potlačit duplicitní zprávy. Duplikáty můžete potlačit stejným způsobem, jak je popsáno pro aktivní replikaci.

Pasivní replikace je obecně ekonomičtější než aktivní replikace, protože ve většině případů se provádí pouze jedna operace. Latence, propustnost a peněžní náklady jsou shodné s nereplikovaný scénář.

Při použití pasivní replikace mohou být v následujících scénářích zprávy ztraceny nebo přijaty dvakrát:

* **Zpoždění nebo ztráta zprávy**: Předpokládejme, že odesílatel úspěšně odeslal zprávu m1 do primární fronty a poté fronta nebude k dispozici dříve, než příjemce obdrží m1. Odesílatel odešle následující zprávu m2 do sekundární fronty. Pokud je primární fronta dočasně nedostupná, příjemce obdrží m1 poté, co bude fronta znovu k dispozici. V případě havárie nemusí přijímač nikdy přijímat m1.
* **Duplicitní příjem**: Předpokládejme, že odesílatel odešle zprávu m do primární fronty. Service Bus úspěšně zpracuje m, ale nepodaří odeslat odpověď. Po odestoujíte operace odeslání odešle odesílatel identickou kopii m do sekundární fronty. Pokud příjemce je schopen přijímat první kopii m před primární fronty přestane být k dispozici, příjemce obdrží obě kopie m přibližně ve stejnou dobu. Pokud příjemce není schopen přijmout první kopii m před primární fronty přestane být k dispozici, příjemce zpočátku obdrží pouze druhou kopii m, ale pak obdrží druhou kopii m, když primární fronty k dispozici.

Ukázka [geografické replikace se standardní vrstvou služby Service Bus][Geo-replication with Service Bus Standard Tier] ukazuje pasivní replikaci entit zasílání zpráv.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Ochrana koncových bodů přenosu před výpadky nebo katastrofami datového centra
Geografická replikace koncových bodů [Azure Relay](../service-bus-relay/relay-what-is-it.md) umožňuje službu, která zpřístupňuje koncový bod přenosu k dispozici v přítomnosti výpadků service bus. Chcete-li dosáhnout geografické replikace, služba musí vytvořit dva koncové body přenosu v různých oborech názvů. Obory názvů musí být umístěny v různých datových centrech a dva koncové body musí mít různé názvy. Primárního koncového bodu lze například dosáhnout pod **contosoPrimary.servicebus.windows.net/myPrimaryService**, zatímco jeho sekundární protějšek lze dosáhnout pod **contosoSecondary.servicebus.windows.net/mySecondaryService**.

Služba pak naslouchá na obou koncových bodech a klient může vyvolat službu prostřednictvím obou koncových bodů. Klientská aplikace náhodně vybere jeden z přenosů jako primární koncový bod a odešle svůj požadavek do aktivního koncového bodu. Pokud se operace nezdaří s kódem chyby, tato chyba označuje, že koncový bod přenosu není k dispozici. Aplikace otevře kanál do koncového bodu zálohování a znovu vydá požadavek. V tomto okamžiku aktivní a záložní koncové body přepnout role: klientská aplikace považuje starý aktivní koncový bod za nový koncový bod zálohování a starý koncový bod zálohování za nový aktivní koncový bod. Pokud obě operace odesílání nezdaří, role dvou entit zůstávají beze změny a je vrácena chyba.

## <a name="next-steps"></a>Další kroky
Další informace o zotavení po havárii najdete v těchto článcích:

* [Geografické zotavení azure service bus](service-bus-geo-dr.md)
* [Kontinuita provozu databáze Azure SQL][Azure SQL Database Business Continuity]
* [Návrh odolných aplikací pro Azure][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Geo-replication with Service Bus Standard Tier]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]: ../sql-database/sql-database-business-continuity.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png
