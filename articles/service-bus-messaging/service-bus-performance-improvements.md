---
title: Osvědčené postupy pro zlepšení výkonu pomocí Azure Service Bus | Microsoft Docs
description: Popisuje postup použití služby Service Bus k optimalizaci výkonu při výměně zprostředkované zprávy.
services: service-bus-messaging
documentationcenter: na
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: e756c15d-31fc-45c0-8df4-0bca0da10bb2
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/05/2018
ms.author: sethm
ms.openlocfilehash: e6762d988da7d34893852505d8ce0fd30622eaaf
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34802540"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Osvědčené postupy pro zlepšení výkonu pomocí zasílání zpráv Service Bus

Tento článek popisuje, jak používat Azure Service Bus za účelem optimalizace výkonu při výměně zprostředkované zprávy. První část tohoto článku popisuje různé mechanismy, které nabízí za účelem zvýšení výkonu. Druhá část obsahuje pokyny k použití služby Service Bus tak, že můžete nabídnout nejlepší výkon v daném scénáři.

V tomto tématu se pojem "client" představuje Každá entita, která přistupuje k Service Bus. Klient může trvat roli odesílatele nebo příjemce. Termín "sender" se používá pro Service Bus fronta nebo téma klienta, který odesílá zprávy do předplatného fronta nebo téma sběrnice. Termín "příjemce" odkazuje na Service Bus fronty nebo předplatného klienta, který přijímá zprávy z fronty Service Bus nebo předplatné.

Tyto části seznámí několik konceptů, které používá Service Bus, které pomáhají výkonu.

## <a name="protocols"></a>Protokoly

Service Bus umožní klientům posílat a přijímat zprávy přes jeden ze tří protokolů:

1. Pokročilé zpráv služby Řízení front Protocol (AMQP)
2. Sběrnice zpráv protokolu (SBMP)
3. HTTP

Protokoly AMQP a SBMP jsou efektivnější, protože udržují připojení k Service Bus, dokud existuje objekt pro vytváření zpráv. Také implementuje dávkování a prefetching. Pokud není výslovně uvedeno, veškerý obsah v tomto tématu se předpokládá použití AMQP nebo SBMP.

## <a name="reusing-factories-and-clients"></a>Opětovné použití objektů Factory a klientů

Service Bus klient objekty, jako například [QueueClient] [ QueueClient] nebo [MessageSender][MessageSender], jsou vytvořeny pomocí [ MessagingFactory] [ MessagingFactory] objekt, který také poskytuje interní správu připojení. Doporučuje se, že nezavřete zasílání zpráv továrny nebo fronty, témata a předplatné klienty po odeslat zprávu a pak další zprávu odešlete znovu vytvořit. Zavřením objekt zasílání zpráv odstraní připojení ke službě Service Bus a vytvoření nového připojení při opětovném vytváření objektu pro vytváření. Navázání připojení je náročná operace, které se můžete vyhnout opětovné použití stejný objekt pro vytváření a objekty klienta pro více operací. Můžete bezpečně používat [QueueClient] [ QueueClient] objekt pro odesílání zpráv z více vláken a souběžných asynchronní operace. 

## <a name="concurrent-operations"></a>Souběžných operací

Provádění operace (odesílání, příjem, odstranit, apod) nějakou dobu trvá. Tentokrát zahrnuje zpracování operace služby Service Bus kromě latence požadavku a odpovědi. Pokud chcete zvýšit počet operací za čas, musí současně provést operace. Tento souhlas můžete dosáhnout různými způsoby:

* **Asynchronní operace**: klient plány operations provedením asynchronní operace. Další požadavek je spuštěn před dokončením předchozí požadavek. Následující fragment kódu je příklad operace asynchronní odesílání:
  
 ```csharp
  BrokeredMessage m1 = new BrokeredMessage(body);
  BrokeredMessage m2 = new BrokeredMessage(body);
  
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
  
  Následující kód je příkladem asynchronní operace příjmu:
  
  ```csharp
  Task receive1 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
  Task receive2 = queueClient.ReceiveAsync().ContinueWith(ProcessReceivedMessage);
  
  Task.WaitAll(receive1, receive2);
  Console.WriteLine("All messages received");
  
  async void ProcessReceivedMessage(Task<BrokeredMessage> t)
  {
    BrokeredMessage m = t.Result;
    Console.WriteLine("{0} received", m.Label);
    await m.CompleteAsync();
    Console.WriteLine("{0} complete", m.Label);
  }
  ```

* **Více objektů Factory**: Všichni klienti (odesílatelé kromě příjemců), které jsou vytvořené pomocí stejné objekty Factory sdílet jedno připojení TCP. Zpráva maximální propustnost je omezen počet operací, které můžete přejít přes toto připojení TCP. Propustnost, kterou lze získat pomocí jednoho factory se výrazně liší podle doby odezvy TCP a velikost zprávy. Chcete-li dosáhnout vyšší propustnosti, použijte více objektů Factory zasílání zpráv.

## <a name="receive-mode"></a>Zobrazí režim

Při vytvoření fronty nebo předplatného klienta, můžete určit režim příjmu: *funkce Náhled zámku* nebo *přijetí a odstranění*. Zobrazí výchozí režim je [PeekLock][PeekLock]. Při provozu v tomto režimu, klient odešle požadavek pro příjem zprávy ze služby Service Bus. Jakmile klient obdrží zprávu, odešle požadavek na dokončení zprávy.

Při nastavování režimu receive na [ReceiveAndDelete][ReceiveAndDelete], oba kroky se zkombinují v jedné žádosti. Tyto kroky snížit celkový počet operací a zkvalitnit celkovou propustnost zpráv. Tato výkonnější dodává jeho riziko ztráty zprávy.

Service Bus nepodporuje transakce pro operace přijímat a odstranění. Kromě toho jsou požadovány pro všechny scénáře, ve kterých chce odložení klienta funkce Náhled zámku sémantiku nebo [nedoručených zpráv](service-bus-dead-letter-queues.md) zprávu.

## <a name="client-side-batching"></a>Dávkování na straně klienta

Dávkování na straně klienta umožňuje klientovi fronta nebo téma do prodleva odesílání zprávy pro určitou dobu. Pokud klient pošle další zprávy během tohoto období, přenáší zprávy v jedné dávce. Dávkování na straně klienta rovněž způsobí, že fronta nebo předplatného klienta tak, aby dávky více **Complete** požadavky do jedné žádosti. Dávkování je dostupná jenom pro asynchronní **odeslat** a **Complete** operace. Synchronní operace se okamžitě odesílají do služby Service Bus. Dávkování dojít k funkce Náhled nebo přijímat operace, ani dávkování dojde k do klientů.

Ve výchozím nastavení používá klienta s intervalem batch 20 ms. Batch interval, můžete změnit nastavením [BatchFlushInterval] [ BatchFlushInterval] vlastnost před vytvořením objektu pro vytváření zpráv. Toto nastavení ovlivní všechny klienty, které jsou vytvořené pomocí tento objekt pro vytváření.

Chcete-li zakázat dávkování, nastavte [BatchFlushInterval] [ BatchFlushInterval] vlastnost **TimeSpan.Zero**. Příklad:

```csharp
MessagingFactorySettings mfs = new MessagingFactorySettings();
mfs.TokenProvider = tokenProvider;
mfs.NetMessagingTransportSettings.BatchFlushInterval = TimeSpan.FromSeconds(0.05);
MessagingFactory messagingFactory = MessagingFactory.Create(namespaceUri, mfs);
```

Dávkování nemá vliv na počet operací fakturovatelný zasílání zpráv a je dostupná jenom pro protokol klienta služby Service Bus. Protokol HTTP nepodporuje dávkování.

## <a name="batching-store-access"></a>Dávkování přístup k úložišti

Pokud chcete zvýšit propustnost fronty, tématu nebo předplatného, Service Bus dávek více zpráv při zapíše do jeho interní úložiště. Pokud je povoleno na se fronta nebo téma, zápis zpráv do úložiště bude zpracovat v dávce. Pokud je povoleno na fronty nebo předplatného, odstraňování zpráv z úložiště bude zpracovat v dávce. Pokud je povolený přístup dávkové úložiště pro entitu, Service Bus zpozdí operace zápisu úložiště o dané entity podle až 20 ms. 

> [!NOTE]
> Neexistuje žádné riziko ztráty zprávy s dávkování, i když dojde k chybě služby Service Bus na konci intervalu 20ms dávkování. 

Další úložiště operace, ke kterým došlo během tohoto intervalu jsou přidány do dávky. Zpracovat v dávce ovlivňuje pouze přístup k úložišti **odeslat** a **Complete** operace; přijímat operace neovlivní. Dávkové úložiště přístup je vlastnost u entity. Dávkování dochází mezi všechny entity, které umožňují přístup dávkové úložiště.

Při vytváření nové fronty, tématu nebo předplatného, je ve výchozím nastavení povolen přístup dávkové úložiště. Chcete-li zakázat přístup dávkové úložiště, nastavte [EnableBatchedOperations] [ EnableBatchedOperations] vlastnost **false** před vytvořením entity. Příklad:

```csharp
QueueDescription qd = new QueueDescription();
qd.EnableBatchedOperations = false;
Queue q = namespaceManager.CreateQueue(qd);
```

Dávkové úložiště přístup nemá vliv na počet operací fakturovatelný zasílání zpráv a je vlastností fronty, tématu nebo předplatného. Je nezávislé na režim příjmu a protokol, který se používá mezi klientem a službu Service Bus.

## <a name="prefetching"></a>Prefetching

[Prefetching](service-bus-prefetch.md) umožňuje klientovi fronty nebo předplatné načíst další zprávy ze služby při provádění operace příjmu. Klient uloží tyto zprávy v místní mezipaměti. Velikost mezipaměti je dáno [QueueClient.PrefetchCount] [ QueueClient.PrefetchCount] nebo [SubscriptionClient.PrefetchCount] [ SubscriptionClient.PrefetchCount] vlastnosti. Každého klienta, který umožňuje prefetching udržuje vlastní mezipaměť. Mezipaměť není sdílet mezi klienty. Pokud klient zahájí operace příjmu a jeho mezipaměť je prázdná, služba přenáší dávku zpráv. Velikost dávky se rovná velikosti mezipaměti nebo 256 KB, podle toho, která je menší. Pokud klient zahájí operace příjmu a mezipaměti obsahuje zprávu, zpráva je převzata z mezipaměti.

Když je prefetched zprávy, se prefetched zpráva jen uzamkne službu. S zámek nemůže přijímat prefetched zpráva jiný příjemce. Pokud příjemce nemůže dokončit zprávu, než vyprší platnost zámek, k dispozici pro ostatní příjemce zprávy. Prefetched kopie zprávy zůstává v mezipaměti. Příjemce, který využívá vypršenou platností v mezipaměti kopie dostanou výjimku při pokusu o dokončení této zprávě. Ve výchozím nastavení zámek zprávy vyprší po 60 sekund. Tuto hodnotu lze rozšířit na 5 minut. Pokud chcete zabránit spotřeby zprávy s vypršenou platností, by měla být velikost mezipaměti vždy menší než počet zpráv, které mohou být využívány službou klienta v určeném časovém limitu uzamčení.

Při použití výchozí zámku vypršení platnosti 60 sekund, správné hodnoty pro [SubscriptionClient.PrefetchCount] [ SubscriptionClient.PrefetchCount] je 20krát maximální počet zpracovaných položek všechny přijímačů objektu pro vytváření. Například objekt factory vytvoří tři příjemci a každý příjemce může zpracovat až 10 zpráv za sekundu. Předběžné načtení počet nesmí být delší než 20 × 3 × 10 = 600. Ve výchozím nastavení [QueueClient.PrefetchCount] [ QueueClient.PrefetchCount] je nastaven na hodnotu 0, což znamená, že žádné další zprávy jsou načtených ze služby.

Prefetching zprávy zvyšuje celkovou propustnost pro fronty nebo předplatného, protože snižuje celkový počet zpráv operace nebo zpátečních cest. Načítání první zprávu, ale bude trvat déle (z důvodu velikost vyšší zprávy). Přijímání zpráv prefetched bude rychlejší, protože tyto zprávy již byly staženy klientem.

Vlastnost time to live (TTL) zprávy, které se kontroluje server v době, kdy server odešle zprávu do klienta. Klient nekontroluje vlastnost TTL dané zprávy při doručení zprávy. Místo toho zpráva může být přijata i v případě TTL dané zprávy uplynutí a zprávy se uloží do mezipaměti klienta.

Prefetching nemá vliv na počet operací fakturovatelný zasílání zpráv a je dostupná jenom pro protokol klienta služby Service Bus. Protokol HTTP nepodporuje prefetching. Prefetching je k dispozici pro synchronní a asynchronní operace příjmu.

## <a name="express-queues-and-topics"></a>Express front a témat

Expresní entity povolte vysoké propustnosti a menší latence scénáře a jsou podporovány pouze na vrstvě Standard zasílání zpráv. Entity vytvořené v [Premium obory názvů](service-bus-premium-messaging.md) nepodporují možnost express. S expresní entity Pokud je odeslána zpráva se fronta nebo téma, zprávy se neuloží okamžitě v úložišti pro přenos zpráv. Místo toho do mezipaměti v paměti. Pokud zpráva zůstane ve frontě více než několik sekund, se automaticky zapisuje do ustájení úložiště, tedy chránit před ztrátou kvůli výpadku. Zápis zprávy do mezipaměti paměti zvyšuje propustnost a snižuje latence, protože není k dispozici přístup na pevné úložiště v době, kdy je zpráva odeslána. Zprávy, které se spotřebovávají během několika sekund nezapisují k úložišti zasílání zpráv. Následující příklad vytvoří express tématu.

```csharp
TopicDescription td = new TopicDescription(TopicName);
td.EnableExpress = true;
namespaceManager.CreateTopic(td);
```

Pokud expresní entity odeslání zprávu, která obsahuje důležité informace, které nesmí být ztraceny, odesílatel můžete vynutit Service Bus se okamžitě zachovat zprávy na pevné úložiště nastavením [ForcePersistence] [ ForcePersistence] vlastnost **true**.

> [!NOTE]
> Expresní entity nepodporují transakce.

## <a name="use-of-partitioned-queues-or-topics"></a>Použití oddílů fronty nebo témata

Interně Service Bus používá stejný uzel a zasílání zpráv uložení ke zpracování a ukládání všechny zprávy pro entity přenosu zpráv (fronty nebo tématu). A [oddílů fronta nebo téma](service-bus-partitioning.md), na druhé straně je distribuované ve více uzlech a úložiště pro zasílání zpráv. Oddílů fronty a témata nejen poskytne vyšší výkon než regulární fronty a témata, budou také vykazovat vyšší dostupnosti. Chcete-li vytvořit dělené entity, nastavte [enablepartitioning je] [ EnablePartitioning] vlastnost **true**, jak je znázorněno v následujícím příkladu. Další informace o dělené entity najdete v tématu [segmentované entity zasílání zpráv][Partitioned messaging entities].

> [!NOTE]
> Dělené entity již nejsou podporovány v [skladová položka Premium](service-bus-premium-messaging.md). 

```csharp
// Create partitioned queue.
QueueDescription qd = new QueueDescription(QueueName);
qd.EnablePartitioning = true;
namespaceManager.CreateQueue(qd);
```

## <a name="use-of-multiple-queues"></a>Použití více front

Pokud není možné použít oddílů fronta nebo téma nebo očekávané zátěže nelze zpracovat pomocí jednoho oddílů fronta nebo téma, je nutné použít více entit pro zasílání zpráv. Pokud používáte více entit, vytvořte vyhrazený klienta pro každou entitu, místo použití stejného klienta pro všechny entity.

## <a name="development-and-testing-features"></a>Vývoj a testování funkcí

Service Bus má jednu funkci, určených pro vývoj, které **by měl být nikdy použit ve konfigurace produkční**: [TopicDescription.EnableFilteringMessagesBeforePublishing][].

Pokud nová pravidla nebo filtry jsou přidány do tématu, můžete použít [TopicDescription.EnableFilteringMessagesBeforePublishing][] k ověření, že nový výraz filtru funguje podle očekávání.

## <a name="scenarios"></a>Scénáře

Následující části popisují typické scénáře zasílání zpráv a popisují nastavení služby Service Bus. Propustnosti jsou klasifikovány jako malé (méně než 1 zpráv za sekundu), střední (1 zpráv za sekundu nebo větší, ale méně než 100 zpráv za sekundu) a vysokou (100 zprávy/druhý nebo vyšší). Počet klientů, které jsou klasifikovány jako malá (5 nebo méně), střední (víc než 5, ale menší než nebo rovna 20) a velké (více než 20).

### <a name="high-throughput-queue"></a>Vysokou propustností fronty

Cíl: Maximalizujte propustnost jedné frontě. Počet odesílateli a příjemci je malá.

* Chcete-li zvýšit celkový rychlost odesílání do fronty, použijte k vytvoření odesílatelé více. Pro každý odesílatele použijte asynchronní operace nebo více vláken.
* Pokud chcete zvýšit celkový rychlost přijímání z fronty, použijte k vytvoření přijímače více.
* Použití asynchronních operací využívat výhod dávkování na straně klienta.
* Nastavte dávkování interval na 50 ms a snížit počet přenosů protokolu klienta služby Service Bus. Pokud jsou použity více odesílatelé, zvýšit dávkování interval, který má 100 ms.
* Ponechte povolen přístup dávkové úložiště. Tento přístup zvyšuje celkovou rychlost, kdy mohou být zprávy zapisovány do fronty.
* Nastavte počet předběžné načtení 20krát maximální zpracování sazby všichni příjemci objekt pro vytváření. Tento počet snižuje počet přenosů protokolu klienta služby Service Bus.
* Oddílů frontu použijte pro lepší výkon a dostupnost.

### <a name="multiple-high-throughput-queues"></a>Více vysokou propustností fronty

Cíl: Maximalizujte celkovou propustnost více front. Propustnost jednotlivé fronty je střední nebo vysoké.

Pokud chcete získat maximální propustnost napříč více front, použijte nastavení popsané Maximalizovat propustnost jedné frontě. Kromě toho můžete použijte různé objekty Factory vytvořit klienti, kteří odesílat nebo přijímat z různých front.

### <a name="low-latency-queue"></a>Fronty s nízkou latencí

Cíl: Minimalizaci latence začátku do konce se fronta nebo téma. Počet odesílateli a příjemci je malá. Propustnost fronty je malá nebo střední.

* Zakážete dávkování na straně klienta. Klient, ihned odešle zprávu.
* Zakážete přístup dávkové úložiště. Služba okamžitě zapíše zprávu do úložiště.
* Při použití jednoho klienta, nastavte počet předběžné načtení 20krát zpracování míru příjemce. Pokud více zpráv přijaty ve frontě ve stejnou dobu, odesílá protokol klienta služby Service Bus je všechny najednou. Když klient obdrží na další zprávu, zpráva již v místní mezipaměti. Mezipaměti musí být malé.
* Pokud používáte více klientů, nastavte počet předběžné načtení na hodnotu 0. Nastavením počet druhý klienta může přijímat o druhou zprávu při první klient stále ještě zpracování první zprávy.
* Oddílů frontu použijte pro lepší výkon a dostupnost.

### <a name="queue-with-a-large-number-of-senders"></a>Fronty s velkým počtem uživatelů

Cíl: Maximalizujte propustnost se fronta nebo téma s velkým počtem uživatelů. Každý odesílatel odešle zprávy střední míru. Počet příjemců je malá.

Umožňuje až 1 000 souběžných připojení k entitě přenosu zpráv Service Bus (nebo 5000 pomocí protokolu AMQP). Tento limit je vyžadována na úrovni oboru názvů a fronty, témata nebo odběry jsou omezená podle maximální počet souběžných připojení na obor názvů. Pro fronty je toto číslo sdílet mezi odesílateli a příjemci. Pokud jsou požadovány pro odesílatelé všechna připojení 1000, nahraďte fronty téma a v rámci jednoho předplatného. Téma přijme až 1 000 souběžných připojení z odesílatelé, zatímco předplatné přijímá další 1 000 souběžných připojení z příjemců. Pokud více než 1 000 souběžných odesílatelé, by měl odesílatelé odesílání zpráv na protokol Service Bus přes HTTP.

Chcete-li maximalizovat propustnost, proveďte následující kroky:

* Pokud každý odesílatele se nachází v jiném procesu, použijte pouze jeden objekt factory pro procesy.
* Použití asynchronních operací využívat výhod dávkování na straně klienta.
* Použijte výchozí interval 20 ms dávkování a snížit počet přenosů protokolu klienta služby Service Bus.
* Ponechte povolen přístup dávkové úložiště. Tento přístup zvyšuje celkovou rychlost, kdy mohou být zprávy zapisovány do fronta nebo téma.
* Nastavte počet předběžné načtení 20krát maximální zpracování sazby všichni příjemci objekt pro vytváření. Tento počet snižuje počet přenosů protokolu klienta služby Service Bus.
* Oddílů frontu použijte pro lepší výkon a dostupnost.

### <a name="queue-with-a-large-number-of-receivers"></a>Fronty s velkým počtem příjemci

Cíl: Maximalizujte rychlost přijímání fronty nebo předplatné s velký počet příjemců. Každý příjemce přijímá zprávy střední rychlostí. Počet uživatelů je malá.

Service Bus umožňuje až 1 000 souběžných připojení k entitě. Pokud fronty vyžaduje více než 1 000 příjemci, nahraďte fronty téma a více předplatných. Každý odběr může podporovat až 1 000 souběžných připojení. Alternativně můžete příjemci přistupovat ke frontě prostřednictvím protokolu HTTP.

Chcete-li maximalizovat propustnost, postupujte takto:

* Pokud každý příjemce se nachází v jiném procesu, použijte pouze jeden objekt factory pro procesy.
* Příjemci můžete použít synchronní nebo asynchronní operace. Zadaný počet střední receive jednotlivé příjemce, dávkování na straně klienta dokončení požadavku nemá vliv na propustnost příjemce.
* Ponechte povolen přístup dávkové úložiště. Tento přístup snižuje celkové zatížení entity. Také snižuje celkovou rychlost, kdy mohou být zprávy zapisovány do fronta nebo téma.
* Předběžné počet nastavená na hodnotu malé (například PrefetchCount = 10). Tento počet zabraňuje příjemci v nečinnosti, zatímco ostatní příjemce máte velké množství zpráv do mezipaměti.
* Oddílů frontu použijte pro lepší výkon a dostupnost.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Téma se malý počet odběrů

Cíl: Maximalizujte propustnost téma s malým počtem odběry. Zprávu přijme mnoho odběrů, což znamená, že míra kombinované receive přes všechny odběry je větší než frekvence odesílání. Počet uživatelů je malá. Počet příjemců na předplatné je malá.

Chcete-li maximalizovat propustnost, postupujte takto:

* Chcete-li zvýšit celkový rychlost odesílání do tématu, použijte k vytvoření odesílatelé více. Pro každý odesílatele použijte asynchronní operace nebo více vláken.
* Pokud chcete zvýšit celková míra přijetí z odběru, použijte k vytvoření přijímače více. Pro každý příjemce použijte asynchronní operace nebo více vláken.
* Použití asynchronních operací využívat výhod dávkování na straně klienta.
* Použijte výchozí interval 20 ms dávkování a snížit počet přenosů protokolu klienta služby Service Bus.
* Ponechte povolen přístup dávkové úložiště. Tento přístup zvyšuje celkovou rychlost, kdy mohou být zprávy zapisovány do tématu.
* Nastavte počet předběžné načtení 20krát maximální zpracování sazby všichni příjemci objekt pro vytváření. Tento počet snižuje počet přenosů protokolu klienta služby Service Bus.
* Použijte oddílů tématu pro lepší výkon a dostupnost.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Téma se velký počet odběrů

Cíl: Maximalizujte propustnost téma se velký počet předplatných. Zpráva je přijatých mnoho odběrů, což znamená, že míra kombinované receive přes všechny odběry je mnohem větší než frekvence odesílání. Počet uživatelů je malá. Počet příjemců na předplatné je malá.

Témata s velkým počtem odběry obvykle odhalí nízkou celkovou propustnost všechny zprávy jsou směrovány do všech odběrů. Tato nízkou propustnost je způsobená skutečnost, že každou zprávu přijme mnohokrát a všechny zprávy, které jsou obsaženy v tématu a všechny její odběry jsou uložené ve stejném úložišti. Předpokládá se, že počet odesílatelé a počet příjemců na předplatné je malé. Service Bus podporuje až 2 000 odběry na téma.

Chcete-li maximalizovat propustnost, zkuste následující kroky:

* Použití asynchronních operací využívat výhod dávkování na straně klienta.
* Použijte výchozí interval 20 ms dávkování a snížit počet přenosů protokolu klienta služby Service Bus.
* Ponechte povolen přístup dávkové úložiště. Tento přístup zvyšuje celkovou rychlost, kdy mohou být zprávy zapisovány do tématu.
* Nastavte počet předběžné načtení 20krát očekávané receive míru v sekundách. Tento počet snižuje počet přenosů protokolu klienta služby Service Bus.
* Použijte oddílů tématu pro lepší výkon a dostupnost.

## <a name="next-steps"></a>Další postup

Další informace o optimalizaci výkonu služby Service Bus, najdete v části [segmentované entity zasílání zpráv][Partitioned messaging entities].

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
