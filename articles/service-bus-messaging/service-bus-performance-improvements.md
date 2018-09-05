---
title: Osvědčené postupy pro zlepšení výkonu pomocí Azure Service Bus | Dokumentace Microsoftu
description: Popisuje, jak můžete optimalizovat výkon při výměně zprostředkovaných zpráv Service Bus.
services: service-bus-messaging
documentationcenter: na
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: article
ms.date: 06/14/2018
ms.author: spelluru
ms.openlocfilehash: 9dd9150411b465d210c7e02dc52d6851f670845f
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43697631"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Osvědčené postupy pro zlepšení výkonu pomocí zasílání zpráv Service Bus

Tento článek popisuje, jak používat Azure Service Bus optimalizovat výkon při výměně zprostředkovaných zpráv. První část tohoto článku popsané různé mechanizmy, které nabízí za účelem zvýšení výkonu. Druhá část obsahuje pokyny, jak Service Bus používat způsobem, který může nabídnout nejlepší výkon v daném scénáři.

Výraz "client" v celém tomto článku označuje Každá entita, která má přístup k Service Bus. Klienta můžete převést roli odesílatele a příjemce. Termín "sender" se používá pro služby Service Bus fronty nebo tématu klienta, která odesílá zprávy do odběru fronty nebo tématu služby Service Bus. Termín "příjemce" odkazuje na Service Bus fronty nebo odběru klienta, která přijímá zprávy z fronty služby Service Bus nebo odběru.

Tyto části přinášejí několik konceptů, které používá služby Service Bus ke zvýšení výkonu.

## <a name="protocols"></a>Protokoly

Service Bus umožní klientům posílat a přijímat zprávy přes jeden ze tří protokolů:

1. Pokročilé řízení front zpráv (AMQP) protokolu
2. Protokol (SBMP) zasílání zpráv Service Bus
3. HTTP

AMQP a SBMP jsou efektivnější, protože udržují připojení k Service Bus, dokud existuje objekt pro vytváření zpráv. Implementuje navíc dávkové zpracování a předběžné načítání. Pokud není výslovně uvedeno, veškerý obsah v tomto článku se předpokládá použití připojení přes AMQP nebo SBMP.

## <a name="reusing-factories-and-clients"></a>Opětovné použití objekty pro vytváření a klientů

Klient služby Service Bus objekty, jako například [QueueClient] [ QueueClient] nebo [MessageSender][MessageSender], jsou vytvořené prostřednictvím [ MessagingFactory] [ MessagingFactory] objektu, který také obsahuje interní správu připojení. Doporučuje se, že neukončíte zasílání zpráv objekty pro vytváření nebo fronty, témata a odběru klientům po odeslání zprávy a pak odeslat další zprávy znovu vytvořit. Připojení ke službě Service Bus messaging factory zavření odstraní a nové připojení se naváže při opětovném vytváření objekt pro vytváření. Navazování připojení je náročná operace, které můžete se vyhnout opětovné použití stejný objekt pro vytváření a objekty klientů pro více operací. Můžete bezpečně používat [QueueClient] [ QueueClient] objektu pro odesílání zpráv ze souběžných asynchronních operací a více vláken. 

## <a name="concurrent-operations"></a>Souběžné operace

Provádí operaci (odesílání, příjem, odstranit, atd.) chvíli trvá. Tento čas zahrnuje zpracování operace ve službě Service Bus kromě latenci požadavku a odpovědi. Pokud chcete zvýšit počet operací za čas, musí současně provést operace. 

Klient naplánuje souběžných operací pomocí provádí asynchronní operace. Další požadavek je spuštěn před dokončením předchozí žádosti. Následující fragment kódu je příklad asynchronní odeslání operace:
  
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
  
  Následující kód představuje příklad asynchronní operace příjmu. Zobrazit úplné program [tady](https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues):
  
  ```csharp
  var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);
  var doneReceiving = new TaskCompletionSource<bool>();

  receiver.RegisterMessageHandler(
  ```

## <a name="receive-mode"></a>Zobrazí režim

