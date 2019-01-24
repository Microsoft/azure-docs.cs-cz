---
title: Vytvořit dělené fronty Azure Service Bus a témat | Dokumentace Microsoftu
description: Popisuje, jak rozdělit fronty a témata Service Bus s využitím více zprostředkovatele zpráv.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/06/2018
ms.author: aschhab
ms.openlocfilehash: 48b7d7450503b27b5515e655be3f048f57c2238d
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856583"
---
# <a name="partitioned-queues-and-topics"></a>Dělené fronty a témata

Azure Service Bus používá více zprostředkovatele zpráv pro zpracování zpráv a více úložišť pro přenos zpráv k uložení zpráv. Konvenční fronty nebo tématu je zpracovat zprostředkovatele a uložená do jednoho úložiště pro přenos zpráv. Service Bus *oddíly* povolit front a témat nebo *entity pro zasílání zpráv*, chcete-li být rozdělené mezi více zprostředkovatele zpráv a úložiště pro zasílání zpráv. Dělení znamená, že celkovou propustnost rozdělené entity je už omezený výkon zprostředkovatele nebo úložiště zpráv. Kromě toho dočasnému výpadku úložiště nevykresluje dělené fronty nebo tématu není k dispozici. Dělené fronty a témata mohou obsahovat všechny pokročilé funkce služby Service Bus, jako třeba podporu pro transakce a relací.

> [!NOTE]
> Dělení je k dispozici při vytvoření entity pro všechny fronty a témata v základní nebo standardní SKU. Není k dispozici pro zasílání zpráv SKU úrovně Premium, ale všechny dříve existující dělené entity v oborech názvů úrovně Premium i nadále fungovat podle očekávání.
 
Není možné změnit možnost rozdělení na jakékoli existující fronty nebo tématu; možnost lze nastavit pouze při vytvoření entity.

## <a name="how-it-works"></a>Jak to funguje

Každý dělená fronta nebo téma se skládá z několika fragmenty. Každý fragment je uložen v jiném úložišti zasílání zpráv a zpracovává jinou zprávu zprostředkovatele. Odeslání zprávy do dělené fronty nebo tématu Service Bus tuto zprávu přiřadí k jednomu fragmentů. Výběr se provádí náhodně ve službě Service Bus nebo s použitím klíče oddílu, můžete zadat odesílatele.

Když klient požaduje při příjmu zprávy z dělená fronta nebo přihlášení k odběru oddílů tématu, Service Bus dotazy všechny fragmentů zpráv, vrátí první zprávu, která se získá z některého z úložiště pro zasílání zpráv pro příjemce. Service Bus mezipamětí druhé zprávy a vrátí je při přijetí další přijímat požadavky. Přijímající klient nemá žádné informace o dělení; chování klienta dělené fronty nebo tématu (například číst, dokončete, odložit, nedoručených zpráv, předběžné načítání) je stejný jako chování regulárních entity.

Se neúčtují žádné další poplatky při odesílání zprávy nebo příjmu zprávy z dělená fronta nebo téma.

## <a name="enable-partitioning"></a>Povolit dělení

Dělené fronty a témata službou Azure Service Bus, pomocí sady Azure SDK verze 2.2 nebo vyšší, nebo zadejte `api-version=2013-10` nebo novějším žádostem protokolu HTTP.

### <a name="standard"></a>Standard

V zasílání zpráv úrovně Standard můžete vytvořit front služby Service Bus a témat v 1, 2, 3, 4 nebo velikosti 5 GB (výchozí hodnota je 1 GB). Service Bus s dělením povolená, vytvoří 4 kopie (4 oddíly) entity za každý GB zadáte. V důsledku toho pokud vytvoříte frontu, která velikost je 5 GB, s 4 oddíly maximální velikost fronty se stane (5 \* 4) = 20 GB. Zobrazí maximální velikost dělená fronta nebo téma zobrazením vstupu [webu Azure portal][Azure portal]v **přehled** okno pro danou entitu.

### <a name="premium"></a>Premium

