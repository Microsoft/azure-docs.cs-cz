---
title: Osvědčené postupy pro zlepšení výkonu pomocí Azure Service Bus | Microsoft Docs
description: Popisuje, jak použít Service Bus k optimalizaci výkonu při výměně zprostředkovaných zpráv.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 09/14/2018
ms.author: aschhab
ms.openlocfilehash: 3d2d26e8cb8a3b1ee7720424aea701ca063ecc9f
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2019
ms.locfileid: "72596462"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Osvědčené postupy pro zlepšení výkonu pomocí Service Bus zasílání zpráv

Tento článek popisuje, jak použít Azure Service Bus k optimalizaci výkonu při výměně zprostředkovaných zpráv. První část tohoto článku popisuje různé mechanismy, které vám pomůžou zvýšit výkon. Druhá část poskytuje pokyny pro použití Service Bus způsobem, který může nabízet nejlepší výkon v daném scénáři.

V rámci tohoto článku pojem "klient" odkazuje na libovolnou entitu, která přistupuje k Service Bus. Klient může převzít roli odesilatele nebo příjemce. Termín "odesilatel" se používá pro Service Bus frontu nebo klienta tématu, který odesílá zprávy do fronty Service Bus nebo z předplatného tématu. Pojem "příjemce" odkazuje na klienta Service Bus front nebo odběrů, který přijímá zprávy z fronty nebo předplatného Service Bus.

Tyto části představují několik konceptů, které Service Bus používá ke zvýšení výkonu.

## <a name="protocols"></a>Protokoly

Service Bus umožňuje klientům odesílat a přijímat zprávy prostřednictvím jednoho ze tří protokolů:

1. Rozšířený protokol řízení front zpráv (AMQP) (AMQP)
2. Protokol SBMP (Service Bus Messaging Protocol)
3. HTTP

AMQP a SBMP jsou efektivnější, protože udržují připojení Service Bus, dokud objekt pro vytváření zpráv existuje. Implementuje také dávkování a předběžné načítání. Pokud není výslovně uvedeno jinak, veškerý obsah v tomto článku předpokládá použití AMQP nebo SBMP.

## <a name="reusing-factories-and-clients"></a>Používání továrn a klientů

Service Bus objekty klienta, například [QueueClient][QueueClient] nebo [MessageSender][MessageSender], se vytvářejí prostřednictvím objektu [MessagingFactory][MessagingFactory] , který poskytuje také interní správu připojení. Po odeslání zprávy doporučujeme, abyste nezavřeli továrny zasílání zpráv nebo fronty, témata a předplatná, a pak je znovu vytvoříte při odeslání další zprávy. Při zavírání továrny zasílání zpráv se odstraní připojení ke službě Service Bus a při opětovném vytváření továrny se vytvoří nové připojení. Navázání připojení je náročná operace, kterou se můžete vyhnout opakovanému použití stejného objektu factory a klienta pro více operací. Tyto objekty klienta můžete bezpečně použít pro souběžné asynchronní operace a z více vláken. 

## <a name="concurrent-operations"></a>Souběžné operace

Provádění operace (odeslání, přijetí, odstranění atd.) nějakou dobu trvá. Tato doba zahrnuje zpracování operace službou Service Bus kromě latence žádosti a odpovědi. Chcete-li zvýšit počet operací v čase, operace musí být spuštěny souběžně. 

Klient plánuje souběžné operace provedením asynchronních operací. Další požadavek se spustí před dokončením předchozí žádosti. Následující fragment kódu je příkladem asynchronní operace odeslání:
  
 ```csharp
  Message m1 = new BrokeredMessage(body);
  Message m2 = new BrokeredMessage(body);
  
  Task send1 = queueClient.SendAsync(m1).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #1");
    });
  Task send2 = queueClient.SendAsync(m2).ContinueWith((t) => 
    {
      Console.WriteLine("Sent message #2");
    });
  Task.WaitAll(send1, send2);
  Console.WriteLine("All messages sent");
  ```
  
  Následující kód je příkladem asynchronní operace Receive. Úplný program najdete [tady](https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues):
  
  ```csharp
  var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);
  var doneReceiving = new TaskCompletionSource<bool>();

  receiver.RegisterMessageHandler(...);
  ```