Při vytváření fronty nebo odběru klienta, můžete zadat režim příjmu: *neboli Peek-lock* nebo *přijme a odstraní*. Zobrazí výchozí režim je [PeekLock][PeekLock]. Při fungování v tomto režimu, klient odešle žádost o přijetí zprávy ze služby Service Bus. Po přijetí zprávy klient odešle žádost dokončit zprávu.

Při nastavování režimu příjmu na [ReceiveAndDelete][ReceiveAndDelete], oba kroky jsou zkombinované v jedné žádosti. Tyto kroky snížit celkový počet operací a zkvalitnit celkovou propustnost zpráv. Toto zvýšení výkonu se dodává nese došlo ke ztrátě zpráv.

Service Bus nepodporuje transakce pro operace přijímat a odstranění. Kromě toho jsou požadovány pro všechny scénáře, ve kterých chce odložit klienta neboli peek-lock sémantiku nebo [dead-letter](service-bus-dead-letter-queues.md) zprávu.

## <a name="client-side-batching"></a>Dávkování na straně klienta

Dávkování na straně klienta umožňuje klientovi fronty nebo tématu ke zpoždění odesílání zprávy pro určitou dobu. Pokud během tohoto časového období klient odešle další zprávy, přenese zprávy v jedné dávce. Dávkování na straně klienta rovněž způsobí, že fronta nebo odběr klienta dávce více **Complete** požadavky do jednoho požadavku. Dávkování je dostupná jenom pro asynchronní **odeslat** a **Complete** operace. Synchronní operace okamžitě odešlou do služby Service Bus. Dávkové zpracování dojít pro náhled nebo operace příjmu ani dávkování dochází mezi klienty.

Ve výchozím nastavení používá klienta batch interval 20 MS. Batch interval můžete změnit tak, že nastavíte [BatchFlushInterval] [ BatchFlushInterval] vlastnost před vytvořením messaging factory. Toto nastavení má vliv na všechny klienty, kteří vytvářejí tento objekt pro vytváření.

Chcete-li zakázat dávkování, nastavte [BatchFlushInterval] [ BatchFlushInterval] vlastnost **TimeSpan.Zero**. Příklad:

```csharp
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

Dávkování nemá vliv na počet účtovaných operací zasílání zpráv a je k dispozici pouze pro použití protokolu klienta služby Service Bus [Microsoft.ServiceBus.Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) knihovny. Protokol HTTP nepodporuje dávkování.

## <a name="batching-store-access"></a>Dávkování přístupu ke službě store

Pokud chcete zvýšit propustnost fronty, tématu nebo předplatného, Service Bus dávek více zpráv při zapisuje do svého interního úložiště. Pokud je povoleno ve frontě nebo tématu, zápis zpráv do úložiště se mají přidat do dávky. Pokud je povoleno ve frontě nebo odběru, odstraňování zpráv z úložiště se mají přidat do dávky. Pokud je povolen přístup dávkové úložiště pro entitu, Service Bus zpoždění operace zápisu úložiště o dané entitě podle až 20 ms. 

> [!NOTE]
> Neexistuje žádné riziko ztráty zprávy s dávkování, i v případě, že dojde k chybě služby Service Bus na konci dávkování interval 20ms. 

Operace další úložiště, které během tohoto intervalu dojde k jsou přidány do služby batch. Dávce ovlivní pouze přístup k úložišti **odeslat** a **Complete** operace přijímat operace nebude mít vliv. Dávkové úložiště přístup je vlastnost entity. Dávkování dochází mezi všechny entity, které umožňují přístup dávkové úložiště.

Při vytváření nové fronty, tématu nebo předplatného, je ve výchozím nastavení povolen přístup dávkové úložiště. Chcete-li zakázat přístup dávkové úložiště, nastavte [EnableBatchedOperations] [ EnableBatchedOperations] vlastnost **false** před vytvořením entity. Příklad:

```csharp
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

Přístup dávkové úložiště nemá vliv na počet účtovaných operací zasílání zpráv a je vlastností fronty, tématu nebo odběru. Je nezávislá režimu příjmu a protokol, který se používá mezi klientem a službou Service Bus.

## <a name="prefetching"></a>Předběžné načítání