V oboru názvů úrovně Premium není podporována dělení entit. Však můžete stále vytvořit front a témat Service Bus v 1, 2, 3, 4, 5, 10, 20, 40 nebo velikosti 80 GB (výchozí hodnota je 1 GB). Zobrazí velikost fronty nebo tématu zobrazením vstupu [webu Azure portal][Azure portal]v **přehled** okno pro danou entitu.

### <a name="create-a-partitioned-entity"></a>Vytvořit dělené entity

Existuje několik způsobů, jak vytvořit dělené fronty nebo tématu. Když vytvoříte frontu nebo téma z vaší aplikace, můžete povolit dělení pro fronty nebo tématu v uvedeném pořadí nastavením [QueueDescription.EnablePartitioning] [ QueueDescription.EnablePartitioning] nebo [ TopicDescription.EnablePartitioning] [ TopicDescription.EnablePartitioning] vlastnost **true**. Tyto vlastnosti musí být nastavena v době vytvoření fronty nebo tématu a jsou k dispozici pouze v starší [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) knihovny. Jak bylo uvedeno dříve, není možné změnit tyto vlastnosti na existující frontu nebo téma. Příklad:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Alternativně můžete vytvořit dělené fronty nebo tématu [webu Azure portal][Azure portal]. Při vytváření fronty nebo tématu na portálu **povolit dělení** možnost fronty nebo tématu **vytvořit** ve výchozím nastavení je zaškrtnuté políčko dialogového okna. Pouze můžete zakázat tuto možnost v úrovni Standard entity; na úrovni Premium se nepodporuje vytváření oddílů a zaškrtávacího políčka nemá žádný vliv. 

## <a name="use-of-partition-keys"></a>Použití klíče oddílu

Pokud zprávu ve frontě do dělené fronty nebo tématu, Service Bus zkontroluje přítomnosti určitého klíče oddílu. Pokud jeden najde, vybere fragment podle klíče. Pokud nelze najít klíč oddílu, vybere fragment založené na interním algoritmu.

### <a name="using-a-partition-key"></a>Použití klíče oddílu

Některé scénáře, jako je například relací nebo transakce, vyžadují zpráv k uložení do specifického fragmentu. Všechny tyto scénáře vyžadují použití klíče oddílu. Všechny zprávy, které používají stejný klíč oddílu jsou přiřazeny do stejného fragmentu. Pokud fragment je dočasně nedostupný, Service Bus vrátí chybu.

V závislosti na scénáři jiná zpráva vlastnosti slouží jako klíč oddílu:

**ID relace**: Pokud má zpráva [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) vlastnost nastavena a pak pomocí služby Service Bus **SessionID** jako klíč oddílu. Tímto způsobem všechny zprávy, které patří do stejné relace jsou zpracovávány stejného zprostředkovatele zpráv. Relace umožňují zajistit řazení a také konzistence stavy relací zpráv Service Bus.

**PartitionKey**: Pokud má zpráva [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) vlastností, ale ne [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) vlastnost nastavena a pak pomocí služby Service Bus [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) hodnota vlastnosti jako klíč oddílu. Pokud má zpráva i [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) sadu vlastností, obě vlastnosti musí být identické. Pokud [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) je nastavena na jinou hodnotu než [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) výjimky neplatné operace vrátí vlastnost, Service Bus. [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) vlastnost by měla použít, pokud odesílatel odešle vědět transakční zprávy mimo relaci. Klíč oddílu zajistí, že stejný zprostředkovatel zasílání zpráv zpracovává všechny zprávy odeslané v rámci transakce.

**ID zprávy**: Pokud má fronta nebo téma [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) vlastnost nastavena na **true** a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) nebo [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) nejsou nastavené vlastnosti, pak bude [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) hodnota vlastnosti slouží jako klíč oddílu. (Knihovny Microsoft .NET a AMQP automaticky přiřadit ID zprávy Pokud odesílací aplikace.) V takovém případě všechny kopie stejné zprávy zpracovávají stejným zprostředkovatele zpráv. Toto ID umožňuje detekovat a odstraňovat duplicitní zprávy služby Service Bus. Pokud [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) vlastnost není nastavena na **true**, nebere v úvahu služby Service Bus [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) vlastnost jako klíč oddílu.