## <a name="receive-mode"></a>Režim příjmu

Když vytváříte klienta front nebo odběrů, můžete zadat režim přijímání: *prohlížet a uzamknout* nebo *přijmout a odstranit*. Výchozím režimem příjmu je [PeekLock][PeekLock]. Při provozu v tomto režimu pošle klient žádost o přijetí zprávy od Service Bus. Jakmile klient obdrží zprávu, pošle požadavek na dokončení zprávy.

Při nastavení režimu příjmu na [ReceiveAndDelete][ReceiveAndDelete]se oba kroky zkombinují v jednom požadavku. Tyto kroky omezují celkový počet operací a můžou zlepšit celkovou propustnost zpráv. Tento zvýšení výkonu přináší riziko ztráty zpráv.

Service Bus nepodporuje transakce pro operace Receive a DELETE. Kromě toho se vyžaduje sémantika prohlížení zámku pro všechny scénáře, ve kterých bude klient chtít zprávu odložit nebo [nedoručeně](service-bus-dead-letter-queues.md) .

## <a name="client-side-batching"></a>Dávkování na straně klienta

Dávkování na straně klienta umožňuje klientovi nebo tématu klienta zpozdit odeslání zprávy po určitou dobu. Pokud během tohoto časového období klient odešle další zprávy, přenese zprávy v jedné dávce. Dávkování na straně klienta také způsobí, že klient fronty nebo předplatného vygeneruje více **úplných** požadavků do jediného požadavku. Dávkování je k dispozici pouze pro operace asynchronního **odeslání** a **dokončení** . Synchronní operace se okamžitě odesílají do služby Service Bus. Dávkování se nevyskytuje pro operace prohlížení a příjmu ani dávkování neproběhne napříč klienty.

Ve výchozím nastavení klient používá interval dávky 20 MS. Interval dávky můžete změnit nastavením vlastnosti [BatchFlushInterval][BatchFlushInterval] před vytvořením objektu pro vytváření zpráv. Toto nastavení má vliv na všechny klienty vytvořené touto továrnou.

Chcete-li zakázat dávkování, nastavte vlastnost [BatchFlushInterval][BatchFlushInterval] na **hodnotu TimeSpan. Zero**. Například:

```csharp
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

Dávkování nemá vliv na počet fakturovatelných operací zasílání zpráv a je k dispozici pouze pro Service Bus klientský protokol pomocí knihovny [Microsoft. ServiceBus. Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) . Protokol HTTP nepodporuje dávkování.

> [!NOTE]
> Nastavení BatchFlushInterval zajistí, že dávkování bude z pohledu aplikace implicitní. To znamená, že aplikace provádí volání SendAsync () a CompleteAsync () a neprovádí specifická volání Batch.
>
> Explicitní dávkování na straně klienta lze implementovat pomocí níže uvedeného volání metody – 
> ```csharp
> Task SendBatchAsync (IEnumerable<BrokeredMessage> messages);
> ```
> Souhrnná velikost zpráv musí být menší než maximální velikost podporovaná cenovou úrovní.

## <a name="batching-store-access"></a>Batch – přístup k úložišti

Pokud chcete zvýšit propustnost fronty, tématu nebo předplatného, Service Bus se při zápisu do svého interního úložiště dávky vyřadí několik zpráv. Pokud je tato možnost povolená ve frontě nebo tématu, zapisuje se do tohoto úložiště zprávy zapsané do dávky. Pokud je ve frontě nebo předplatném povolené, odstraní se zprávy ze Storu. Pokud je pro entitu povolený přístup k dávkovému ukládání, Service Bus zpozdí operaci zápisu úložiště týkající se této entity až o 20 MS. 

> [!NOTE]
> Nehrozí riziko ztráty zpráv s dávkou, a to ani v případě, že dojde k selhání Service Bus na konci intervalu dávkování 20ms. 

Další operace úložiště, ke kterým dojde během tohoto intervalu, se přidají do dávky. Přístup k dávkovému úložišti má vliv jenom na operace **Send** a **Complete** . operace Receive nejsou ovlivněny. Přístup k Batch Storu je vlastnost v entitě. Dávkování probíhá napříč všemi entitami, které umožňují přístup k dávce v dávkovém úložišti.

Při vytváření nové fronty, tématu nebo předplatného je ve výchozím nastavení povolený přístup k Batch Storu. Chcete-li zakázat přístup k dávce, nastavte před vytvořením entity vlastnost [EnableBatchedOperations][EnableBatchedOperations] na **hodnotu false** . Například:

```csharp
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

