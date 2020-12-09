---
title: Vytvořit dělené Azure Service Bus fronty a témata | Microsoft Docs
description: Popisuje, jak rozdělit Service Bus fronty a témata pomocí více zprostředkovatelů zpráv.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 8fd845ba24fd96ad6de566a7f55b25bd7129074d
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930428"
---
# <a name="partitioned-queues-and-topics"></a>Dělené fronty a témata

Azure Service Bus využívá více zprostředkovatelů zpráv ke zpracování zpráv a více úložišť pro zasílání zpráv pro ukládání zpráv. Konvenční frontu nebo téma zpracovává jeden zprostředkovatel zpráv a ukládá se do jednoho úložiště pro zasílání zpráv. Service Bus *oddíly* umožňují rozdělit do oddílů fronty a témata nebo *entity zasílání zpráv* v rámci více zprostředkovatelů zpráv a úložišť pro zasílání zpráv. Dělení znamená, že celková propustnost entit rozdělená na oddíly již není omezena výkonem jediného zprostředkovatele zpráv nebo úložiště pro zasílání zpráv. Kromě toho dočasný výpadek úložiště pro zasílání zpráv nezobrazuje dělenou frontu nebo téma není k dispozici. Dělené fronty a témata můžou obsahovat všechny pokročilé funkce Service Bus, jako je podpora transakcí a relací.

> [!NOTE]
> Dělení je k dispozici při vytváření entit pro všechny fronty a témata v jednotkách Basic a Standard. Pro SKU zasílání zpráv na úrovni Premium není k dispozici, ale všechny dříve existující rozdělené entity v oborech názvů Premium budou fungovat podle očekávání.
 
Možnost dělení v žádné existující frontě nebo tématu není možné změnit. možnost lze nastavit pouze při vytváření entity.

## <a name="how-it-works"></a>Jak to funguje

Každá dělená fronta nebo téma se skládá z několika oddílů. Každý oddíl je uložený v jiném úložišti pro zasílání zpráv a zpracovávaný jiným zprostředkovatelem zpráv. Po odeslání zprávy do dělené fronty nebo tématu Service Bus přiřadí zprávu k jednomu z oddílů. Výběr se provádí náhodně Service Bus nebo pomocí klíče oddílu, který může odesílatel zadat.

Když chce klient přijmout zprávu z dělené fronty nebo z předplatného oddílu, Service Bus zadá dotaz na všechny oddíly pro zprávy a vrátí první zprávu získanou z úložišť zpráv do přijímače. Service Bus ukládá do mezipaměti ostatní zprávy a vrátí je, když obdrží další žádosti o přijetí. Přijímající klient neví o dělení; chování v dělené frontě nebo tématu s klientským přístupem (například čtení, dokončování, odložení, nedoručené zprávy, předběžné načítání) je stejné jako chování běžné entity.

Operace prohlížet v nerozdělené entitě vždycky vrátí nejstarší zprávu, ale ne na dělenou entitu. Místo toho vrátí nejstarší zprávu v jednom z oddílů, u kterých zprostředkovatel zpráv odpověděl jako první. V rámci všech oddílů není nijak zaručeno, že vrácená zpráva je nejstarší. 

Při odesílání zprávy do dělené fronty nebo tématu se neúčtují žádné další náklady.
>[!NOTE]
> Náhled operace vrátí nejstarší zprávu z části na základě jejího SequenceNumber. Pro entity partioned je číslo sekvence vystaveno relativně k oddílu. Další informace najdete v tématu [sekvence zpráv a časová razítka](../service-bus-messaging/message-sequencing.md).

## <a name="enable-partitioning"></a>Povolit dělení

Pokud chcete použít dělené fronty a témata s Azure Service Bus, použijte Azure SDK verze 2,2 nebo novější, nebo zadejte `api-version=2013-10` nebo novější v požadavcích http.

### <a name="standard"></a>Standard

