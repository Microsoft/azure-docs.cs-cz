---
title: Vytvoření rozdělených front a témat služby Azure Service Bus | Dokumenty společnosti Microsoft
description: Popisuje, jak rozdělit fronty a témata service bus pomocí více zprostředkovatelů zpráv.
services: service-bus-messaging
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 02/06/2020
ms.author: aschhab
ms.openlocfilehash: 671368993acb43c0d55eca73119effa934e3cff8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260940"
---
# <a name="partitioned-queues-and-topics"></a>Dělené fronty a témata

Azure Service Bus zaměstnává více zprostředkovatelů zpráv ke zpracování zpráv a více úložišť zpráv pro ukládání zpráv. Konvenční fronty nebo tématu je zpracována jeden zprostředkovatel zpráv a uloženy v jednom úložišti zasílání zpráv. Oddíly *služby* Service Bus umožňují fronty a témata nebo *zasílání zpráv entity*, které mají být rozděleny do oddílů mezi více zprostředkovatelů zpráv a úložiště zpráv. Dělení znamená, že celková propustnost dělené entity již není omezena výkonem zprostředkovatele jedné zprávy nebo úložiště zasílání zpráv. Dočasný výpadek úložiště zpráv navíc neznemožní rozdělení rozdělené fronty nebo tématu. Rozdělené fronty a témata mohou obsahovat všechny pokročilé funkce služby Service Bus, jako je například podpora transakcí a relací.

> [!NOTE]
> Dělení je k dispozici při vytváření entit pro všechny fronty a témata v základních nebo standardních skum. Není k dispozici pro sku zasílání zpráv Premium, ale všechny dříve existující oddíly entity v oborech názvů Premium nadále fungovat podle očekávání.
 
Není možné změnit možnost dělení na existující frontu nebo téma; Tuto možnost můžete nastavit pouze při vytváření entity.

## <a name="how-it-works"></a>Jak to funguje

Každá rozdělená fronta nebo téma se skládá z více oddílů. Každý oddíl je uložen v jiném úložišti zpráv a zpracovány jiným zprostředkovatelem zpráv. Při odeslání zprávy do dělené fronty nebo tématu service bus přiřadí zprávu do jednoho z oddílů. Výběr se provádí náhodně pomocí služby Service Bus nebo pomocí klíče oddílu, který může zadávat odesílatel.

Pokud klient chce přijímat zprávy z rozdělené fronty nebo z odběr na rozdělené téma, Service Bus dotazuje všechny oddíly pro zprávy a potom vrátí první zprávu, která je získána z libovolného úložiště zpráv příjemci. Service Bus ukládá do mezipaměti ostatní zprávy a vrátí je, když obdrží další požadavky na příjem. Přijímající klient si není vědom dělení; chování rozdělené fronty nebo tématu orientovaného na klienta (například čtení, dokončení, odložení, nedoručené, předběžné načítání) je shodné s chováním běžné entity.

Při odesílání zprávy do dělené fronty nebo tématu nebo při přijímání zprávy z dělené fronty nebo tématu se neplatí žádné další náklady.

## <a name="enable-partitioning"></a>Povolení dělení

Pokud chcete pomocí Služby Azure Service Bus používat dělené fronty a témata, `api-version=2013-10` použijte azure sdk verzi 2.2 nebo novější nebo zadejte nebo novější v požadavcích HTTP.

### <a name="standard"></a>Standard

Ve vrstvě Standardní zasílání zpráv můžete vytvořit fronty a témata služby Service Bus ve velikostech 1, 2, 3, 4 nebo 5 GB (výchozí hodnota je 1 GB). S rozdělením povoleno Service Bus vytvoří 16 kopií (16 oddílů) entity, každý stejné velikosti zadané. Pokud tedy vytvoříte frontu o velikosti 5 GB s 16 oddíly, \* maximální velikost fronty se stane (5 16) = 80 GB. Maximální velikost rozdělené fronty nebo tématu najdete v jeho vstupu na [webu Azure Portal][Azure portal]v okně **Přehled** pro tuto entitu.

### <a name="premium"></a>Premium

V oboru názvů úrovně Premium nejsou podporovány entity dělení. Stále však můžete vytvořit fronty a témata služby Service Bus ve velikostech 1, 2, 3, 4, 5, 10, 20, 40 nebo 80 GB (výchozí hodnota je 1 GB). Velikost fronty nebo tématu najdete v jeho vstupu na [webu Azure Portal][Azure portal]v okně **Přehled** pro tuto entitu.