Přístup k Batch Storu nemá vliv na počet fakturovaných operací zasílání zpráv a jedná se o vlastnost fronty, tématu nebo předplatného. Nezávisí na režimu příjmu a protokolu, který se používá mezi klientem a službou Service Bus.

## <a name="prefetching"></a>Předběžné načítání

[Předběžné načítání](service-bus-prefetch.md) umožňuje klientovi fronty nebo předplatného načítat další zprávy ze služby při provádění operace Receive. Klient ukládá tyto zprávy do místní mezipaměti. Velikost mezipaměti je určena vlastnostmi [QueueClient. PrefetchCount][QueueClient.PrefetchCount] nebo [SubscriptionClient. PrefetchCount][SubscriptionClient.PrefetchCount] . Každý klient, který umožňuje předběžné navýšení, zachovává svou vlastní mezipaměť. Mezipaměť není sdílená mezi klienty. Pokud klient inicializuje operaci Receive a jeho mezipaměť je prázdná, služba přenáší dávku zpráv. Velikost dávky se rovná velikosti mezipaměti nebo 256 KB, podle toho, která hodnota je menší. Pokud klient zahájí operaci přijetí a mezipaměť obsahuje zprávu, zpráva je pořízena z mezipaměti.

Při předběžném načtení zprávy služba uzamkne přednačtenou zprávu. V případě zámku nemůže být přednačtená zpráva přijata jiným přijímačem. Pokud příjemce nemůže dokončit zprávu před vypršením platnosti zámku, bude zpráva k dispozici ostatním příjemcům. Přednačtená kopie zprávy zůstane v mezipaměti. Přijímač, který využívá kopii v mezipaměti s vypršenou platností, obdrží při pokusu o dokončení této zprávy výjimku. Ve výchozím nastavení vyprší platnost zámku zprávy po 60 sekundách. Tato hodnota se dá prodloužit na 5 minut. Aby nedocházelo k vyčerpání neplatných zpráv, velikost mezipaměti by měla být vždy menší než počet zpráv, které může klient spotřebovat v intervalu časového limitu zámku.

Při použití výchozího zámku vypršení 60 sekund je platná hodnota pro [PrefetchCount][SubscriptionClient.PrefetchCount] 20 časů maximálního počtu zpracování všech přijímačů továrny. Například továrna vytvoří tři přijímače a každý příjemce může zpracovat až 10 zpráv za sekundu. Počet předběžných hodnot by neměl být vyšší než 20 × 3 × 10 = 600. Ve výchozím nastavení je [PrefetchCount][QueueClient.PrefetchCount] nastaveno na hodnotu 0, což znamená, že ze služby nejsou načteny žádné další zprávy.

Předběžné načítání zpráv zvyšuje celkovou propustnost pro frontu nebo odběr, protože snižuje celkový počet operací se zprávami nebo se na ně zaznamená výměna zpráv. Načítají se první zpráva, ale bude trvat déle (kvůli zvýšené velikosti zprávy). Přijímání předem načtených zpráv bude rychlejší, protože klient již tyto zprávy stáhl.

