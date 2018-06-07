---
title: Vytvoření oddílů fronty Azure Service Bus a témat | Microsoft Docs
description: Popisuje, jak při vytváření oddílů témata a fronty Service Bus pomocí více zprostředkovatelé zprávy.
services: service-bus-messaging
author: sethmanheim
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 06/06/2018
ms.author: sethm
ms.openlocfilehash: f9fb5f53496ea3f98a9d3341e77db283a3e3b570
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34824366"
---
# <a name="partitioned-queues-and-topics"></a>Dělené fronty a témata

Azure Service Bus používá více zpráv zprostředkovatelé ke zpracování zpráv a více úložiště pro zasílání zpráv pro ukládání zpráv. Konvenční fronta nebo téma zpracování zprostředkovatelem jedné zprávy a uloženy v úložišti jeden zasílání zpráv. Service Bus *oddíly* povolit front a témat, nebo *entity pro zasílání zpráv*, k rozdělení na oddíly na více zpráv zprostředkovatelé a úložiště pro zasílání zpráv. Vytváření oddílů znamená, že celkovou propustnost dělené entity je již omezena výkon zprostředkovatele jedné zprávy nebo úložišti pro přenos zpráv. Kromě toho dočasnému výpadku zasílání zpráv úložiště nevykresluje oddílů fronta nebo téma není k dispozici. Oddílů fronty a témata může obsahovat všechny pokročilé funkce služby Service Bus, například podporu transakce a relací.

> [!NOTE]
> Vytváření oddílů je k dispozici při vytváření entity pro všechny fronty a témata v základní nebo standardní SKU. Není k dispozici pro zasílání zpráv skladová položka Premium, ale žádné dřívější dělené entity v oborech názvů Premium i nadále fungovat podle očekávání.
 
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

V oboru názvů úrovně Premium vytváření oddílů entity není podporována. Však stále vytvořením front Service Bus a témat na 1, 2, 3, 4, 5, 10, 20, 40 nebo velikosti 80 GB (výchozí hodnota je 1 GB). Zobrazí velikost fronta nebo téma prohlížením jeho položku [portál Azure][Azure portal]v **přehled** okno této entity.

### <a name="create-a-partitioned-entity"></a>Vytvoření oddílů entity

Existuje několik způsobů vytvoření oddílů fronta nebo téma. Když vytvoříte fronta nebo téma z vaší aplikace, můžete povolit vytváření oddílů pro fronta nebo téma v uvedeném pořadí nastavením [QueueDescription.EnablePartitioning] [ QueueDescription.EnablePartitioning] nebo [ TopicDescription.EnablePartitioning] [ TopicDescription.EnablePartitioning] vlastnost **true**. Tyto vlastnosti musí být nastavena v době vytvoření fronta nebo téma a jsou k dispozici pouze v starší [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) knihovny. Jak jsme uvedli dříve, není možné měnit tyto vlastnosti existující fronta nebo téma. Příklad:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Alternativně můžete vytvořit oddílů fronta nebo téma v [portál Azure][Azure portal]. Když vytvoříte na portálu, fronta nebo téma **povolit vytváření oddílů** možnost v fronta nebo téma **vytvořit** ve výchozím nastavení je zaškrtnuté políčko dialogové okno. Pouze můžete zakázat tuto možnost v entitě úrovně Standard; ve vrstvě | Premium není podporováno vytváření oddílů a políčka nemá žádný vliv. 

## <a name="use-of-partition-keys"></a>Použití klíče oddílů

Pokud zpráva zařazených do fronty do oddílů fronta nebo téma, Service Bus zkontroluje přítomnost klíč oddílu. V případě, že některou najde, vybere fragment na základě tohoto klíče. Pokud nenajde klíč oddílu, vybere fragment založený na interní algoritmu.

### <a name="using-a-partition-key"></a>Použitím klíče oddílu

Některé scénáře, jako je například relací nebo transakce, vyžadují zpráv, které mají být uloženy v konkrétních fragment. Tyto scénáře vyžadují použití klíč oddílu. Všechny zprávy, které používají stejný klíč oddílu jsou přiřazeny k stejného fragmentu. Pokud fragment je dočasně nedostupný, Service Bus vrátí chybu.

V závislosti na scénáři vlastnosti jiná zpráva slouží jako klíč oddílu:

**SessionId**: Pokud má zprávu [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) nastavte vlastnost a potom Service Bus používá **SessionID** jako klíč oddílu. Tímto způsobem, všechny zprávy, které patří do stejné relace jsou zpracovávány stejné zprostředkovatele zpráv. Relace povolit Service Bus zaručit zpráva řazení a také konzistence stavy relací.

