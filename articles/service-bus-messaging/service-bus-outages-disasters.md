---
title: Izolování Azure Service Bus aplikací proti výpadkům a katastrofám
description: V tomto článku najdete techniky ochrany aplikací proti možnému výpadku Azure Service Bus.
ms.topic: article
ms.date: 02/10/2021
ms.openlocfilehash: b9090a54cd58788dbd13f528af4dda4aa96005b7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100374588"
---
# <a name="best-practices-for-insulating-applications-against-service-bus-outages-and-disasters"></a>Osvědčené postupy pro ochranu aplikací před haváriemi a výpadky služby Service Bus

Klíčové aplikace musí fungovat průběžně, a to i v případě neplánovaných výpadků nebo havárií. Tento článek popisuje postupy, které můžete použít k ochraně Service Bus aplikací proti možnému výpadku nebo havárii služby.

Výpadek je definován jako dočasná nedostupnost Azure Service Bus. Výpadek může ovlivnit některé součásti Service Bus, jako je třeba úložiště pro zasílání zpráv nebo i celé datové centrum. Po vyřešení problému bude Service Bus opět k dispozici. Výpadky obvykle nezpůsobí ztrátu zpráv nebo jiných dat. Příkladem selhání součásti je nedostupnost konkrétního úložiště pro zasílání zpráv. Příkladem výpadku v rámci datového centra je selhání výpadku datového centra nebo chybný přepínač sítě datového centra. Výpadek může trvat několik minut až pár dní.

Havárie se definuje jako trvalá ztráta jednotky škálování Service Bus nebo datacentra. Datové centrum může nebo nemusí být k dispozici znovu. Obvykle dojde ke ztrátě některých nebo všech zpráv nebo jiných dat. Příklady havárií jsou požáry, zahlcení nebo zemětřesení.

## <a name="protecting-against-outages-and-disasters---service-bus-premium"></a>Ochrana před výpadky a haváriemi – Service Bus Premium
Koncepce vysoké dostupnosti a zotavení po havárii jsou postaveny přímo do Azure Service Bus úrovně Premium, jak v rámci stejné oblasti (přes Zóny dostupnosti), tak i napříč různými oblastmi (prostřednictvím Geo-Disaster obnovení).

### <a name="geo-disaster-recovery"></a>Geo-Disaster obnovení

Service Bus Premium podporuje obnovení geografických havárií na úrovni oboru názvů. Další informace najdete v tématu [Azure Service Busho geografického zotavení po havárii](service-bus-geo-dr.md). Funkce zotavení po havárii, která je dostupná jenom pro [SKU Premium](service-bus-premium-messaging.md) , implementuje zotavení po havárii metadat a spoléhá na primární a sekundární obory názvů pro zotavení po havárii. Při obnovení Geo-Disaster se mezi primárními a sekundárními obory názvů replikují jenom metadata pro entity.  

### <a name="availability-zones"></a>Zóny dostupnosti

SKU Service Bus Premium podporuje [zóny dostupnosti](../availability-zones/az-overview.md)a poskytuje umístění s izolací chyb v rámci stejné oblasti Azure. Service Bus spravuje tři kopie úložiště pro zasílání zpráv (1 primární a 2 sekundární). Service Bus udržuje všechny tři kopie synchronizovány s daty a operacemi správy. Pokud primární kopie neproběhne úspěšně, jedna ze sekundárních kopií bude povýšena na primární bez pozorovaného výpadku. Pokud se aplikace zobrazí jako přechodné odpojení od Service Bus, logika opakování v sadě SDK se automaticky znovu připojí k Service Bus. 

Když použijete zóny dostupnosti, metadata i data (zprávy) se replikují napříč datovými centry v zóně dostupnosti. 

> [!NOTE]
> Podpora Zóny dostupnosti pro Azure Service Bus Premium je dostupná jenom v [oblastech Azure](../availability-zones/az-region.md) , kde se nacházejí zóny dostupnosti.

Zóny dostupnosti můžete povolit jenom pro nové obory názvů pomocí Azure Portal. Service Bus nepodporuje migraci stávajících oborů názvů. Po povolení v oboru názvů nelze zakázat redundanci zóny.