Hodnota vlastnosti TTL (Time-to-Live) zprávy je serverem zkontrolována v době, kdy server odesílá klientovi zprávu. Klient při přijetí zprávy nekontroluje vlastnost TTL zprávy. Místo toho lze zprávu přijmout i v případě, že hodnota TTL zprávy byla předána, zatímco byla zpráva uložena do mezipaměti klienta.

Předběžné načtení nemá vliv na počet fakturovatelných operací zasílání zpráv a je k dispozici pouze pro Service Bus klientský protokol. Protokol HTTP nepodporuje předběžné načtení. Předběžné načtení je k dispozici pro synchronní i asynchronní operace Receive.

## <a name="prefetching-and-receivebatch"></a>Přednačtení a ReceiveBatch

I když koncepce předběžného načítání více zpráv má podobnou sémantiku zpracování zpráv v dávce (ReceiveBatch), existují drobné rozdíly, které je potřeba při jejich vzájemném využívání zachovat.

Předběžné načtení je konfigurace (nebo režim) na straně klienta (QueueClient a SubscriptionClient) a ReceiveBatch je operace (která má sémantiku požadavku-odpověď).

Při současném použití těchto případů Vezměte v úvahu následující případy:

* Předběžné načtení by mělo být větší než nebo rovno počtu zpráv, které očekáváte od ReceiveBatch.
* Předběžného načtení může být až n/3 krát počet zpracovaných zpráv za sekundu, kde n je výchozí doba trvání zámku.

Existují některé problémy s hladkou metodou (tj. udržování příliš vysokého počtu předběžného načítání), protože to znamená, že je zpráva pevně nastavená na konkrétního příjemce. Doporučujeme, abyste si vyzkoušeli hodnoty předběžného načítání mezi výše uvedenými mezními hodnotami a empirickě Identifikujte, co se vejde.

## <a name="multiple-queues"></a>Více front

Pokud očekávaná zátěž nemůže být zpracována jednou frontou nebo tématem, je nutné použít více entit zasílání zpráv. Při použití více entit vytvořte vyhrazeného klienta pro každou entitu namísto použití stejného klienta pro všechny entity.

## <a name="development-and-testing-features"></a>Funkce pro vývoj a testování

Service Bus má jednu funkci, která se používá specificky pro vývoj, která **by se nikdy neměla používat v produkčních konfiguracích**: [TopicDescription.EnableFilteringMessagesBeforePublishing][].

Když jsou do tématu přidána nová pravidla nebo filtry, můžete použít [TopicDescription.EnableFilteringMessagesBeforePublishing][] a ověřit tak, že nový výraz filtru pracuje podle očekávání.

## <a name="scenarios"></a>Scénáře

Následující části popisují typické scénáře zasílání zpráv a popisují upřednostňovaná nastavení Service Bus. Míry propustnosti jsou klasifikovány jako malé (méně než jedna zpráva za sekundu), střední (1 zpráva/sekunda nebo větší, ale méně než 100 zpráv za sekundu) a vysoká (100 zpráv za sekundu nebo vyšší). Počet klientů je klasifikován jako malý (5 nebo méně), střední (více než 5, ale menší nebo rovno 20) a velký (více než 20).

### <a name="high-throughput-queue"></a>Fronta s vysokou propustností

Cíl: maximalizuje propustnost jedné fronty. Počet odesílatelů a přijímačů je malý.

* Pro zvýšení celkové míry odesílání do fronty použijte k vytvoření odesílatelů více tovární zpráv. Pro každého odesilatele použijte asynchronní operace nebo více vláken.
* Pro zvýšení celkové míry příjmu z fronty použijte k vytvoření přijímačů více tovární zpráv.
* Pomocí asynchronních operací můžete využít dávkování na straně klienta.
* Nastavte interval dávkování na 50 ms, abyste snížili počet Service Busch přenosů klientských protokolů. Pokud se používá víc odesílatelů, zvyšte interval dávkování na 100 ms.
* Nechte povolený přístup k Batch Storu. Tento přístup zvyšuje celkovou rychlost, kterou lze do fronty zapsat zprávy.
* Nastavte počet předběžných hodnot na 20 časů, který bude mít maximální počet zpracování všech přijímačů továrny. Tento počet snižuje počet Service Busch přenosů klientských protokolů.