**PartitionKey**: Pokud má zprávu [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) vlastnost ale ne [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) nastavte vlastnost a potom Service Bus používá [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) vlastnost hodnota jako klíč oddílu. Pokud zpráva nemá i [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) a [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) nastavení vlastnosti, obě vlastnosti musí být identické. Pokud [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) je nastavena na jinou hodnotu než [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) vlastnost, Service Bus vrací výjimku neplatná operace. [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) vlastnost je užitečný v případě, že odesílatel odešle vědět transakční zprávy mimo relaci. Klíč oddílu zajistí, že všechny zprávy, které se odesílají v rámci transakce jsou zpracovávány stejné zasílání zpráv zprostředkovatele.

**MessageId**: Pokud má fronta nebo téma [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) vlastnost nastavena na hodnotu **true** a [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) nebo [ PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) nejsou nastaveny vlastnosti, pak se [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) hodnotu vlastnosti slouží jako klíč oddílu. (Knihovny rozhraní Microsoft .NET a AMQP automaticky přiřadit ID zprávy Pokud odesílající aplikací neexistuje.) V takovém případě jsou všechny kopie stejné zprávy zpracovávány stejné zprostředkovatele zpráv. Toto ID umožňuje Service Bus ke zjištění a odstranění duplicitních zpráv. Pokud [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) vlastnost není nastavena na **true**, Service Bus nebere v úvahu [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid) vlastnost jako klíč oddílu.

### <a name="not-using-a-partition-key"></a>Není použitím klíče oddílu

Chybí klíč oddílu distribuuje sběrnice zpráv v kruhového dotazování na všechny fragmenty oddílů fronta nebo téma. Pokud vybraný fragment není k dispozici, Service Bus zprávu přiřadí různé fragment. Tímto způsobem operaci odeslání úspěšné i přes dočasné nedostupnosti úložišti pro přenos zpráv. Nebudou však dosáhnout zaručenou pořadí, které poskytuje klíč oddílu.

Podrobné informace o kompromis mezi dostupnost (žádný klíč oddílu) a konzistence (s použitím klíč oddílu), najdete v části [v tomto článku](../event-hubs/event-hubs-availability-and-consistency.md). Tyto informace platí stejně pro dělené entity služby Service Bus.

Umožnit Service Bus dost času zařadit do fronty zprávy do různých fragment, [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) hodnotu zadanou pomocí klienta, která odesílá zprávy musí být větší než 15 sekund. Doporučujeme, abyste nastavili [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) vlastnost, která má výchozí hodnotu 60 sekund.

Klíč oddílu "PIN kódy" zprávu s fragmentem konkrétní. Pokud není k dispozici úložišti pro přenos zpráv, který obsahuje tento fragment, Service Bus vrátí chybu. Chybí klíč oddílu Service Bus můžete vybrat jiný fragment a operace proběhla úspěšně. Proto se doporučuje, aby nezadáte klíč oddílu Pokud to není nutné.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Rozšířené témata: použití transakcí s dělené entity

Zprávy odeslané v rámci transakce musí určovat klíč oddílu. Klíč může být jedna z následujících vlastností: [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey), nebo [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid). Všechny zprávy, které se odesílají v rámci stejné transakci musíte zadat stejný klíč oddílu. Pokud se pokusíte odeslat zprávu bez klíče oddílu v rámci transakce, Service Bus vrátí výjimku neplatná operace. Pokud se pokusíte odeslat více zpráv v rámci jedné transakce, které mají různé klíče oddílů, Service Bus vrátí výjimku neplatná operace. Příklad:

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

Pokud nejsou nastavené žádné vlastnosti, které slouží jako klíč oddílu, PIN kódy Service Bus zprávu, která se konkrétní fragment. K tomuto chování dochází, zda se používá transakce. Doporučuje se, že nezadáte klíč oddílu Pokud není nutné.

## <a name="using-sessions-with-partitioned-entities"></a>Použití relací s dělené entity

Pokud chcete odeslat transakční zprávy do tématu deklaracemi relace nebo fronty, musí mít zprávy [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) sadu vlastností. Pokud [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) také zadána vlastnost, musí být stejný jako [SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid) vlastnost. Pokud se liší, vrátí se Service Bus výjimku neplatná operace.

Na rozdíl od front regulární (bez oddílů) nebo témata není možné použít jen jednu transakci odeslat více zpráv na jiné relace. Pokud pokus, vrátí se Service Bus výjimku neplatná operace. Příklad:

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

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Předávání automatické zpráv s dělené entity

Service Bus podporuje automatické zpráva předávání od, k nebo mezi dělené entity. Pokud chcete povolit předávání automatické zpráv, nastavte [QueueDescription.ForwardTo] [ QueueDescription.ForwardTo] vlastnost na zdrojové fronty nebo předplatné. Pokud zpráva Určuje klíč oddílu ([SessionId](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey), nebo [MessageId](/dotnet/api/microsoft.azure.servicebus.message.messageid)), tento klíč oddílu se používá pro Cílová entita.