![1][]


## <a name="protecting-against-outages-and-disasters---service-bus-standard"></a>Ochrana před výpadky a haváriemi – Service Bus Standard
Pro zajištění odolnosti proti výpadkům datového centra při použití cenové úrovně Standard Messaging Service Bus podporuje dva přístupy: *aktivní* a *pasivní* replikace. Pro každý přístup, pokud daná fronta nebo téma musí zůstat přístupné v případě výpadku datového centra, můžete ho vytvořit v obou oborech názvů. Obě entity můžou mít stejný název. Například primární frontu lze dosáhnout v rámci **contosoPrimary.ServiceBus.Windows.NET/myQueue**, zatímco její sekundární protějšek lze dosáhnout v rámci **contosoSecondary.ServiceBus.Windows.NET/myQueue**.

>[!NOTE]
> **Aktivní replikace** a nastavení **pasivní replikace** jsou řešení pro obecné účely a ne konkrétní funkce Service Bus. Logika replikace (odesílání do dvou různých oborů názvů) v aplikacích odesílatele a přijímači musí mít vlastní logiku pro detekci duplicit.

Pokud aplikace nevyžaduje trvalou komunikaci od odesílatele k přijímači, může aplikace implementovat trvalou frontu na straně klienta, aby se zabránilo ztrátě zpráv a musela chránit odesílatele před případnými přechodnými Service Busmi chybami.

### <a name="active-replication"></a>Aktivní replikace
Aktivní replikace používá pro každou operaci entity v obou oborech názvů. Každý klient, který odesílá zprávu, odesílá dvě kopie stejné zprávy. První kopii pošle primární entitě (například **contosoPrimary.ServiceBus.Windows.NET/Sales**) a druhá kopie zprávy se pošle sekundární entitě (třeba **contosoSecondary.ServiceBus.Windows.NET/Sales**).

Klient přijímá zprávy z obou front. Příjemce zpracuje první kopii zprávy a druhá kopie se potlačí. Chcete-li potlačit duplicitní zprávy, musí odesílatel označit každou zprávu jedinečným identifikátorem. Obě kopie zprávy musí být označené stejným identifikátorem. Můžete použít vlastnosti [BrokeredMessage. MessageID][BrokeredMessage.MessageId] nebo [BrokeredMessage. Label][BrokeredMessage.Label] nebo vlastní vlastnost k označení zprávy. Přijímač musí udržovat seznam zpráv, které již byly přijaty.

Ukázka [geografické replikace s Service Bus standardní vrstva][Geo-replication with Service Bus Standard Tier] demonstruje aktivní replikaci entit zasílání zpráv.

> [!NOTE]
> Aktivní replikační přístup zdvojnásobuje počet operací, takže tento přístup může mít za následek vyšší náklady.
> 
> 

### <a name="passive-replication"></a>Pasivní replikace
V případě nenáročného selhání používá pasivní replikace jenom jednu ze dvou entit zasílání zpráv. Klient odešle zprávu na aktivní entitu. Pokud se operace s aktivní entitou nezdařila s kódem chyby, který indikuje, že datové centrum hostující aktivní entitě nemusí být k dispozici, klient pošle kopii zprávy do zálohovací entity. V tomto okamžiku role přepínače aktivní a entity zálohování: klient odesílajícího klienta považuje starou aktivní entitu za novou entitu zálohování a stará entita zálohování je novou aktivní entitou. Pokud operace odeslání selže, role dvou entit zůstanou beze změny a vrátí se chyba.

Klient přijímá zprávy z obou front. Vzhledem k tomu, že je pravděpodobné, že příjemce obdrží dvě kopie stejné zprávy, musí příjemce potlačit duplicitní zprávy. Duplicity můžete potlačit stejným způsobem, jak je popsáno v tématu aktivní replikace.

Obecně platí, že pasivní replikace je výhodnější než aktivní replikace, protože ve většině případů je provedena pouze jedna operace. Latence, propustnost a peněžní náklady jsou stejné jako nereplikovaný scénář.