### <a name="multiple-high-throughput-queues"></a>Několik front s vysokou propustností

Cíl: maximalizuje celkovou propustnost více front. Propustnost jednotlivých front je střední nebo vysoká.

Pokud chcete dosáhnout maximální propustnosti napříč několika frontami, použijte nastavení s popisem pro maximalizaci propustnosti jedné fronty. Kromě toho můžete použít různé továrny k vytváření klientů, kteří odesílají nebo dostanou z různých front.

### <a name="low-latency-queue"></a>Fronta s nízkou latencí

Cíl: minimalizuje koncovou latenci fronty nebo tématu. Počet odesílatelů a přijímačů je malý. Propustnost fronty je malá nebo střední.

* Zakažte dávkování na straně klienta. Klient okamžitě pošle zprávu.
* Zakažte přístup k dávkovému ukládání. Služba okamžitě zapíše zprávu do úložiště.
* Pokud používáte jednoho klienta, nastavte počet předběžných hodnot na 20 časů zpracování přijímače. Pokud fronta současně dorazí do fronty více zpráv, protokol klienta Service Bus odesílá všechna data ve stejnou dobu. Když klient obdrží další zprávu, tato zpráva je již v místní mezipaměti. Mezipaměť by měla být malá.
* Pokud používáte více klientů, nastavte počet předběžných hodnot na 0. Když nastavíte počet, druhý klient může obdržet druhou zprávu, zatímco první klient stále zpracovává první zprávu.

### <a name="queue-with-a-large-number-of-senders"></a>Zařadit do fronty s velkým počtem odesílatelů

Cíl: maximalizuje propustnost fronty nebo tématu s velkým počtem odesílatelů. Každý odesilatel odesílá zprávy se střední sazbou. Počet přijímačů je malý.

Service Bus umožňuje až 1000 souběžných připojení k entitě zasílání zpráv (nebo 5000 pomocí AMQP). Toto omezení se vynutilo na úrovni oboru názvů a fronty/témata/předplatná se omezené omezením počtu souběžných připojení na obor názvů. U front se toto číslo sdílí mezi odesílateli a přijímači. Pokud se pro odesílatele vyžadují všechna připojení 1000, nahraďte frontu tématem a jedním předplatným. Téma přijímá až 1000 souběžných připojení od odesílatelů, zatímco předplatné přijímá další 1000 souběžných připojení od příjemců. Pokud potřebujete více než 1000 současných odesílatelů, odesílají odesílatelé zprávy do protokolu Service Bus přes HTTP.

K maximalizaci propustnosti proveďte následující kroky:

* Pokud se každý odesilatel nachází v jiném procesu, použijte pro každý proces jenom jeden objekt factory.
* Pomocí asynchronních operací můžete využít dávkování na straně klienta.
* Snižte počet Service Busch přenosů klientských protokolů pomocí výchozího intervalu dávkování 20 MS.
* Nechte povolený přístup k Batch Storu. Tento přístup zvyšuje celkovou rychlost, kterou lze zprávy zapsat do fronty nebo tématu.
* Nastavte počet předběžných hodnot na 20 časů, který bude mít maximální počet zpracování všech přijímačů továrny. Tento počet snižuje počet Service Busch přenosů klientských protokolů.

### <a name="queue-with-a-large-number-of-receivers"></a>Zařazení do fronty s velkým počtem přijímačů

Cíl: Maximalizujte míru přijetí fronty nebo předplatného s velkým počtem přijímačů. Každý příjemce obdrží zprávy se střední sazbou. Počet odesílatelů je malý.

Service Bus povoluje až 1000 souběžných připojení k entitě. Pokud fronta vyžaduje více než 1000 přijímačů, nahraďte frontu tématem a několika odběry. Každé předplatné může podporovat až 1000 souběžných připojení. Přijímačé taky můžou ke frontě přistupovat přes protokol HTTP.