### <a name="not-using-a-partition-key"></a>Bez použití klíče oddílu

Service Bus chybí klíč oddílu, distribuuje zprávy způsobem kruhové dotazování na tyto fragmenty dělená fronta nebo téma. Pokud není k dispozici zvolené fragment, Service Bus tuto zprávu přiřadí k různé fragment. Tímto způsobem, bez ohledu na dočasné nedostupnosti úložiště bude úspěšná operace odeslání. Nebudou však dosáhnout zaručené řazení, který obsahuje klíč oddílu.

Podrobnější diskuzi o kompromis mezi dostupností (žádný klíč oddílu) a konzistenci (pomocí klíče oddílu), najdete v článku [v tomto článku](../event-hubs/event-hubs-availability-and-consistency.md). Tyto informace se vztahuje stejnou měrou do dělené entity služby Service Bus.

Service Bus poskytnout dostatečný čas zařadit do fronty zprávy do různých fragment, [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) hodnotu zadanou pomocí klienta, která odesílá zprávy musí být větší než 15 sekund. Doporučuje se, že jste nastavili [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) vlastnost na výchozí hodnotu 60 sekund.

Klíč oddílu "připíná" zprávu do specifického fragmentu. Pokud není k dispozici úložiště pro přenos zpráv, který obsahuje tento fragment, Service Bus vrátí chybu. Chybí klíč oddílu Service Bus můžete vybrat jiný fragment a úspěšné operaci. Proto se doporučuje, pokud je vyžadován, není zadat klíč oddílu.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Pokročilá témata: používání transakcí s dělené entity

Zprávy odeslané v rámci transakce musí určovat klíč oddílu. Klíč může být jedna z následujících vlastností: [ID relace](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey), nebo [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid). Všechny zprávy odeslané v rámci stejné transakce musí určovat stejný klíč oddílu. Při pokusu o odeslání zprávy bez klíče oddílu v rámci transakce, Service Bus vrací výjimku neplatná operace. Pokud se pokusíte odeslat více zpráv v rámci jedné transakce, které mají různé klíče oddílů, Service Bus vrací výjimku neplatná operace. Příklad:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

Pokud nejsou nastavené žádné vlastnosti, které slouží jako klíč oddílu, připíná služby Service Bus zprávu, která se specifického fragmentu. K tomuto chování dochází, jestli se používá transakce. Je doporučeno, pokud není nutné není určovat klíč oddílu.

## <a name="using-sessions-with-partitioned-entities"></a>Použití relací s dělené entity

Pokud chcete poslat transakční zprávu s ohledem na relaci tématu nebo fronty, musí mít zpráva [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) sadu vlastností. Pokud [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) je také zadána vlastnost, musí být stejný jako [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) vlastnost. Pokud se liší, Service Bus vrací výjimku neplatná operace.

Na rozdíl od pravidelných (bez oddílů) front nebo témat není možné použít jen jednu transakci odeslat více zpráv na jiné relace. Při pokusu o, Service Bus vrací výjimku neplatná operace. Příklad:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.SessionId = "mySession";
    messageSender.SendAsync(msg); 
    ts.CompleteAsync();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Automatické přesměrování s zpráv segmentované entity

Service Bus podporuje automatické přesměrování z do a mezi dělené entity zpráv. Chcete-li povolit předávání automatické zpráv, nastavte [QueueDescription.ForwardTo] [ QueueDescription.ForwardTo] vlastnosti zdrojové fronty nebo odběru. Pokud zpráva Určuje klíč oddílu ([SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey), nebo [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)), tento klíč oddílu se používá pro Cílová entita.

## <a name="considerations-and-guidelines"></a>Důležité informace a pokyny
* **Funkce vysoké konzistence**: Pokud entita používá funkce, jako jsou relace, duplicit nebo explicitní kontrolu nad klíč rozdělení do oddílů, operací zasílání zpráv vždy směrovat na konkrétní fragmenty. Pokud některý z tyto fragmenty docházet k vysoké návštěvnosti nebo příslušné úložiště není v pořádku, tyto operace selhat a snížit dostupnost. Celkově je stále mnohem vyšší než bez oddílů entity; konzistence pouze podmnožinu provoz se setkává s problémy, na rozdíl od veškerý provoz. Další informace najdete v tomto [diskuzi o dostupnosti a konzistence](../event-hubs/event-hubs-availability-and-consistency.md).
* **Správa**: Operace, jako jsou Create, Update a Delete musí provést na tyto fragmenty entity. Pokud žádné fragmentu není v pořádku, může vést k selhání těchto operací. Pro operaci Get informace, jako je zpráva se počítá musí být agregován ze všech fragmenty. Pokud žádné fragmentu není v pořádku, stav dostupnosti entity se hlásí jako omezená.
* **Zpráva scénáře s nízkou**: Pro takové scénáře, zvláště při použití protokolu HTTP, možná budete muset provést více operací příjmu, aby bylo možné získat všechny zprávy. Front-endu pro příjem požadavků, provádí příjmu na tyto fragmenty a ukládá do mezipaměti všechny odpovědi přijata. Následné přijmout požadavek na stejné připojení by těžit z této ukládání do mezipaměti a přijímat bude nižší latenci. Ale pokud máte více připojení nebo prostřednictvím protokolu HTTP, který vytvoří nové připojení pro každý požadavek. V důsledku toho není k dispozici žádná záruka, že byste přejít na stejném uzlu. Pokud jsou všechny existující zprávy uzamčen a uložili do mezipaměti v jiném front-endu, operace obdržení vrátí **null**. Nakonec vyprší zprávy a můžete přijímat je znovu. Doporučuje se udržování připojení HTTP.
* **Procházení a prohlížení zpráv**: K dispozici pouze v starší [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) knihovny. [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) vždy nevrací počtu zpráv zadaného v [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) vlastnost. Existují dva běžné důvody pro toto chování. Jedním z důvodů je, že agregovaná velikost kolekce zpráv překračuje maximální velikost 256 KB. Dalším důvodem je, že pokud má fronty nebo tématu [EnablePartitioning vlastnost](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) nastavena na **true**, oddíl nemusí mít dostatek zprávy k dokončení požadovaný počet zpráv. Obecně platí, pokud aplikace chce přijímat určitého počtu zpráv, měla by volat [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) opakovaně, dokud získá daný počet zpráv, nebo nejsou žádné další zprávy pro náhled. Další informace, včetně ukázek kódu, najdete v článku [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) nebo [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) dokumentace k rozhraní API.

## <a name="latest-added-features"></a>Nejnovější přidaná funkce

* Přidat nebo odebrat pravidlo se teď podporuje s dělené entity. Liší od bez oddílů entity, tyto operace nejsou podporovány v rámci transakce. 
* AMQP se teď podporuje pro odesílání a příjem zpráv do a z rozdělené entity.
* AMQP se teď podporuje pro tyto operace: [Batch Send](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch), [Batch přijímat](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), [přijímat pořadové číslo](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [Náhled](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [obnovit uzamčení](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [plán Zpráva](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync), [zrušit naplánovanou zprávu](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync), [přidat pravidlo](/dotnet/api/microsoft.azure.servicebus.ruledescription), [odebrat pravidlo](/dotnet/api/microsoft.azure.servicebus.ruledescription), [obnovit uzamčení relace](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), [ Stav relace sady](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), [stav relace Get](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate), a [výčet relací](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Dělené entity omezení

Service Bus aktuálně ukládá tato omezení na dělené fronty a témata:

* Dělené fronty a témata nejsou podporované v zasílání zpráv úrovně Premium. Relace jsou podporovány v premier úrovně s použitím ID relace. 
* Dělené fronty a témata nepodporují odesílání zpráv, které patří do různých relacích v rámci jedné transakce.
* Service Bus aktuálně umožňuje v jednom oboru názvů až 100 dělených front nebo témat. Každý dělená fronta nebo téma započítává kvóta 10 000 entity na obor názvů (nevztahuje na úroveň Premium).

## <a name="next-steps"></a>Další postup

Přečtěte si informace o klíčových konceptech protokolu AMQP 1.0 specifikace v zasílání zpráv [Průvodce protokolem AMQP 1.0](service-bus-amqp-protocol-guide.md).

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md