Při použití pasivní replikace mohou být zprávy v následujících scénářích ztraceny nebo přijaty dvakrát:

* **Zpoždění a ztráta zprávy**: předpokládá, že odesílatel úspěšně odeslal zprávu M1 do primární fronty a dokud příjemce neobdrží M1, fronta nebude k dispozici. Odesílatel pošle další zprávu m2 do sekundární fronty. Pokud je primární fronta dočasně nedostupná, příjemce obdrží M1, jakmile bude fronta znovu dostupná. V případě havárie nemusí přijímač nikdy přijímat M1.
* **Duplicitní příjem**: předpokládá, že odesílatel pošle do primární fronty zprávu m. Service Bus úspěšně zpracovala m, ale nedokáže odeslat odpověď. Po vypršení časového limitu operace odeslání pošle odesílateli identickou kopii m do sekundární fronty. Pokud příjemce může obdržet první kopii m, než bude primární fronta k dispozici, příjemce obdrží obě kopie m ve stejnou dobu. Pokud příjemce nedokáže získat první kopii m, než bude primární fronta k dispozici, příjemce zpočátku obdrží pouze druhou kopii m, ale obdrží druhou kopii m, když bude primární fronta k dispozici.

Ukázka [geografické replikace s Service Bus standardní vrstva][Geo-replication with Service Bus Standard Tier] demonstruje pasivní replikaci entit zasílání zpráv.

## <a name="protecting-relay-endpoints-against-datacenter-outages-or-disasters"></a>Ochrana koncových bodů Relay proti výpadkům Datacenter nebo haváriím
Geografická replikace koncových bodů [Azure Relay](../azure-relay/relay-what-is-it.md) umožňuje službě, která zpřístupňuje koncový bod přenosu, dosažitelná v přítomnosti Service Bus výpadků. Aby bylo možné zajistit geografickou replikaci, musí služba vytvořit dva koncové body přenosu v různých oborech názvů. Obory názvů se musí nacházet v různých datových centrech a dva koncové body musí mít jiné názvy. Například primární koncový bod je možné dosáhnout v rámci **contosoPrimary.ServiceBus.Windows.NET/myPrimaryService**, zatímco jeho sekundární protějšek je možné dosáhnout v rámci **contosoSecondary.ServiceBus.Windows.NET/mySecondaryService**.

Služba pak naslouchá oběma koncovým bodům a klient může službu vyvolat prostřednictvím libovolného koncového bodu. Klientská aplikace náhodně vybere jeden z přenosů jako primární koncový bod a pošle svůj požadavek do aktivního koncového bodu. Pokud operace selže s kódem chyby, znamená to, že koncový bod Relay není k dispozici. Aplikace otevře kanál na koncový bod zálohy a znovu vydá požadavek. V tomto okamžiku role přepínače aktivní a koncové body zálohování: klientská aplikace považuje starý aktivní koncový bod za nový koncový bod zálohy a starý koncový bod zálohy bude novým aktivním koncovým bodem. Pokud operace odeslání selže, role dvou entit zůstanou beze změny a vrátí se chyba.

## <a name="next-steps"></a>Další kroky
Další informace o zotavení po havárii najdete v těchto článcích:

* [Azure Service Bus geografické zotavení po havárii](service-bus-geo-dr.md)
* [Azure SQL Database provozní kontinuita][Azure SQL Database Business Continuity]
* [Návrh odolných aplikací pro Azure][Azure resiliency technical guidance]

[Service Bus Authentication]: service-bus-authentication-and-authorization.md
[Partitioned messaging entities]: service-bus-partitioning.md
[Asynchronous messaging patterns and high availability]: service-bus-async-messaging.md#failure-of-service-bus-within-an-azure-datacenter
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[BrokeredMessage.Label]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[Geo-replication with Service Bus Standard Tier]: https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/GeoReplication
[Azure SQL Database Business Continuity]:../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md
[Azure resiliency technical guidance]: /azure/architecture/resiliency

[1]: ./media/service-bus-outages-disasters/az.png