K maximalizaci propustnosti udělejte toto:

* Pokud se každý přijímač nachází v jiném procesu, použijte pro každý proces jenom jeden objekt factory.
* Přijímačé můžou používat synchronní nebo asynchronní operace. Vzhledem k střední míře příjmu jednotlivého přijímače dávka na straně klienta s kompletní žádostí nijak neovlivní propustnost přijímače.
* Nechte povolený přístup k Batch Storu. Tento přístup omezuje celkové zatížení entity. Zároveň se tím snižuje celková rychlost, kterou lze zprávy zapsat do fronty nebo tématu.
* Nastavte počet předběžných hodnot na malou hodnotu (například PrefetchCount = 10). Tento počet brání přijímačům v nečinnosti, zatímco ostatní příjemci mají velký počet zpráv uložených v mezipaměti.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Téma s malým počtem předplatných

Cíl: maximalizace propustnosti tématu s malým počtem předplatných. Celá řada předplatných obdrží zprávu, což znamená, že celková míra příjmu u všech předplatných je větší než rychlost odesílání. Počet odesílatelů je malý. Počet přijímačů na předplatné je malý.

K maximalizaci propustnosti udělejte toto:

* Pro zvýšení celkové míry odeslání do tématu použijte k vytvoření odesílatelů více tovární zpráv. Pro každého odesilatele použijte asynchronní operace nebo více vláken.
* Pro zvýšení celkové míry příjmu z předplatného použijte k vytvoření přijímačů více zpráv. Pro každého příjemce použijte asynchronní operace nebo více vláken.
* Pomocí asynchronních operací můžete využít dávkování na straně klienta.
* Snižte počet Service Busch přenosů klientských protokolů pomocí výchozího intervalu dávkování 20 MS.
* Nechte povolený přístup k Batch Storu. Tento přístup zvyšuje celkovou rychlost, kterou lze do tématu zapsat zprávy.
* Nastavte počet předběžných hodnot na 20 časů, který bude mít maximální počet zpracování všech přijímačů továrny. Tento počet snižuje počet Service Busch přenosů klientských protokolů.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Téma s velkým počtem předplatných

Cíl: maximalizace propustnosti tématu s velkým počtem předplatných. Celá řada předplatných obdrží zprávu, což znamená, že celková míra příjmu u všech předplatných je mnohem větší než rychlost odesílání. Počet odesílatelů je malý. Počet přijímačů na předplatné je malý.

Témata s velkým počtem předplatných obvykle zveřejňují nízkou celkovou propustnost, pokud jsou všechny zprávy směrovány do všech předplatných. Tato nízká propustnost je způsobena tím, že každá zpráva je přijata mnohokrát a všechny zprávy, které jsou obsaženy v tématu a všechny její odběry, jsou uloženy ve stejném úložišti. Předpokládá se, že počet odesílatelů a počet příjemců na předplatné je malý. Service Bus podporuje až 2 000 předplatných na jedno téma.

Pokud chcete maximalizovat propustnost, zkuste provést následující kroky:

* Pomocí asynchronních operací můžete využít dávkování na straně klienta.
* Snižte počet Service Busch přenosů klientských protokolů pomocí výchozího intervalu dávkování 20 MS.
* Nechte povolený přístup k Batch Storu. Tento přístup zvyšuje celkovou rychlost, kterou lze do tématu zapsat zprávy.
* Nastavte počet předplatných na hodnotu 20 časů očekávané míry příjmu v sekundách. Tento počet snižuje počet Service Busch přenosů klientských protokolů.

[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[PeekLock]: /dotnet/api/microsoft.azure.servicebus.receivemode
[ReceiveAndDelete]: /dotnet/api/microsoft.azure.servicebus.receivemode
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[EnableBatchedOperations]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations
[QueueClient.PrefetchCount]: /dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount
[SubscriptionClient.PrefetchCount]: /dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[Partitioned messaging entities]: service-bus-partitioning.md
[TopicDescription.EnableFilteringMessagesBeforePublishing]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing
