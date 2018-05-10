---
title: Vytvoření oddílů fronty Azure Service Bus a témat | Microsoft Docs
description: Popisuje, jak při vytváření oddílů témata a fronty Service Bus pomocí více zprostředkovatelé zprávy.
services: service-bus-messaging
author: sethmanheim
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 05/08/2016
ms.author: sethm
ms.openlocfilehash: 0759decec9d80f1f836110a8907049213ca1eed6
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="partitioned-queues-and-topics"></a>Dělené fronty a témata

Azure Service Bus používá více zpráv zprostředkovatelé ke zpracování zpráv a více úložiště pro zasílání zpráv pro ukládání zpráv. Konvenční fronta nebo téma zpracování zprostředkovatelem jedné zprávy a uloženy v úložišti jeden zasílání zpráv. Service Bus *oddíly* povolit front a témat, nebo *entity pro zasílání zpráv*, k rozdělení na oddíly na více zpráv zprostředkovatelé a úložiště pro zasílání zpráv. Vytváření oddílů znamená, že celkovou propustnost dělené entity je již omezena výkon zprostředkovatele jedné zprávy nebo úložišti pro přenos zpráv. Kromě toho dočasnému výpadku zasílání zpráv úložiště nevykresluje oddílů fronta nebo téma není k dispozici. Oddílů fronty a témata může obsahovat všechny pokročilé funkce služby Service Bus, například podporu transakce a relací.

Informace o interní informace o Service Bus, najdete v článku [architektura služby Service Bus] [ Service Bus architecture] článku.

> [!NOTE]
> Vytváření oddílů je k dispozici při vytváření entity pro všechny fronty a témata v základní nebo standardní SKU. Není k dispozici pro zasílání zpráv skladová položka Premium, ale žádné existující dělené entity na úrovni Premium bude fungovat podle očekávání.
 
Není možné změnit možnost vytváření oddílů v žádné stávající fronta nebo téma; možnost lze nastavit pouze při vytváření entity.

## <a name="how-it-works"></a>Jak to funguje

Každý oddílů fronta nebo téma se skládá z více fragmentů. Každý fragment je uložen v jiném úložišti zasílání zpráv a provádí jiná zpráva zprostředkovatele. Pro odeslání zprávy do oddílů fronta nebo téma sběrnice přiřadí zpráva jedním z fragmentů. Výběr provádí náhodně pomocí sběrnice nebo pomocí klíč oddílu, můžete zadat odesílatele.

Pokud klient požaduje pro příjem zprávy z fronty oddílů nebo z odběru do oddílů tématu Service Bus dotazy všechny fragmenty pro zprávy, pak vrátí první zprávu, která se získá z jakéhokoli z úložiště pro zasílání zpráv k příjemce. Service Bus mezipamětí dalších zpráv a vrátí je při přijetí další přijímat požadavky. Přijímající klient nemá informace o vytváření oddílů; chování klienta oddílů fronta nebo téma (například číst, dokončení, odložení, nedoručených zpráv, prefetching) je stejný jako u chování regulárních entity.

Není k dispozici bez dalších nákladů při odesílání zprávy, která se nebo přijímání zprávy z oddílů fronta nebo téma.

## <a name="enable-partitioning"></a>Povolit dělení

Pokud chcete používat oddílů front a témat s Azure Service Bus, použít sadu Azure SDK verze 2.2 nebo vyšší, nebo zadat `api-version=2013-10` nebo novější ve své žádosti HTTP.

### <a name="standard"></a>Standard

Na vrstvě Standard zasílání zpráv můžete vytvořit front Service Bus a témat v 1, 2, 3, 4 nebo velikostí 5 GB (výchozí hodnota je 1 GB). S oddíly povolená, vytvoří Service Bus 16 kopie (16 oddíly) entity pro každého GB je zadat. Jako takový, když vytvoříte frontu, který je 5 GB velikost, s 16 oddíly maximální velikost fronty stane (5 \* 16) = 80 GB. Zobrazí maximální velikost fronty oddílů nebo téma prohlížením jeho položku [portál Azure][Azure portal]v **přehled** okno této entity.

### <a name="premium"></a>Premium

V oboru názvů úrovně Premium můžete vytvořit front Service Bus a témat v 1, 2, 3, 4, 5, 10, 20, 40 nebo velikosti 80 GB (výchozí hodnota je 1 GB). Service Bus s oddíly, ve výchozím nastavení povolená, vytvoří dva oddíly na entity. Zobrazí maximální velikost fronty oddílů nebo téma prohlížením jeho položku [portál Azure][Azure portal]v **přehled** okno této entity.

Další informace o vytváření oddílů v zasílání zpráv úrovně Premium najdete v tématu [Service Bus Premium a Standard zasílání zpráv úrovně](service-bus-premium-messaging.md). 