Na úrovni Standard pro zasílání zpráv můžete vytvořit Service Bus fronty a témata ve velikosti 1, 2, 3, 4 nebo 5 GB (výchozí hodnota je 1 GB). Když je zapnuté dělení, Service Bus vytvoří 16 kopií (16 oddílů) entity, každé zadané velikosti. Pokud tedy vytvoříte frontu o velikosti 5 GB s 16 oddíly, maximální velikost fronty bude (5 \* 16) = 80 GB. Maximální velikost vaší dělené fronty nebo tématu můžete zobrazit tak, že v okně **Přehled** pro danou entitu prohlížíte její položku v [Azure Portal][Azure portal].

### <a name="premium"></a>Premium

V oboru názvů úrovně Premium se nepodporují dělení entit. Stále ale můžete vytvářet Service Bus fronty a témata v 1, 2, 3, 4, 5, 10, 20, 40 nebo 80-GB (výchozí hodnota je 1 GB). Velikost fronty nebo tématu můžete zobrazit tak, že v okně **Přehled** pro danou entitu prohlížíte její položku v [Azure Portal][Azure portal].

### <a name="create-a-partitioned-entity"></a>Vytvoření rozdělené entity

Existuje několik způsobů, jak vytvořit dělenou frontu nebo téma. Když vytvoříte frontu nebo téma z aplikace, můžete povolit dělení na oddíly pro frontu nebo téma nastavením vlastnosti [QueueDescription. EnablePartitioning][QueueDescription.EnablePartitioning] nebo [TopicDescription. EnablePartitioning][TopicDescription.EnablePartitioning] na **hodnotu true**. Tyto vlastnosti musí být nastaveny v okamžiku vytvoření fronty nebo tématu a jsou k dispozici pouze ve starší knihovně [windowsazure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) . Jak bylo uvedeno dříve, není možné tyto vlastnosti změnit ve stávající frontě nebo tématu. Příklad:

```csharp
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Případně můžete vytvořit dělenou frontu nebo téma v [Azure Portal][Azure portal]. Když vytvoříte frontu nebo téma na portálu, ve výchozím nastavení je zaškrtnuta možnost **Povolit dělení** v dialogovém okně **vytvořit nebo vytvořit** téma. Tuto možnost můžete zakázat jenom v entitě úrovně Standard; v dělení úrovně Premium se nepodporuje a zaškrtávací políčko nemá žádný vliv. 

## <a name="use-of-partition-keys"></a>Použití klíčů oddílu

Když se do dělené fronty nebo tématu zařadí zpráva do fronty, Service Bus zkontroluje přítomnost klíče oddílu. Pokud najde jednu, vybere oddíl na základě tohoto klíče. Pokud klíč oddílu nenajde, vybere oddíl na základě interního algoritmu.

### <a name="using-a-partition-key"></a>Použití klíče oddílu

Některé scénáře, například relace nebo transakce, vyžadují, aby se zprávy uložily do konkrétního oddílu. Všechny tyto scénáře vyžadují použití klíče oddílu. Všechny zprávy, které používají stejný klíč oddílu, jsou přiřazeny ke stejnému oddílu. Pokud je oddíl dočasně nedostupný, Service Bus vrátí chybu.

V závislosti na scénáři se jako klíč oddílu používají různé vlastnosti zpráv:

**SessionID**: Pokud má zpráva nastavenou vlastnost [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) , Service Bus jako klíč oddílu použije **SessionID** . Tímto způsobem budou všechny zprávy patřící do stejné relace zpracovávány stejným zprostředkovatelem zpráv. Relace umožňují Service Bus zaručit řazení zpráv a také konzistenci stavů relace.

**PartitionKey**: Pokud má zpráva vlastnost [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) , ale ne nastavenou vlastnost [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) , pak Service Bus jako klíč oddílu použije hodnotu vlastnosti [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) . Pokud má zpráva současně nastavené vlastnosti [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) i [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) , musí být obě vlastnosti identické. Pokud je vlastnost [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) nastavena na jinou hodnotu než vlastnost [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) , Service Bus vrátí neplatnou výjimku operace. Vlastnost [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) by měla být použita, pokud odesilatel odesílá transakční zprávy, které nepodporují relaci. Klíč oddílu zajišťuje, že všechny zprávy, které jsou odeslány v rámci transakce, jsou zpracovávány stejným zprostředkovatelem přenosu zpráv.

**MessageID**: Pokud má fronta nebo téma vlastnost [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) nastavenou na **hodnotu true** a vlastnosti [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) nebo [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) nejsou nastaveny, pak hodnota vlastnosti [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid) slouží jako klíč oddílu. (Knihovny Microsoft .NET a AMQP automaticky přiřazují ID zprávy, pokud odesílající aplikace ne.) V tomto případě jsou všechny kopie stejné zprávy zpracovávány stejným zprostředkovatelem zpráv. Toto ID umožňuje Service Bus zjišťování a odstraňování duplicitních zpráv. Pokud vlastnost [RequiresDuplicateDetection](/dotnet/api/microsoft.azure.management.servicebus.models.sbqueue.requiresduplicatedetection) není nastavena na **hodnotu true**, Service Bus nepovažuje vlastnost [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid) za klíč oddílu.

### <a name="not-using-a-partition-key"></a>Nepoužívá se klíč oddílu.

V případě absence klíče oddílu Service Bus distribuuje zprávy v kruhovém dotazování do všech oddílů rozdělené do dělené fronty nebo tématu. Pokud vybraný oddíl není k dispozici, Service Bus přiřadí zprávu na jiný oddíl. Tímto způsobem bude operace odeslání úspěšná bez ohledu na dočasnou nedostupnost úložiště pro zasílání zpráv. Nebudete ale mít zaručené řazení, které poskytuje klíč oddílu.

Podrobnější diskuzi o kompromisech mezi dostupností (bez klíče oddílu) a konzistencí (pomocí klíče oddílu) najdete v [tomto článku](../event-hubs/event-hubs-availability-and-consistency.md). Tyto informace se vztahují rovnoměrně na dělené Service Bus entity.

Aby bylo Service Bus dostatek času na zařazování zprávy do jiného oddílu, hodnota [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) zadaná klientem, který odesílá zprávu, musí být delší než 15 sekund. Doporučuje se nastavit vlastnost [OperationTimeout](/dotnet/api/microsoft.azure.servicebus.queueclient.operationtimeout) na výchozí hodnotu 60 sekund.

Klíč oddílu "PIN" zprávy do konkrétního oddílu. Pokud úložiště pro zasílání zpráv, které obsahuje tento oddíl, není k dispozici, Service Bus vrátí chybu. V případě absence klíče oddílu může Service Bus vybrat jiný oddíl a operace bude úspěšná. Proto se doporučuje, abyste klíč oddílu nezadali, pokud ho nepotřebujete.

## <a name="advanced-topics-use-transactions-with-partitioned-entities"></a>Pokročilá témata: použití transakcí s rozdělenými entitami

Zprávy odeslané v rámci transakce musí určovat klíč oddílu. Klíč může být jedna z následujících vlastností: [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)nebo [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid). Všechny zprávy, které jsou odeslány jako součást stejné transakce, musí určovat stejný klíč oddílu. Pokud se pokusíte odeslat zprávu bez klíče oddílu v rámci transakce, Service Bus vrátí neplatnou výjimku operace. Pokud se pokusíte odeslat více zpráv v rámci stejné transakce, které mají různé klíče oddílu, Service Bus vrátí výjimku neplatné operace. Příklad:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.PartitionKey = "myPartitionKey";
    await messageSender.SendAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

Pokud je nastavená kterákoli z vlastností, která slouží jako klíč oddílu, Service Bus zprávu zapnete do konkrétního oddílu. K tomuto chování dochází bez ohledu na to, zda je použita transakce. Doporučuje se, abyste klíč oddílu neurčili, pokud není potřeba.

## <a name="using-sessions-with-partitioned-entities"></a>Použití relací s rozdělenými entitami

Chcete-li odeslat transakční zprávu do tématu nebo fronty využívající relaci, musí mít zpráva nastavenu vlastnost [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) . Pokud je zadána vlastnost [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey) , musí být stejná jako vlastnost [SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid) . Pokud se liší, Service Bus vrátí výjimku neplatné operace.

Na rozdíl od běžných (nerozdělených) front nebo témat není možné použít jedinou transakci k posílání více zpráv do různých relací. Při pokusu Service Bus vrací výjimku neplatné operace. Příklad:

```csharp
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    Message msg = new Message("This is a message");
    msg.SessionId = "mySession";
    await messageSender.SendAsync(msg); 
    await ts.CompleteAsync();
}
committableTransaction.Commit();
```

## <a name="automatic-message-forwarding-with-partitioned-entities"></a>Automatické předávání zpráv pomocí dělených entit

Service Bus podporuje automatické předávání zpráv mezi rozdělenými entitami nebo mezi nimi. Chcete-li povolit automatické předávání zpráv, nastavte vlastnost [QueueDescription. ForwardTo][QueueDescription.ForwardTo] ve zdrojové frontě nebo předplatném. Pokud zpráva Určuje klíč oddílu ([SessionID](/dotnet/api/microsoft.azure.servicebus.message.sessionid), [PartitionKey](/dotnet/api/microsoft.azure.servicebus.message.partitionkey)nebo [MessageID](/dotnet/api/microsoft.azure.servicebus.message.messageid)), použije se tento klíč oddílu pro cílovou entitu.

## <a name="considerations-and-guidelines"></a>Doporučení a pokyny
* **Funkce s vysokou konzistencí**: Pokud entita používá funkce, jako jsou například relace, zjišťování duplicit nebo explicitní řízení klíče dělení, operace zasílání zpráv se vždycky směrují do konkrétního oddílu. Pokud některý z oddílů pracuje s vysokým provozem nebo je úložiště v pořádku, dojde k selhání a dostupnosti těchto operací. Konzistence je stále mnohem vyšší než nedělené entity; jenom v podmnožině provozu dochází k problémům, a to na rozdíl od všech přenosů. Další informace najdete v této [diskuzi o dostupnosti a konzistenci](../event-hubs/event-hubs-availability-and-consistency.md).
* **Správa**: operace, jako je vytváření, aktualizace a odstraňování, se musí provádět na všech oddílech entity. Pokud některý oddíl není v pořádku, může pro tyto operace dojít k selhání. V případě operace Get musí být informace, jako jsou počty zpráv, agregované ze všech oddílů. Pokud některý oddíl není v pořádku, je stav dostupnosti entity hlášeno jako omezené.
* **Scénáře nedostatku zpráv o svazcích**: u takových scénářů, zejména při použití protokolu HTTP, může být nutné provést více operací Receive, aby bylo možné získat všechny zprávy. U žádostí o přijetí se u front-endu provede příjem všech oddílů a uloží do mezipaměti všechny přijaté odpovědi. Další žádost o přijetí na stejné připojení by měla těžit z tohoto ukládání do mezipaměti a latence příjmu budou nižší. Pokud máte ale více připojení nebo používáte protokol HTTP, vytvoří se pro každý požadavek nové připojení. V takovém případě není nijak zaručeno, že by se na stejný uzel nakládal. Pokud jsou všechny existující zprávy uzamčeny a uloženy v mezipaměti v jiném front-end, operace Receive vrátí **hodnotu null**. Zprávy nakonec vyprší a můžete je opět přijmout. Doporučuje se Keep-Alive protokolu HTTP. Při vytváření oddílů ve scénářích s nízkým objemem se můžou operace příjmu trvat déle, než se čekalo. Proto doporučujeme, abyste v těchto scénářích nepoužívali dělení. Pokud chcete zlepšit výkon, odstraňte všechny existující dělené entity a znovu je vytvořte s neaktivovaným rozdělením.
* **Procházet a prohlížet zprávy**: k dispozici pouze ve starší knihovně [windowsazure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) . [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) vždy nevrátí počet zpráv zadaný ve vlastnosti [MessageCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.messagecount) . Existují dva běžné důvody pro toto chování. Jedním z důvodů je, že agregovaná velikost kolekce zpráv překračuje maximální velikost 256 KB. Dalším důvodem je, že pokud má fronta nebo téma [vlastnost EnablePartitioning](/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning) nastavenou na **hodnotu true**, oddíl nemusí mít dostatek zpráv k dokončení požadovaného počtu zpráv. Obecně platí, že pokud aplikace chce přijmout určitý počet zpráv, měla by volat [PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) opakovaně, dokud nezíská tento počet zpráv, nebo neexistují žádné další zprávy, které by bylo možné prohlížet. Další informace, včetně ukázek kódu, naleznete v dokumentaci k rozhraní API [QueueClient. PeekBatch](/dotnet/api/microsoft.servicebus.messaging.queueclient.peekbatch) nebo [SubscriptionClient. PeekBatch](/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.peekbatch) .

## <a name="latest-added-features"></a>Nejnovější přidané funkce

* Pro rozdělené entity se teď podporuje přidat nebo odebrat pravidlo. Liší se od nerozdělených entit, tyto operace nejsou v rámci transakcí podporovány. 
* AMQP se teď podporuje pro posílání a přijímání zpráv do dělené entity a z ní.
* AMQP se teď podporuje pro následující operace: [dávkové odeslání](/dotnet/api/microsoft.servicebus.messaging.queueclient.sendbatch), [získání dávky](/dotnet/api/microsoft.servicebus.messaging.queueclient.receivebatch), [příjem podle pořadových čísel](/dotnet/api/microsoft.servicebus.messaging.queueclient.receive), [Náhled](/dotnet/api/microsoft.servicebus.messaging.queueclient.peek), [obnovení zámku](/dotnet/api/microsoft.servicebus.messaging.queueclient.renewmessagelock), [naplánovat zprávu](/dotnet/api/microsoft.azure.servicebus.queueclient.schedulemessageasync), [zrušit naplánovanou zprávu](/dotnet/api/microsoft.azure.servicebus.queueclient.cancelscheduledmessageasync), [Přidat pravidlo](/dotnet/api/microsoft.azure.servicebus.ruledescription), [odebrat pravidlo](/dotnet/api/microsoft.azure.servicebus.ruledescription), [Zámek obnovení relace](/dotnet/api/microsoft.servicebus.messaging.messagesession.renewlock), [nastavit stav relace](/dotnet/api/microsoft.servicebus.messaging.messagesession.setstate), [získat stav](/dotnet/api/microsoft.servicebus.messaging.messagesession.getstate)relace a [vytvořit výčet relací](/dotnet/api/microsoft.servicebus.messaging.queueclient.getmessagesessions).

## <a name="partitioned-entities-limitations"></a>Omezení entit s oddíly

V současné době Service Bus ukládá na dělené fronty a témata následující omezení:

* Dělené fronty a témata se v úrovni zasílání zpráv úrovně Premium nepodporují. Relace jsou podporovány ve vrstvě Premier pomocí identifikátoru SessionId. 
* Dělené fronty a témata nepodporují posílání zpráv, které patří do různých relací v rámci jedné transakce.
* Service Bus aktuálně umožňuje v jednom oboru názvů až 100 dělených front nebo témat. Každá dělená fronta nebo téma se počítá s kvótou 10 000 entit na obor názvů (nevztahuje se na úroveň Premium).

## <a name="next-steps"></a>Další kroky

Přečtěte si o základních konceptech specifikace zasílání zpráv AMQP 1,0 v [příručce k protokolu AMQP 1,0](service-bus-amqp-protocol-guide.md).

[Azure portal]: https://portal.azure.com
[QueueDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablepartitioning
[QueueDescription.ForwardTo]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.forwardto
[AMQP 1.0 support for Service Bus partitioned queues and topics]: ./service-bus-amqp-protocol-guide.md