## <a name="considerations-and-guidelines"></a>Důležité informace a pokyny
* **Funkce vysoké konzistence**: Pokud entity používá funkce, jako jsou relace, detekci duplikátů nebo explicitní kontrolu nad klíč rozdělení do oddílů, pak zasílání zpráv operace jsou vždy směrovány do konkrétní fragmenty. Pokud je některý z fragmentů intenzivní provoz nebo příslušné úložiště není v pořádku, tyto operace nezdaří a sníží dostupnosti. Obecně je stále mnohem vyšší než bez oddílů entity; konzistence pouze podmnožinu provozu dochází k problémům, a veškerý provoz. Další informace najdete v tématu to [diskuzi o dostupnosti a konzistence](../event-hubs/event-hubs-availability-and-consistency.md).
* **Správa**: operací, jako je vytvoření, aktualizace a odstranění je potřeba provést na všechny fragmenty entity. Pokud žádné fragment není v pořádku, to může způsobit selhání pro tyto operace. Pro operaci Get informace, jako je počty zprávy musí být agregován z všechny fragmenty. Pokud žádné fragment není v pořádku, stav dostupnosti entity se hlásí jako omezené.
* **Nízká svazku zpráva scénáře**: pro takové scénáře, zejména v případě, že pomocí protokolu HTTP, možná budete muset provést několika přijímat operace, aby bylo možné získat všechny zprávy. Pro přijetí žádosti front-endu provádí všechny fragmenty příjmu a ukládá do mezipaměti všechny přijaté odpovědi. Následné přijetí požadavku na stejné připojení by mít užitek z této ukládání do mezipaměti a přijímat bude nižší latenci. Ale pokud máte více připojení nebo prostřednictvím protokolu HTTP, který vytvoří nové připojení pro každý požadavek. Jako takový není zaručeno, že bude zobrazovat na stejném uzlu. Pokud jsou všechny existující zprávy uzamčen a uložené v mezipaměti v jiné front-endu, vrátí operace příjmu **null**. Zprávy nakonec vyprší a je můžete přijímat znovu. Doporučuje se udržování připojení HTTP.
* **Procházet zprávy funkce Náhled**: k dispozici pouze v starší [WindowsAzure.ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) knihovny. [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) vždy nevrací počet zpráv zadaný v [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) vlastnost. Existují dvě běžné důvody pro toto chování. Jedním z důvodů je, že agregovaná velikost kolekci zpráv překračuje maximální velikost 256 KB. Dalším důvodem je, že pokud má fronta nebo téma [enablepartitioning je vlastnost](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) nastavena na **true**, oddíl nemusí mít dostatek zprávy a pokuste se provést požadovaný počet zpráv. Obecně platí, pokud aplikace chce dostávat konkrétní počet zpráv, ho by měly volat [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) opakovaně, dokud získá počet zpráv, nebo nejsou žádné další zprávy k prohlížení. Další informace, včetně ukázky kódu, najdete v článku [QueueClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) nebo [SubscriptionClient.PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) dokumentaci k rozhraní API.

## <a name="latest-added-features"></a>Nejnovější přidané funkce

* Přidat nebo odebrat pravidlo se teď podporuje s dělené entity. Liší od bez oddílů entity, tyto operace nejsou podporovány v rámci transakce. 
* AMQP se teď podporuje pro odesílání a přijímání zpráv do a z oddílů entity.
* AMQP je nyní podporován pro následující operace: [odeslání dávky](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch), [přijímat Batch](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), [Receive podle pořadových čísel](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [prohlížet](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [ Obnovení zámku](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [naplánovat zpráva](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync), [zrušit naplánované zpráva](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync), [přidat pravidlo](/dotnet/api/microsoft.azure.servicebus.ruledescription), [odebrat pravidlo](/dotnet/api/microsoft.azure.servicebus.ruledescription), [Relace obnovení zámku](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), [stav relace sady](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), [stav relace Get](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate), a [výčet relací](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Dělené entity omezení

Aktuálně Service Bus sebou následující omezení na oddílů fronty a témata:

* Oddílů front a témat nejsou podporovány v zasílání zpráv úrovně Premium.
* Oddílů fronty a témata nepodporují odesílání zpráv, které patří do různých relacích v rámci jedné transakce.
* Service Bus aktuálně umožňuje v jednom oboru názvů až 100 dělených front nebo témat. Každý oddílů fronta nebo téma započítává kvótu 10 000 entit na obor názvů (nevztahuje na úrovni Premium).

## <a name="next-steps"></a>Další postup

Přečtěte si informace o klíčových konceptech AMQP 1.0, specifikace v zasílání zpráv [Průvodce protokolu AMQP 1.0](service-bus-amqp-protocol-guide.md).

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: service-bus-partitioned-queues-and-topics-amqp-overview.md