### <a name="create-a-partitioned-entity"></a>Vytvoření oddílů entity

Existuje několik způsobů vytvoření oddílů fronta nebo téma. Když vytvoříte fronta nebo téma z vaší aplikace, můžete povolit vytváření oddílů pro fronta nebo téma v uvedeném pořadí nastavením [QueueDescription.EnablePartitioning] [ QueueDescription.EnablePartitioning] nebo [ TopicDescription.EnablePartitioning] [ TopicDescription.EnablePartitioning] vlastnost **true**. Tyto vlastnosti musí být nastavena v době fronta nebo téma není vytvořená. Jak jsme uvedli dříve, není možné měnit tyto vlastnosti existující fronta nebo téma. Příklad:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Alternativně můžete vytvořit oddílů fronta nebo téma v [portál Azure] [ Azure portal] nebo v sadě Visual Studio. Když vytvoříte na portálu, fronta nebo téma **povolit vytváření oddílů** možnost v fronta nebo téma **vytvořit** ve výchozím nastavení je zaškrtnuté políčko dialogové okno. Pouze můžete zakázat tuto možnost v entitě úrovně Standard; ve vrstvě | Premium dělení je vždy povolena. V sadě Visual Studio, klikněte **povolit vytváření oddílů** zaškrtnout políčko **novou frontu** nebo **nové téma** dialogové okno.

## <a name="use-of-partition-keys"></a>Použití klíče oddílů
Pokud zpráva zařazených do fronty do oddílů fronta nebo téma, Service Bus zkontroluje přítomnost klíč oddílu. V případě, že některou najde, vybere fragment na základě tohoto klíče. Pokud nenajde klíč oddílu, vybere fragment založený na interní algoritmu.

### <a name="using-a-partition-key"></a>Použitím klíče oddílu
Některé scénáře, jako je například relací nebo transakce, vyžadují zpráv, které mají být uloženy v konkrétních fragment. Tyto scénáře vyžadují použití klíč oddílu. Všechny zprávy, které používají stejný klíč oddílu jsou přiřazeny k stejného fragmentu. Pokud fragment je dočasně nedostupný, Service Bus vrátí chybu.

V závislosti na scénáři vlastnosti jiná zpráva slouží jako klíč oddílu:

**SessionId**: Pokud má zprávu [BrokeredMessage.SessionId] [ BrokeredMessage.SessionId] nastavte vlastnost a potom Service Bus používá tuto vlastnost jako klíč oddílu. Tímto způsobem, všechny zprávy, které patří do stejné relace jsou zpracovávány stejné zprostředkovatele zpráv. Relace povolit Service Bus zaručit zpráva řazení a také konzistence stavy relací.

**PartitionKey**: Pokud má zprávu [BrokeredMessage.PartitionKey] [ BrokeredMessage.PartitionKey] vlastnost ale ne [BrokeredMessage.SessionId] [ BrokeredMessage.SessionId] nastavte vlastnost a potom Service Bus používá [PartitionKey] [ PartitionKey] vlastnost jako klíč oddílu. Pokud zpráva nemá i [SessionId] [ SessionId] a [PartitionKey] [ PartitionKey] nastavení vlastnosti, obě vlastnosti musí být identické. Pokud [PartitionKey] [ PartitionKey] je nastavena na jinou hodnotu než [SessionId] [ SessionId] neplatný vrátí vlastnost, Service Bus operace došlo k výjimce. [PartitionKey] [ PartitionKey] vlastnost je užitečný v případě, že odesílatel odešle vědět transakční zprávy mimo relaci. Klíč oddílu zajistí, že všechny zprávy, které se odesílají v rámci transakce jsou zpracovávány stejné zasílání zpráv zprostředkovatele.

**MessageId**: Pokud má fronta nebo téma [QueueDescription.RequiresDuplicateDetection] [ QueueDescription.RequiresDuplicateDetection] vlastnost nastavena na hodnotu **true** a [ BrokeredMessage.SessionId] [ BrokeredMessage.SessionId] nebo [BrokeredMessage.PartitionKey] [ BrokeredMessage.PartitionKey] nejsou nastaveny vlastnosti, pak se [ BrokeredMessage.MessageId] [ BrokeredMessage.MessageId] vlastnost slouží jako klíč oddílu. (Knihovny rozhraní Microsoft .NET a AMQP automaticky přiřadit ID zprávy Pokud odesílající aplikací neexistuje.) V takovém případě jsou všechny kopie stejné zprávy zpracovávány stejné zprostředkovatele zpráv. Toto ID umožňuje Service Bus ke zjištění a odstranění duplicitních zpráv. Pokud [QueueDescription.RequiresDuplicateDetection] [ QueueDescription.RequiresDuplicateDetection] vlastnost není nastavena na **true**, Service Bus nebere v úvahu [MessageId] [ MessageId] vlastnost jako klíč oddílu.