[Předběžné načítání](service-bus-prefetch.md) umožňuje klientovi fronty nebo odběru se při provádění operace obdržení načíst další zprávy ze služby. Klient ukládá tyto zprávy v místní mezipaměti. Velikost mezipaměti je určená [QueueClient.PrefetchCount] [ QueueClient.PrefetchCount] nebo [SubscriptionClient.PrefetchCount] [ SubscriptionClient.PrefetchCount] vlastnosti. Každý klient, který umožňuje předběžné načítání udržuje svůj vlastní mezipaměti. Mezipaměť není sdílet mezi klienty. Pokud klient zahájí operaci příjmu a uloženou v mezipaměti je prázdný, službu přenáší dávku zpráv. Velikost dávky se rovná velikosti mezipaměti nebo 256 KB, podle toho, co je menší. Pokud klient zahájí operaci příjmu a mezipaměti obsahuje zprávy, zprávu je převzat z mezipaměti.

Pokud zpráva je předem načteného, služba zpráva jen uzamkne předem načteného. S uzamčením předem načteného zprávu nelze přijmout jiný příjemce. Pokud příjemce nelze dokončit zprávu, než vyprší platnost zámku, stane se dostupným pro ostatní příjemce zprávy. Předem načteného kopie zprávy zůstává v mezipaměti. Příjemce, který využívá kopie v mezipaměti vypršela platnost, obdrží výjimku při pokusu o dokončení této zprávy. Ve výchozím nastavení zámek zprávy vyprší za 60 sekund. Tuto hodnotu je možné rozšířit na 5 minut. Pokud chcete zabránit spotřeby zprávy s vypršenou platností, musí být velikost mezipaměti vždy menší než počet zpráv, které mohou být využívány službou klienta během intervalu časového limitu zámku.

Při použití výchozí platnost zámku 60 sekund, hodnota vhodná pro [PrefetchCount] [ SubscriptionClient.PrefetchCount] je 20krát maximální počet zpracovaných položek všech přijímačů objektu pro vytváření. Například objekt pro vytváření vytvoří tři příjemců a každý příjemce může zpracovat až 10 zpráv za sekundu. Předběžné načtení počet může být maximálně 20 × 3 × 10 = 600. Ve výchozím nastavení [PrefetchCount] [ QueueClient.PrefetchCount] je nastavena na hodnotu 0, což znamená, že žádné další zprávy načtené ze služby.

Předběžné načítání zpráv zvyšuje celkovou propustnost pro fronty nebo odběru, protože snižuje celkový počet zpráv operace nebo výměny zpráv. Načítá se první zprávou, ale bude trvat déle (z důvodu velikosti zvýšenou zprávy). Příjem zpráv s předem načteného bude rychlejší, protože tyto zprávy již byly staženy klienta.

Vlastnost time to live (TTL) zprávy je kontroluje server v době, kdy server odešle zprávu do klienta. Klient nekontroluje vlastnosti TTL zprávy při doručení zprávy. Místo toho můžete zprávu přijmout i v případě, že hodnota TTL zprávy prošel při zpráva byla uložena do mezipaměti klienta.

Předběžné načítání nemá vliv na počet účtovaných operací zasílání zpráv a je dostupná jenom pro protokol klienta služby Service Bus. Protokol HTTP nepodporuje předběžné načítání. Předběžné načítání je k dispozici pro synchronní a asynchronní operace příjmu.

## <a name="express-queues-and-topics"></a>Express fronty a témata

Expresní entity povolit scénáře snížené latenci a vysokou propustnost a jsou podporovány pouze na úrovni Standard zasílání zpráv. Entity vytvořené v [názvové prostory úrovně Premium](service-bus-premium-messaging.md) nepodporují možnost express. Expresní entity v případě zprávy do fronty nebo tématu, zpráva se uloží okamžitě v úložišti pro přenos zpráv. Místo toho je tam uložena v paměti. Pokud zpráva zůstane ve frontě déle než několik sekund, se automaticky zapisuje do stabilní úložiště, tedy chránit před ztrátou kvůli výpadku. Zapsat zprávu do mezipaměti zvyšuje propustnost a snižuje latence, protože neexistuje žádný přístup k úložišti stabilní v době, kdy je zpráva odeslána. Zprávy, které se spotřebovávají během několika sekund nejsou zapsány do úložiště zpráv. Následující příklad vytvoří express tématu.

```csharp
TopicDescription td = new TopicDescription(TopicName);
td.EnableExpress = true;
namespaceManager.CreateTopic(td);
```

Pokud je odeslána zpráva obsahující důležité informace, které nesmí dojít ke ztrátě je do expresní entity, odesílatel můžete vynutit služby Service Bus se okamžitě zachovat zpráva, kterou chcete stabilní úložiště tak, že nastavíte [ForcePersistence] [ ForcePersistence] vlastnost **true**.

> [!NOTE]
> Expresní entity nepodporují transakce.

## <a name="partitioned-queues-or-topics"></a>Dělené fronty a témata

Interně stejný uzel používá služby Service Bus a zasílání zpráv ukládat pro zpracování a ukládání všech zpráv pro entity pro zasílání zpráv (fronty nebo tématu). A [dělená fronta nebo téma](service-bus-partitioning.md), na druhé straně je distribuovaná mezi více uzlů zasílání zpráv a úložiště. Dělené fronty a témata nejen přinést vyšší výkon než regulární fronty a témata, jsou také vykazuje nejvyšší dostupnost. Chcete-li vytvořit dělené entity, nastavte [EnablePartitioning] [ EnablePartitioning] vlastnost **true**, jak je znázorněno v následujícím příkladu. Další informace o dělené entity, naleznete v tématu [segmentované entity zasílání zpráv][Partitioned messaging entities].

> [!NOTE]
> Dělené entity se nepodporují v [SKU úrovně Premium](service-bus-premium-messaging.md). 

```csharp
// Create partitioned queue.
QueueDescription qd = new QueueDescription(QueueName);
qd.EnablePartitioning = true;
namespaceManager.CreateQueue(qd);
```

## <a name="multiple-queues"></a>Více front

Pokud není možné použít dělená fronta nebo téma nebo nelze zpracovat očekávané zatížení jednoho dělené fronty nebo tématu, je nutné použít více entit pro zasílání zpráv. Při použití více entit, vytvořte vyhrazený klienta pro každou entitu, namísto použití stejného klienta pro všechny entity.

## <a name="development-and-testing-features"></a>Vývoj a testování funkcí

Service Bus má jednu funkci, která slouží speciálně pro vývoj, která **byste nikdy neměli používat v produkčním prostředí konfigurace**: [TopicDescription.EnableFilteringMessagesBeforePublishing][].

Pokud nová pravidla nebo filtry jsou přidány do tématu, můžete použít [TopicDescription.EnableFilteringMessagesBeforePublishing][] k ověření, že nový výraz filtr funguje podle očekávání.

## <a name="scenarios"></a>Scénáře

Následující části popisují některé typické scénáře zasílání zpráv a popisují nastavení služby Service Bus. Propustnosti jsou klasifikovány jako malé (méně než 1 zpráv za sekundu), středním (1 zpráv za sekundu nebo větší, ale menší než 100 zpráv za sekundu) a vysokou (100 zpráv/druhý nebo vyšší). Počet klientů, které jsou klasifikovány jako malé (5 nebo méně), střední (více než 5, ale menší než nebo rovna 20) a velké (více než 20).

### <a name="high-throughput-queue"></a>Fronty s vysokou propustností

Cíl: Maximalizuje propustnost jediné fronty. Je malý počet odesílateli a příjemci.

* Pokud chcete zvýšit celkovou rychlost odesílání do fronty, použijte k vytvoření odesílatelů více. Pro každý odesílatele použijte asynchronní operace nebo více vláken.
* Ke zvýšení celková míra přijímání z fronty, použijte více vytvořte příjemce.
* Využijte výhod dávkování na straně klienta pomocí asynchronních operací.
* Nastavte interval dávkování na 50 ms a snížit počet přenosů pomocí protokolu klienta služby Service Bus. Pokud jsou používány více uživatelů, zvyšte interval dávkování na 100 ms.
* Ponechte dávkové úložiště přístup povolen. Tento přístup zvyšuje celkovou rychlost, jakou můžou být zprávy zapisovány do fronty.
* Nastaví počet předběžné načtení 20krát maximální zpracování plateb všichni příjemci objekt pro vytváření. Tento počet snižuje počet přenosy protokolu klienta služby Service Bus.
* Použití dělené fronty pro vylepšení výkonu a dostupnosti.