### <a name="create-a-partitioned-entity"></a>Vytvoření rozdělené entity

Existuje několik způsobů, jak vytvořit rozdělenou frontu nebo téma. Při vytváření fronty nebo tématu z aplikace můžete povolit dělení fronty nebo tématu nastavením [vlastnosti QueueDescription.EnablePartitioning][QueueDescription.EnablePartitioning] nebo [TopicDescription.EnablePartitioning][TopicDescription.EnablePartitioning] na **true**. Tyto vlastnosti musí být nastaveny v době vytvoření fronty nebo tématu a jsou k dispozici pouze ve starší knihovně [WindowsAzure.ServiceBus.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) Jak již bylo uvedeno dříve, není možné změnit tyto vlastnosti na existující fronty nebo tématu. Například:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Případně můžete vytvořit rozdělenou frontu nebo téma na [webu Azure Portal][Azure portal]. Když na portálu vytvoříte frontu nebo téma, je ve výchozím nastavení zaškrtnuta možnost **Povolit dělení** v dialogovém okně **Vytvořit** ve frontě nebo tématu. Tuto možnost můžete zakázat pouze v entitě úrovně Standard. v oddílování úrovně Premium není podporováno a zaškrtávací políčko nemá žádný vliv. 

## <a name="use-of-partition-keys"></a>Použití klíčů oddílů

Pokud je zpráva zařazena do fronty nebo tématu s oddíly, služba Service Bus zkontroluje přítomnost klíče oddílu. Pokud najde jeden, vybere oddíl na základě tohoto klíče. Pokud nenajde klíč oddílu, vybere oddíl na základě interního algoritmu.

### <a name="using-a-partition-key"></a>Použití klíče oddílu

Některé scénáře, například relace nebo transakce, vyžadují zprávy, které mají být uloženy v určitém oddílu. Všechny tyto scénáře vyžadují použití klíče oddílu. Všechny zprávy, které používají stejný klíč oddílu jsou přiřazeny ke stejnému oddílu. Pokud je oddíl dočasně nedostupný, vrátí služba Service Bus chybu.

V závislosti na scénáři se jako klíč oddílu používají různé vlastnosti zprávy:

**SessionId**: Pokud má zpráva nastavenou vlastnost [SessionId,](/dotnet/api/microsoft.azure.servicebus.message.sessionid) pak Service Bus používá **SessionID** jako klíč oddílu. Tímto způsobem všechny zprávy, které patří do stejné relace jsou zpracovány stejné zprostředkovatele zpráv. Relace povolit Service Bus zaručit řazení zpráv, jakož i konzistence stavů relace.

**PartitionKey**: Pokud zpráva má [Vlastnost PartitionKey,](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) ale ne [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) vlastnost nastavit, pak Service Bus používá [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) hodnotu vlastnosti jako klíč oddílu. Pokud zpráva má [sessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) vlastnosti nastavit, obě vlastnosti musí být identické. Pokud [partitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) vlastnost je nastavena na jinou hodnotu než [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) vlastnost, Service Bus vrátí neplatnou výjimku operace. [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) Vlastnost by měla být použita, pokud odesílatel odešle nerelace vědomi transakční zprávy. Klíč oddílu zajišťuje, že všechny zprávy, které jsou odesílány v rámci transakce jsou zpracovány stejným zprostředkovatelem zasílání zpráv.

**MessageId**: Pokud má fronta nebo téma vlastnost [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) nastavenou na **hodnotu true** a [vlastnosti SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) nebo [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) nejsou nastaveny, slouží hodnota vlastnosti [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) jako klíč oddílu. (Knihovny Microsoft .NET a AMQP automaticky přiřadí ID zprávy, pokud odesílající aplikace nikoli.) V tomto případě jsou všechny kopie stejné zprávy zpracovány stejným zprostředkovatelem zpráv. Toto ID umožňuje service bus zjistit a odstranit duplicitní zprávy. Pokud [vlastnost RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) není nastavena na **hodnotu true**, služba Service Bus nepovažuje vlastnost [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) za klíč oddílu.

### <a name="not-using-a-partition-key"></a>Nepoužití klíče oddílu