### <a name="not-using-a-partition-key"></a>Není použitím klíče oddílu
Chybí klíč oddílu distribuuje sběrnice zpráv v kruhového dotazování na všechny fragmenty oddílů fronta nebo téma. Pokud vybraný fragment není k dispozici, Service Bus zprávu přiřadí různé fragment. Tímto způsobem operaci odeslání úspěšné i přes dočasné nedostupnosti úložišti pro přenos zpráv. Nebudou však dosáhnout zaručenou pořadí, které poskytuje klíč oddílu.

Podrobné informace o kompromis mezi dostupnost (žádný klíč oddílu) a konzistence (s použitím klíč oddílu), najdete v části [v tomto článku](../event-hubs/event-hubs-availability-and-consistency.md). Tyto informace platí stejně pro dělené entity služby Service Bus.

Umožnit Service Bus dost času zařadit do fronty zprávy do různých fragment, [MessagingFactorySettings.OperationTimeout] [ MessagingFactorySettings.OperationTimeout] hodnotu zadanou pomocí klienta, která odesílá zprávy musí být větší než 15 sekund. Doporučujeme, abyste nastavili [OperationTimeout] [ OperationTimeout] vlastnost, která má výchozí hodnotu 60 sekund.

Klíč oddílu "PIN kódy" zprávu s fragmentem konkrétní. Pokud není k dispozici úložišti pro přenos zpráv, který obsahuje tento fragment, Service Bus vrátí chybu. Chybí klíč oddílu Service Bus můžete vybrat jiný fragment a operace proběhla úspěšně. Proto se doporučuje, aby nezadáte klíč oddílu Pokud to není nutné.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Rozšířené témata: použití transakcí s dělené entity
Zprávy odeslané v rámci transakce musí určovat klíč oddílu. Klíč může být jedna z následujících vlastností: [BrokeredMessage.SessionId][BrokeredMessage.SessionId], [BrokeredMessage.PartitionKey][BrokeredMessage.PartitionKey], nebo [ BrokeredMessage.MessageId][BrokeredMessage.MessageId]. Všechny zprávy, které se odesílají v rámci stejné transakci musíte zadat stejný klíč oddílu. Pokud se pokusíte odeslat zprávu bez klíče oddílu v rámci transakce, Service Bus vrátí výjimku neplatná operace. Pokud se pokusíte odeslat více zpráv v rámci jedné transakce, které mají různé klíče oddílů, Service Bus vrátí výjimku neplatná operace. Příklad:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

Pokud nejsou nastavené žádné vlastnosti, které slouží jako klíč oddílu, PIN kódy Service Bus zprávu, která se konkrétní fragment. K tomuto chování dochází, zda se používá transakce. Doporučuje se, že nezadáte klíč oddílu Pokud není nutné.

## <a name="using-sessions-with-partitioned-entities"></a>Použití relací s dělené entity
Pokud chcete odeslat transakční zprávy do tématu deklaracemi relace nebo fronty, musí mít zprávy [BrokeredMessage.SessionId] [ BrokeredMessage.SessionId] sadu vlastností. Pokud [BrokeredMessage.PartitionKey] [ BrokeredMessage.PartitionKey] také zadána vlastnost, musí být stejný jako [SessionId] [ SessionId] vlastnost. Pokud se liší, vrátí se Service Bus výjimku neplatná operace.

Na rozdíl od front regulární (bez oddílů) nebo témata není možné použít jen jednu transakci odeslat více zpráv na jiné relace. Pokud pokus, vrátí se Service Bus výjimku neplatná operace. Příklad:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Předávání automatické zpráv s dělené entity
Service Bus podporuje automatické zpráva předávání od, k nebo mezi dělené entity. Pokud chcete povolit předávání automatické zpráv, nastavte [QueueDescription.ForwardTo] [ QueueDescription.ForwardTo] vlastnost na zdrojové fronty nebo předplatné. Pokud zpráva Určuje klíč oddílu ([SessionId][SessionId], [PartitionKey][PartitionKey], nebo [MessageId] [ MessageId]), tento klíč oddílu se používá pro Cílová entita.

## <a name="considerations-and-guidelines"></a>Důležité informace a pokyny
* **Funkce vysoké konzistence**: Pokud entity používá funkce, jako jsou relace, detekci duplikátů nebo explicitní kontrolu nad klíč rozdělení do oddílů, pak zasílání zpráv operace jsou vždy směrovány do konkrétní fragmenty. Pokud je některý z fragmentů intenzivní provoz nebo příslušné úložiště není v pořádku, tyto operace nezdaří a sníží dostupnosti. Obecně je stále mnohem vyšší než bez oddílů entity; konzistence pouze podmnožinu provozu dochází k problémům, a veškerý provoz. Další informace najdete v tématu to [diskuzi o dostupnosti a konzistence](../event-hubs/event-hubs-availability-and-consistency.md).
* **Správa**: operací, jako je vytvoření, aktualizace a odstranění je potřeba provést na všechny fragmenty entity. Pokud žádné fragment není v pořádku, to může způsobit selhání pro tyto operace. Pro operaci Get informace, jako je počty zprávy musí být agregován z všechny fragmenty. Pokud žádné fragment není v pořádku, stav dostupnosti entity se hlásí jako omezené.
* **Nízká svazku zpráva scénáře**: pro takové scénáře, zejména v případě, že pomocí protokolu HTTP, možná budete muset provést několika přijímat operace, aby bylo možné získat všechny zprávy. Pro přijetí žádosti front-endu provádí všechny fragmenty příjmu a ukládá do mezipaměti všechny přijaté odpovědi. Následné přijetí požadavku na stejné připojení by mít užitek z této ukládání do mezipaměti a přijímat bude nižší latenci. Ale pokud máte více připojení nebo prostřednictvím protokolu HTTP, který vytvoří nové připojení pro každý požadavek. Jako takový není zaručeno, že bude zobrazovat na stejném uzlu. Pokud jsou všechny existující zprávy uzamčen a uložené v mezipaměti v jiné front-endu, vrátí operace příjmu **null**. Zprávy nakonec vyprší a je můžete přijímat znovu. Doporučuje se udržování připojení HTTP.
* **Procházet zprávy funkce Náhled**: [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) vždy nevrací počet zpráv zadaný v [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) vlastnost. Existují dvě běžné důvody pro toto chování. Jedním z důvodů je, že agregovaná velikost kolekci zpráv překračuje maximální velikost 256 KB. Dalším důvodem je, že pokud má fronta nebo téma [enablepartitioning je vlastnost](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) nastavena na **true**, oddíl nemusí mít dostatek zprávy a pokuste se provést požadovaný počet zpráv. Obecně platí, pokud aplikace chce dostávat konkrétní počet zpráv, ho by měly volat [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) opakovaně, dokud získá počet zpráv, nebo nejsou žádné další zprávy k prohlížení. Další informace, včetně ukázky kódu, najdete v článku [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) nebo [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) dokumentaci k rozhraní API.

## <a name="latest-added-features"></a>Nejnovější přidané funkce
* Přidat nebo odebrat pravidlo se teď podporuje s dělené entity. Liší od bez oddílů entity, tyto operace nejsou podporovány v rámci transakce. 
* AMQP se teď podporuje pro odesílání a přijímání zpráv do a z oddílů entity.
* AMQP je nyní podporován pro následující operace: [odeslání dávky](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch), [přijímat Batch](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), [Receive podle pořadových čísel](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [prohlížet](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [ Obnovení zámku](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [naplánovat zpráva](/dotnet/api/microsoft.servicebus.messaging.queueclient.schedulemessageasync), [zrušit naplánované zpráva](/dotnet/api/microsoft.servicebus.messaging.queueclient.cancelscheduledmessageasync), [přidat pravidlo](/dotnet/api/microsoft.servicebus.messaging.ruledescription), [odebrat pravidlo](/dotnet/api/microsoft.servicebus.messaging.ruledescription), [Relace obnovení zámku](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), [stav relace sady](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), [stav relace Get](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate), a [výčet relací](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Dělené entity omezení
Aktuálně Service Bus sebou následující omezení na oddílů fronty a témata:

* Oddílů fronty a témata nepodporují odesílání zpráv, které patří do různých relacích v rámci jedné transakce.
* Service Bus aktuálně umožňuje v jednom oboru názvů až 100 dělených front nebo témat. Každý oddílů fronta nebo téma započítává kvótu 10 000 entit na obor názvů (nevztahuje na úrovni Premium).

## <a name="next-steps"></a>Další postup
Přečtěte si informace o klíčových konceptech AMQP 1.0, specifikace v zasílání zpráv [Průvodce protokolu AMQP 1.0](service-bus-amqp-protocol-guide.md).

[Service Bus architecture]: service-bus-architecture.md
[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[BrokeredMessage.SessionId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid
[BrokeredMessage.PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey
[SessionId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.sessionid
[PartitionKey]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.partitionkey
[QueueDescription.RequiresDuplicateDetection]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection
[BrokeredMessage.MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid
[MessageId]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.messageid
[MessagingFactorySettings.OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout
[OperationTimeout]: /dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md