### <a name="multiple-high-throughput-queues"></a>Více front s vysokou propustností

Cíl: Maximalizujte celkovou propustnost více front. Propustnost samostatnou frontu se střední nebo vysoká.

Získat maximální propustnost napříč více front, použijte nastavení uvedené maximalizuje propustnost jediné fronty. Kromě toho můžete použijte různé objekty pro vytváření vytvořit klienty, kteří odeslat nebo přijmout z různých front.

### <a name="low-latency-queue"></a>Fronty s nízkou latencí

Cíl: Minimalizujte latenci začátku do konce fronty nebo tématu. Je malý počet odesílateli a příjemci. Propustnost fronty je malé nebo střední.

* Zakážete dávkování na straně klienta. Klient odešle okamžitě zprávu.
* Zakážete přístup dávkové úložiště. Služba okamžitě zapisuje zprávu do úložiště.
* Při použití jednoho klienta, nastavte počet předběžné načtení na 20 opakováních počet zpracovávaných příjemce. Pokud ve stejnou dobu doručování více zpráv do fronty, odesílá je protokol klienta služby Service Bus všechny najednou. Když klient obdrží další zprávu, tato zpráva je již v místní mezipaměti. Mezipaměti by měla být malá.
* Pokud používáte více klienty, nastavte počet předběžné načtení na hodnotu 0. Nastavením počet druhého klienta může přijímat druhé zprávy, zatímco první klient stále zpracovává první zprávu.
* Použití dělené fronty pro vylepšení výkonu a dostupnosti.

### <a name="queue-with-a-large-number-of-senders"></a>Fronty s velkým počtem uživatelů

Cíl: Maximalizuje propustnost fronty nebo tématu s velkým počtem uživatelů. Každý odesílatel odešle zprávy se střední sazbou. Počet příjemců je malý.

Umožňuje až 1 000 souběžných připojení k entity pro zasílání zpráv služby Service Bus (nebo 5000 pomocí protokolu AMQP). Toto omezení se vynucují na úrovni oboru názvů a fronty a témata nebo předplatná jsou uzavřeny limitu souběžných připojení na obor názvů. Pro fronty je toto číslo sdílena mezi odesílateli a příjemci. Pokud všechna 1 000 připojení pro odesílatelů, nahraďte fronty tématu a v rámci jednoho předplatného. Téma přijme až 1 000 souběžných připojení z odesílatelů, že odběr přijímá další 1 000 souběžných připojení z příjemců. Pokud potřebujete více než 1 000 souběžných uživatelů, by měl odesílatelé odesílají zprávy službě protokolu služby Service Bus přes protokol HTTP.

Maximalizuje propustnost, proveďte následující kroky:

* Pokud každý odesílatele se nachází v jiném procesu, použijte pouze jeden objekt jeden proces.
* Využijte výhod dávkování na straně klienta pomocí asynchronních operací.
* Použijte výchozí dávkování interval 20 ms a snížit počet přenosů pomocí protokolu klienta služby Service Bus.
* Ponechte dávkové úložiště přístup povolen. Tento přístup zvyšuje celkovou rychlost, jakou můžou být zprávy zapisovány do fronty nebo tématu.
* Nastaví počet předběžné načtení 20krát maximální zpracování plateb všichni příjemci objekt pro vytváření. Tento počet snižuje počet přenosy protokolu klienta služby Service Bus.
* Použití dělené fronty pro vylepšení výkonu a dostupnosti.

### <a name="queue-with-a-large-number-of-receivers"></a>Fronty s velkým počtem příjemců

Cíl: Maximalizujte rychlost příjmu z fronty nebo odběru s velkým počtem příjemců. Každý příjemce přijímá zprávy střední rychlostí. Počet uživatelů je malý.