V případě, že klíč oddílu Service Bus distribuuje zprávy způsobem kruhového dotazování na všechny oddíly rozdělené fronty nebo tématu. Pokud vybraný oddíl není k dispozici, service bus přiřadí zprávu do jiného oddílu. Tímto způsobem operace odeslání úspěšné i přes dočasnou nedostupnost úložiště zpráv. Však nedosáhnete zaručené řazení, které klíč oddílu poskytuje.

Podrobnější diskusi o kompromisu mezi dostupností (bez klíče oddílu) a konzistencí (pomocí klíče oddílu) naleznete v [tomto článku](../event-hubs/event-hubs-availability-and-consistency.md). Tyto informace platí stejně pro oddíly Service Bus entity.

Chcete-li poskytnout service bus dostatek času na zařazení zprávy do jiného oddílu, musí být hodnota [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) zadaná klientem, který zprávu odesílá, větší než 15 sekund. Doporučujeme nastavit [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) vlastnost výchozí hodnotu 60 sekund.

Klíč oddílu "připne" zprávu do určitého oddílu. Pokud úložiště zasílání zpráv, který obsahuje tento oddíl není k dispozici, Service Bus vrátí chybu. V případě, že klíč oddílu service bus můžete zvolit jiný oddíl a operace proběhne úspěšně. Proto se doporučuje nezadávat klíč oddílu, pokud je požadováno.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Pokročilá témata: použití transakcí s rozdělenými entitami

Zprávy odeslané v rámci transakce musí určovat klíč oddílu. Klíč může být jedna z následujících vlastností: [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)nebo [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid). Všechny zprávy, které jsou odesílány jako součást stejné transakce, musí zadat stejný klíč oddílu. Pokud se pokusíte odeslat zprávu bez klíče oddílu v rámci transakce, Service Bus vrátí neplatnou výjimku operace. Pokud se pokusíte odeslat více zpráv v rámci stejné transakce, které mají různé klíče oddílu, Service Bus vrátí neplatnou výjimku operace. Například:

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

Pokud jsou nastaveny některé z vlastností, které slouží jako klíč oddílu, Service Bus připne zprávu na konkrétní oddíl. K tomuto chování dochází, zda je použita transakce. Pokud to není nutné, doporučujeme nezadávat klíč oddílu.

## <a name="using-sessions-with-partitioned-entities"></a>Použití relací s rozdělenými entitami

Chcete-li odeslat transakční zprávu na téma nebo frontu podporující relaci, musí mít nastavena vlastnost [SessionId.](/dotnet/api/microsoft.azure.servicebus.message.sessionid) Pokud [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) vlastnost je zadán také, musí být shodné [s SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) vlastnost. Pokud se liší, service bus vrátí neplatnou výjimku operace.

Na rozdíl od běžných (nerozdělených) front nebo témat není možné použít jednu transakci k odeslání více zpráv do různých relací. Pokud se o to pokusíte, vrátí služba Service Bus neplatnou výjimku operace. Například:

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

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Automatické předávání zpráv s rozdělenými entitami

Service Bus podporuje automatické předávání zpráv z, do nebo mezi rozdělenými entitami. Chcete-li povolit automatické předávání zpráv, nastavte vlastnost [QueueDescription.ForwardTo][QueueDescription.ForwardTo] ve zdrojové frontě nebo předplatném. Pokud zpráva určuje klíč oddílu ([SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)nebo [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)), tento klíč oddílu se používá pro cílovou entitu.