Service Bus umožňuje až 1 000 souběžných připojení k entitě. Pokud fronta vyžaduje více než 1 000 příjemců, nahraďte fronty téma a několik předplatných. Každé předplatné může podporovat až 1 000 souběžných připojení. Příjemci také mají přístup k frontě pomocí protokolu HTTP.

Maximalizuje propustnost, postupujte takto:

* Pokud každý příjemce se nachází v jiném procesu, použijte pouze jeden objekt jeden proces.
* Příjemci můžete použít synchronní nebo asynchronní operace. Zadaný počet střední receive jednotlivé příjemce, dokončení žádosti o dávkové zpracování na straně klienta nemá vliv na propustnost příjemce.
* Ponechte dávkové úložiště přístup povolen. Tento přístup snižuje celkové zatížení entity. Také snižuje celkovou rychlost, jakou můžou být zprávy zapisovány do fronty nebo tématu.
* Nastavení předběžného načítání počtu na malou hodnotu (například PrefetchCount = 10). Tento počet přijímačů brání ze stavu nečinnosti, zatímco ostatní příjemce velkého počtu zpráv, které jsou uložené v mezipaměti.
* Použití dělené fronty pro vylepšení výkonu a dostupnosti.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Téma s malý počet předplatných

Cíl: Maximalizuje propustnost téma s malý počet předplatných. Zprávu přijme mnoha předplatnými, což znamená, že je větší než frekvence odesílání míra kombinované receive nad Všechna předplatná. Počet uživatelů je malý. Je malý počet přijímačů na jedno předplatné.

Maximalizuje propustnost, postupujte takto:

* Pokud chcete zvýšit celková míra odeslání do tématu, použijte k vytvoření odesílatelů více. Pro každý odesílatele použijte asynchronní operace nebo více vláken.
* Ke zvýšení celkové míry příjem z odběru použijte více vytvořte příjemce. Pro každý příjemce použijte asynchronní operace nebo více vláken.
* Využijte výhod dávkování na straně klienta pomocí asynchronních operací.
* Použijte výchozí dávkování interval 20 ms a snížit počet přenosů pomocí protokolu klienta služby Service Bus.
* Ponechte dávkové úložiště přístup povolen. Tento přístup zvyšuje celkovou rychlost, jakou můžou být zprávy zapisovány do tématu.
* Nastaví počet předběžné načtení 20krát maximální zpracování plateb všichni příjemci objekt pro vytváření. Tento počet snižuje počet přenosy protokolu klienta služby Service Bus.
* Použijte téma oddílů pro vylepšení výkonu a dostupnosti.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Téma s velký počet předplatných

Cíl: Maximalizuje propustnost tématu s velkým množstvím předplatných. Zpráva přijata mnoha předplatnými, což znamená, že je mnohem větší než míra odesílání míra kombinované receive nad Všechna předplatná. Počet uživatelů je malý. Je malý počet přijímačů na jedno předplatné.

Témata s velkým množstvím předplatných obvykle vystavují nízkou celkovou propustnost, pokud všechny zprávy jsou směrovány na všechna předplatná. Nízká propustnost je způsobeno skutečnost, že každou zprávu přijme v mnoha případech, a všechny zprávy, které jsou obsaženy v tématu a všechny její odběry jsou uložené ve stejném úložišti. Předpokládá se, že je malý počet uživatelů a počet přijímačů na jedno předplatné. Service Bus podporuje až 2 000 odběrů na téma.

Maximalizuje propustnost, vyzkoušejte následující kroky:

* Využijte výhod dávkování na straně klienta pomocí asynchronních operací.
* Použijte výchozí dávkování interval 20 ms a snížit počet přenosů pomocí protokolu klienta služby Service Bus.
* Ponechte dávkové úložiště přístup povolen. Tento přístup zvyšuje celkovou rychlost, jakou můžou být zprávy zapisovány do tématu.
* Nastaví počet předběžné načtení 20krát očekávané receive sazbou během několika sekund. Tento počet snižuje počet přenosy protokolu klienta služby Service Bus.
* Použijte téma oddílů pro vylepšení výkonu a dostupnosti.

## <a name="next-steps"></a>Další postup

Další informace o optimalizaci výkonu služby Service Bus, najdete v článku [segmentované entity zasílání zpráv][Partitioned messaging entities].

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