## <a name="considerations-and-guidelines"></a>Úvahy a pokyny
* **Funkce vysoké konzistence**: Pokud entita používá funkce, jako jsou relace, vyhledávání duplicit nebo explicitní řízení klíče dělení, jsou operace zasílání zpráv vždy směrovány do určitého oddílu. Pokud některý z oddílů dojít k vysokému provozu nebo základní úložiště není v pořádku, tyto operace nezdaří a dostupnost je snížena. Celkově je konzistence stále mnohem vyšší než nerozdělené entity; pouze podmnožina provozu dochází k problémům, na rozdíl od všech provozu. Další informace naleznete v této [diskusi o dostupnosti a konzistenci](../event-hubs/event-hubs-availability-and-consistency.md).
* **Správa**: Operace, jako je například Vytvořit, Aktualizovat a Odstranit, musí být provedeny na všech oddílech entity. Pokud některý oddíl není v pořádku, může mít za následek selhání pro tyto operace. Pro get operace informace, jako je počet zpráv musí být agregovány ze všech oddílů. Pokud některý oddíl není v pořádku, stav dostupnosti entity je hlášena jako omezená.
* **Scénáře zpráv s nízkým objemem**: Pro takové scénáře, zejména při použití protokolu HTTP, bude pravděpodobně muset provést více operací příjmu, aby bylo možné získat všechny zprávy. Pro požadavky na příjem front-end provádí příjem na všech oddílech a ukládá všechny přijaté odpovědi. Následná žádost o přijetí na stejné připojení bude mít prospěch z tohoto ukládání do mezipaměti a latence příjmu bude nižší. Pokud však máte více připojení nebo používáte protokol HTTP, naváže nové připojení pro každý požadavek. Jako takový neexistuje žádná záruka, že by přistát na stejném uzlu. Pokud jsou všechny existující zprávy uzamčeny a uloženy do mezipaměti v jiném front-endu, vrátí operace příjmu **hodnotu null**. Platnost zpráv nakonec vyprší a můžete je znovu obdržet. Doporučuje se udržovat při životě HTTP. Při použití dělení ve scénářích s nízkým objemem může operace příjmu trvat déle, než bylo očekáváno. Proto doporučujeme nepoužívat dělení v těchto scénářích. Odstraňte všechny existující dělené entity a znovu je vytvořte se zakázaným dělením, abyste zvýšili výkon.
* **Procházet a prohlížet zprávy**: K dispozici pouze ve starší knihovně [WindowsAzure.ServiceBus.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) ne vždy vrátí počet zpráv zadaných v [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) vlastnost. Existují dva běžné důvody pro toto chování. Jedním z důvodů je, že agregovaná velikost kolekce zpráv překračuje maximální velikost 256 KB. Dalším důvodem je, že pokud fronta nebo téma má [EnablePartitioning vlastnost](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) nastavena na **hodnotu true**, oddíl nemusí mít dostatek zpráv k dokončení požadovaný počet zpráv. Obecně platí, že pokud aplikace chce přijímat určitý počet zpráv, by měla volat [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) opakovaně, dokud získá tento počet zpráv nebo neexistují žádné další zprávy ke shlédnutí. Další informace, včetně ukázek kódu, naleznete v dokumentaci k rozhraní [API QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) nebo [SubscriptionClient.PeekBatch.](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch)

## <a name="latest-added-features"></a>Nejnovější přidané funkce

* Přidat nebo odebrat pravidlo je nyní podporováno s rozdělenými entitami. Na rozdíl od nerozdělených entit nejsou tyto operace podporovány v rámci transakcí. 
* AMQP je nyní podporován pro odesílání a přijímání zpráv do a z dělené entity.
* AMQP je nyní podporován pro následující operace: [Dávkové odesílání,](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch) [Dávkové příjem,](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch) [Příjem podle pořadového čísla](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [Náhled](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [Obnovit zámek](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [Naplánovat zprávu](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync), Zrušit [naplánovanou zprávu](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync), Přidat [pravidlo](/dotnet/api/microsoft.azure.servicebus.ruledescription), [Odebrat pravidlo](/dotnet/api/microsoft.azure.servicebus.ruledescription), Zámek obnovení [relace](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), Nastavit [stav relace](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), Získat stav [relace](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate)a [Výčet relací](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Omezení oddílů entit

V současné době service bus ukládá následující omezení na rozdělené fronty a témata:

* Dělené fronty a témata nejsou podporovány ve vrstvě zasílání zpráv Premium. Relace jsou podporovány v první vrstvě pomocí SessionId. 
* Dělené fronty a témata nepodporují odesílání zpráv, které patří do různých relací v jedné transakci.
* Service Bus aktuálně umožňuje v jednom oboru názvů až 100 dělených front nebo témat. Každá rozdělená fronta nebo téma se započítává do kvóty 10 000 entit na obor názvů (nevztahuje se na úroveň Premium).

## <a name="next-steps"></a>Další kroky

Přečtěte si o základních konceptech specifikace zasílání zpráv AMQP 1.0 v [průvodci protokolem AMQP 1.0](service-bus-amqp-protocol-guide.md).

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md
