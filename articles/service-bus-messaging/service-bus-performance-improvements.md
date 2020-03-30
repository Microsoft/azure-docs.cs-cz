---
title: Doporučené postupy pro zlepšení výkonu pomocí Azure Service Bus
description: Popisuje, jak pomocí service bus optimalizovat výkon při výměně zprostředkované zprávy.
services: service-bus-messaging
documentationcenter: na
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus-messaging
ms.topic: article
ms.date: 03/12/2020
ms.author: aschhab
ms.openlocfilehash: b864f433c67d47b4b92a1d4b98693ebd42806dd3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259458"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Doporučené postupy pro zlepšení výkonu pomocí zasílání zpráv Service Bus

Tento článek popisuje, jak používat Azure Service Bus k optimalizaci výkonu při výměně zprostředkovaných zpráv. První část tohoto článku popisuje různé mechanismy, které jsou nabízeny ke zvýšení výkonu. Druhá část obsahuje pokyny, jak používat service bus způsobem, který může nabídnout nejlepší výkon v daném scénáři.

V celém tomto článku termín "klient" odkazuje na všechny entity, které přistupuje service bus. Klient může převzít roli odesílatele nebo příjemce. Termín "odesílatel" se používá pro frontu service bus nebo tematického klienta, který odesílá zprávy do fronty service bus nebo tematické předplatné. Termín "příjemce" odkazuje na frontu service bus nebo klienta předplatného, který přijímá zprávy z fronty service bus nebo předplatného.

Tyto části představují několik konceptů, které service bus používá ke zvýšení výkonu.

## <a name="protocols"></a>Protokoly

Service Bus umožňuje klientům odesílat a přijímat zprávy prostřednictvím jednoho ze tří protokolů:

1. Rozšířený protokol řízení front zpráv (AMQP)
2. Protokol zasílání zpráv sběrnice (SBMP)
3. Hypertextový přenosový protokol (HTTP)

AMQP je nejúčinnější, protože udržuje připojení k service bus. Také implementuje dávkování a předběžné načítání. Není-li výslovně uvedeno, veškerý obsah v tomto článku předpokládá použití AMQP nebo SBMP.

> [!IMPORTANT]
> SBMP je k dispozici pouze pro rozhraní .NET Framework. AMQP je výchozí pro standard .NET.

## <a name="choosing-the-appropriate-service-bus-net-sdk"></a>Výběr příslušné sady Service Bus .NET SDK

Existují dvě podporované sady Azure Service Bus .NET SDK. Jejich api jsou velmi podobné a může být matoucí, který z nich si vybrat. V následující tabulce nápovědu k rozhodnutí naleznete následující tabulku. Doporučujeme Microsoft.Azure.ServiceBus SDK jako je modernější, výkonnější a je kompatibilní s platformami. Kromě toho podporuje AMQP přes WebSockets a je součástí kolekce Azure .NET SDK open source projektů.

| Balíček NuGet | Primární obory názvů | Minimální platforma (platformy) | Protokoly |
|---------------|----------------------|---------------------|-------------|
| <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus" target="_blank">Microsoft.Azure.ServiceBus<span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.Azure.ServiceBus`<br>`Microsoft.Azure.ServiceBus.Management` | .NET Core 2.0<br>.NET Framework 4.6.1<br>Mono 5,4<br>Xamarin.iOS 10,14<br>Xamarin.Mac 3,8<br>Xamarin.Android 8.0<br>Univerzální platforma Windows 10.0.16299 | AMQP<br>HTTP |
| <a href="https://www.nuget.org/packages/WindowsAzure.ServiceBus" target="_blank">WindowsAzure.ServiceBus<span class="docon docon-navigate-external x-hidden-focus"></span></a> | `Microsoft.ServiceBus`<br>`Microsoft.ServiceBus.Messaging` | .NET Framework 4.6.1 | AMQP<br>SBMP<br>HTTP |

Další informace o minimální podpoře platformy .NET Standard naleznete v tématu [podpora implementace rozhraní .NET](https://docs.microsoft.com/dotnet/standard/net-standard#net-implementation-support).

## <a name="reusing-factories-and-clients"></a>Opětovné použití továren a klientů

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Objekty klienta service bus, [`IQueueClient`][QueueClient] [`IMessageSender`][MessageSender]jako jsou implementace nebo , by měly být registrovány pro vkládání závislostí jako singletons (nebo instance jednou a sdílené). Doporučujeme nezavírat továrny zasílání zpráv nebo klienty fronty, tématu a předplatného po odeslání zprávy a po odeslání další zprávy je znovu vytvořit. Zavření zprávy továrny odstraní připojení ke službě Service Bus a nové připojení je navázána při opětovném vytvoření výroby. Navázání připojení je nákladné operace, které se můžete vyhnout opětovným použitím stejné objekty továrny a klienta pro více operací. Tyto klientské objekty můžete bezpečně použít pro souběžné asynchronní operace a z více vláken.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Objekty klienta Service `QueueClient` `MessageSender`Bus, například nebo , jsou vytvořeny prostřednictvím [Objektu MessagingFactory,][MessagingFactory] který také poskytuje interní správu připojení. Doporučujeme nezavírat továrny zasílání zpráv nebo klienty fronty, tématu a předplatného po odeslání zprávy a po odeslání další zprávy je znovu vytvořit. Zavření zprávy továrny odstraní připojení ke službě Service Bus a nové připojení je navázána při opětovném vytvoření výroby. Navázání připojení je nákladné operace, které se můžete vyhnout opětovným použitím stejné objekty továrny a klienta pro více operací. Tyto klientské objekty můžete bezpečně použít pro souběžné asynchronní operace a z více vláken.

---

## <a name="concurrent-operations"></a>Souběžné operace

Provedení operace (odeslání, příjem, odstranění atd.) nějakou dobu trvá. Tento čas zahrnuje zpracování operace službou Service Bus kromě latence požadavku a odpovědi. Chcete-li zvýšit počet operací za čas, operace musí provádět souběžně.

Klient naplánuje souběžné operace provedením asynchronních operací. Další požadavek je spuštěn před dokončením předchozího požadavku. Následující fragment kódu je příkladem operace asynchronního odeslání:

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

```csharp
var messageOne = new Message(body);
var messageTwo = new Message(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

```csharp
var messageOne = new BrokeredMessage(body);
var messageTwo = new BrokeredMessage(body);

var sendFirstMessageTask =
    queueClient.SendAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    queueClient.SendAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");
```

---

Následující kód je příkladem operace asynchronního příjmu.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Příklady úplného <a href="https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues" target="_blank">zdrojového kódu <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>najdete v úložišti GitHub :

```csharp
var receiver = new MessageReceiver(connectionString, queueName, ReceiveMode.PeekLock);

static Task LogErrorAsync(Exception exception)
{
    Console.WriteLine(exception);
    return Task.CompletedTask;
};

receiver.RegisterMessageHandler(
    async (message, cancellationToken) =>
    {
        Console.WriteLine("Handle message");
        await receiver.CompleteAsync(message.SystemProperties.LockToken);
    },
    new MessageHandlerOptions(e => LogErrorAsync(e.Exception))
    {
        AutoComplete = false,
        MaxConcurrentCalls = 1
    });
```

Objekt `MessageReceiver` je vytvořena instance s připojovací řetězec, název fronty a peek-look příjem režimu. Dále instance `receiver` slouží k registraci obslužné rutiny zprávy.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Příklady úplného <a href="https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/SendersReceiversWithQueues" target="_blank">zdrojového kódu <span class="docon docon-navigate-external x-hidden-focus"> </span> </a>najdete v úložišti GitHub :

```csharp
var factory = MessagingFactory.CreateFromConnectionString(connectionString);
var receiver = await factory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);

// Register the handler to receive messages asynchronously
receiver.OnMessageAsync(
    async message =>
    {
        Console.WriteLine("Handle message");
        await message.CompleteAsync();
    },
    new OnMessageOptions
    {
        AutoComplete = false,
        MaxConcurrentCalls = 1
    });
```

Vytvoří `MessagingFactory` `factory` objekt z připojovacího řetězce. S `factory` instance `MessageReceiver` je vytvořena instance. Dále instance `receiver` slouží k registraci obslužné rutiny na zprávu.

---

## <a name="receive-mode"></a>Režim příjmu

Při vytváření fronty nebo klienta předplatného můžete určit režim příjmu: *Peek-lock* nebo *Receive a Delete*. Výchozí režim příjmu `PeekLock`je . Při práci ve výchozím režimu klient odešle požadavek na přijetí zprávy ze služby Service Bus. Poté, co klient obdrží zprávu, odešle požadavek na dokončení zprávy.

Při nastavování `ReceiveAndDelete`režimu příjmu na , jsou oba kroky sloučeny do jednoho požadavku. Tyto kroky snížit celkový počet operací a může zlepšit celkovou propustnost zprávy. Tento nárůst výkonu přichází s rizikem ztráty zpráv.

Service Bus nepodporuje transakce pro operace příjmu a odstranění. Kromě toho peek-lock sémantiku jsou vyžadovány pro všechny scénáře, ve kterém klient chce odložit nebo [nedoručené](service-bus-dead-letter-queues.md) zprávy.

## <a name="client-side-batching"></a>Dávkování na straně klienta

Dávkování na straně klienta umožňuje frontě nebo tématu klienta zpoždění odeslání zprávy po určitou dobu. Pokud během tohoto časového období klient odešle další zprávy, přenese zprávy v jedné dávce. Dávkování na straně klienta také způsobí, že fronty nebo klienta předplatného dávky více **požadavků Complete** do jednoho požadavku. Dávkování je k dispozici pouze pro asynchronní **odesílání** a **dokončení** operace. Synchronní operace jsou okamžitě odeslány do služby Service Bus. Dávkování nedochází pro náhled nebo přijímat operace, ani dávkování dojít napříč klienty.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Dávkování funkce pro .NET Standard SDK, ještě nezveřejňuje vlastnost manipulovat.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Ve výchozím nastavení klient používá dávkový interval 20 ms. Dávkový interval můžete změnit nastavením vlastnosti [BatchFlushInterval][BatchFlushInterval] před vytvořením továrny pro zasílání zpráv. Toto nastavení ovlivní všechny klienty, kteří jsou vytvořeni touto továrnou.

Chcete-li zakázat dávkování, nastavte vlastnost [BatchFlushInterval][BatchFlushInterval] na **TimeSpan.Zero**. Například:

```csharp
var settings = new MessagingFactorySettings
{
    NetMessagingTransportSettings =
    {
        BatchFlushInterval = TimeSpan.Zero
    }
};
var factory = MessagingFactory.Create(namespaceUri, settings);
```

Dávkování nemá vliv na počet fakturovatelných operací zasílání zpráv a je k dispozici pouze pro klientský protokol Service Bus pomocí knihovny [Microsoft.ServiceBus.Messaging.](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) Protokol HTTP nepodporuje dávkování.

> [!NOTE]
> Nastavení `BatchFlushInterval` zajišťuje, že dávkování je implicitní z pohledu aplikace. tj.; aplikace uskutečňuje `SendAsync` a `CompleteAsync` volá a neprovádí konkrétní dávková volání.
>
> Explicitní dávkování na straně klienta lze implementovat pomocí volání metody níže:
> ```csharp
> Task SendBatchAsync(IEnumerable<BrokeredMessage> messages);
> ```
> Zde musí být kombinovaná velikost zpráv menší než maximální velikost podporovaná cenovou úrovní.

---

## <a name="batching-store-access"></a>Dávkování přístupu k úložišti

Chcete-li zvýšit propustnost fronty, tématu nebo předplatného, Service Bus dávky více zpráv při zápisu do svého interního úložiště. Pokud je ve frontě nebo tématu povoleno, bude psaní zpráv do úložiště dávkově vypsáno. Pokud je povoleno ve frontě nebo předplatném, odstranění zpráv z úložiště bude dávkově. Pokud je pro entitu povolen dávkový přístup k úložišti, service bus zpozdí operaci zápisu do úložiště týkající se této entity až o 20 ms.

> [!NOTE]
> Neexistuje žádné riziko ztráty zpráv s dávkování, i v případě, že dojde k selhání service bus na konci intervalu dávkování 20 ms.

Další operace úložiště, ke kterým dochází během tohoto intervalu jsou přidány do dávky. Dávkový přístup k úložišti ovlivňuje pouze operace **odesílání** a **dokončení.** operace příjmu nejsou ovlivněny. Dávkový přístup k úložišti je vlastnost entity. K dávkování dochází ve všech entitách, které umožňují dávkový přístup k úložišti.

Při vytváření nové fronty, tématu nebo předplatného je ve výchozím nastavení povolen dávkový přístup k obchodu.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Chcete-li zakázat dávkový přístup k obchodu, budete potřebovat instanci `ManagementClient`aplikace . Vytvořte frontu z popisu `EnableBatchedOperations` fronty, `false`která nastaví vlastnost na .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = await managementClient.CreateQueueAsync(queueDescription);
```

Další informace najdete v následujících článcích:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.Management.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Chcete-li zakázat dávkový přístup k obchodu, budete potřebovat instanci `NamespaceManager`aplikace . Vytvořte frontu z popisu `EnableBatchedOperations` fronty, `false`která nastaví vlastnost na .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = namespaceManager.CreateQueue(queueDescription);
```

Další informace najdete v následujících článcích:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicdescription.enablebatchedoperations?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

Dávkový přístup k úložišti nemá vliv na počet fakturovatelných operací zasílání zpráv a je vlastností fronty, tématu nebo předplatného. Je nezávislý na režimu příjmu a protokolu, který se používá mezi klientem a službou Service Bus.

## <a name="prefetching"></a>Prefetching

[Předběžné načítání](service-bus-prefetch.md) umožňuje klientovi fronty nebo předplatného načíst další zprávy ze služby při provádění operace příjmu. Klient ukládá tyto zprávy v místní mezipaměti. Velikost mezipaměti je určena `QueueClient.PrefetchCount` vlastnostmi nebo. `SubscriptionClient.PrefetchCount` Každý klient, který umožňuje předběžné načítání, udržuje vlastní mezipaměť. Mezipaměť není sdílena mezi klienty. Pokud klient zahájí operaci příjmu a jeho mezipaměť je prázdná, služba přenáší dávku zpráv. Velikost dávky se rovná velikosti mezipaměti nebo 256 KB, podle toho, která hodnota je menší. Pokud klient zahájí operaci příjmu a mezipaměť obsahuje zprávu, zpráva je převzata z mezipaměti.

Když je zpráva přednačtena, služba zamkne přednačtenou zprávu. Pomocí zámku nemůže být zpráva s přednačtenou zprávou přijata jiným příjemcem. Pokud příjemce nemůže dokončit zprávu před vypršením platnosti zámku, zpráva bude k dispozici ostatním příjemcům. Přednačtená kopie zprávy zůstane v mezipaměti. Příjemce, který spotřebovává vypršela kopie uložené v mezipaměti obdrží výjimku při pokusu o dokončení této zprávy. Ve výchozím nastavení vyprší platnost zámku zprávy po 60 sekundách. Tuto hodnotu lze prodloužit na 5 minut. Chcete-li zabránit spotřebě zpráv, jejichž platnost vypršela, velikost mezipaměti by měla být vždy menší než počet zpráv, které mohou být spotřebovány klientem v intervalu vypršení časového limitu uzamčení.

Při použití výchozího vypršení platnosti zámku `PrefetchCount` 60 sekund je dobrá hodnota pro 20násobek maximální rychlosti zpracování všech přijímačů továrny. Například továrna vytvoří tři přijímače a každý příjemce může zpracovat až 10 zpráv za sekundu. Počet předběžného načtení by neměl překročit 20 X 3 X 10 = 600. Ve výchozím `PrefetchCount` nastavení je nastavena na hodnotu 0, což znamená, že ze služby nejsou načteny žádné další zprávy.

Předběžné načítání zpráv zvyšuje celkovou propustnost pro frontu nebo předplatné, protože snižuje celkový počet operací zpráv nebo zpátečních cest. Načítání první zprávy však bude trvat déle (z důvodu zvětšení velikosti zprávy). Příjem předem načtených zpráv bude rychlejší, protože tyto zprávy již byly staženy klientem.

Vlastnost time-to-live (TTL) zprávy je kontrolována serverem v době, kdy server odešle zprávu klientovi. Klient nekontroluje vlastnost TTL zprávy při přijetí zprávy. Místo toho zpráva může být přijata i v případě, že zpráva TTL prošel, zatímco zpráva byla uložena do mezipaměti klienta.

Předběžné načítání nemá vliv na počet fakturovatelných operací zasílání zpráv a je k dispozici pouze pro klientský protokol service bus. Protokol HTTP nepodporuje předběžné načítání. Předběžné načítání je k dispozici pro synchronní i asynchronní operace příjmu.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft.Azure.ServiceBus SDK](#tab/net-standard-sdk)

Další informace naleznete v `PrefetchCount` následujících vlastnostech:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.Azure.ServiceBus.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure.ServiceBus SDK](#tab/net-framework-sdk)

Další informace naleznete v `PrefetchCount` následujících vlastnostech:

* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.queueclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/en-us/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.prefetchcount?view=azure-dotnet" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="prefetching-and-receivebatch"></a>Předběžné načítání a příjembatch

> [!NOTE]
> Tato část se vztahuje pouze na sadu WindowsAzure.ServiceBus SDK, protože sada Microsoft.Azure.ServiceBus SDK nezveřejňuje dávkové funkce.

Zatímco koncepty předběžného načítání více zpráv společně mají podobnou sémantiku jako zpracování zpráv v dávce (`ReceiveBatch`), existují některé drobné rozdíly, které je třeba mít na paměti při jejich společném využití.

Předběžné načtení je konfigurace (nebo režim) `SubscriptionClient`na `ReceiveBatch` straně klienta (`QueueClient` a ) a je operace (která má sémantiku požadavku a odpovědi).

Při společném používání zvažte následující případy

* Předběžné načtení by mělo být větší nebo rovno počtu `ReceiveBatch`zpráv, které očekáváte od aplikace .
* Předběžné načtení může být až n/3 násobek počtu zpráv zpracovaných za sekundu, kde n je výchozí doba trvání zámku.

Tam jsou některé problémy s tím, že chamtivý přístup (tj. udržování prefetch počítat velmi vysoké), protože to znamená, že zpráva je uzamčena na konkrétní příjemce. Doporučení je vyzkoušet prefetch hodnoty mezi prahovými hodnotami uvedenými výše a empiricky identifikovat, co se hodí.

## <a name="multiple-queues"></a>Více front

Pokud očekávané zatížení nelze zpracovat jednou frontou nebo tématem, je nutné použít více entit zasílání zpráv. Při použití více entit vytvořte vyhrazeného klienta pro každou entitu namísto použití stejného klienta pro všechny entity.

## <a name="development-and-testing-features"></a>Vývoj a testování funkcí

> [!NOTE]
> Tato část se vztahuje pouze na sadu WindowsAzure.ServiceBus SDK, protože sada Microsoft.Azure.ServiceBus SDK tuto funkci nezveřejňuje.

Service Bus má jednu funkci, která se používá speciálně [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering]pro vývoj, která by nikdy **neměla být použita v produkčních konfiguracích**: .

Pokud jsou do tématu přidána nová [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] pravidla nebo filtry, můžete použít k ověření, zda nový výraz filtru funguje podle očekávání.

## <a name="scenarios"></a>Scénáře

Následující části popisují typické scénáře zasílání zpráv a popisují upřednostňované nastavení sběrnice Service Bus. Propustnost jsou klasifikovány jako malé (méně než 1 zpráva za sekundu), střední (1 zpráva za sekundu nebo větší, ale méně než 100 zpráv za sekundu) a vysoká (100 zpráv za sekundu nebo vyšší). Počet klientů je klasifikován jako malý (5 nebo méně), střední (více než 5, ale menší nebo roven 20) a velký (více než 20).

### <a name="high-throughput-queue"></a>Fronta s vysokou propustností

Cíl: Maximalizujte propustnost jedné fronty. Počet odesílatelů a příjemců je malý.

* Chcete-li zvýšit celkovou rychlost odesílání do fronty, použijte k vytvoření odesílatelů více továren zpráv. Pro každého odesílatele použijte asynchronní operace nebo více vláken.
* Chcete-li zvýšit celkovou míru příjmu z fronty, použijte k vytvoření příjemců více továren zpráv.
* Pomocí asynchronních operací můžete využít výhod dávkování na straně klienta.
* Nastavte interval dávkování na 50 ms, abyste snížili počet přenosů protokolu service bus. Pokud je použito více odesílatelů, zvyšte interval dávkování na 100 ms.
* Ponechte dávkový přístup do obchodu povolený. Tento přístup zvyšuje celkovou rychlost, jakou mohou být zprávy zapsány do fronty.
* Nastavte počet předběžného načtení na 20násobek maximální rychlosti zpracování všech příjemců z výroby. Tento počet snižuje počet přenosů protokolu klienta Service Bus.

### <a name="multiple-high-throughput-queues"></a>Více front s vysokou propustností

Cíl: Maximalizujte celkovou propustnost více front. Propustnost jednotlivých front je střední nebo vysoká.

Chcete-li získat maximální propustnost ve více frontách, použijte uvedené nastavení k maximalizaci propustnost jedné fronty. Kromě toho použijte různé továrny k vytvoření klientů, kteří odesílají nebo přijímají z různých front.

### <a name="low-latency-queue"></a>Fronta s nízkou latencí

Cíl: Minimalizujte latenci fronty nebo tématu od konce. Počet odesílatelů a příjemců je malý. Propustnost fronty je malá nebo střední.

* Zakázat dávkování na straně klienta. Klient okamžitě odešle zprávu.
* Zakažte dávkový přístup k obchodu. Služba okamžitě zapíše zprávu do úložiště.
* Pokud používáte jednoho klienta, nastavte počet předběžného načtení na 20násobek rychlosti zpracování příjemce. Pokud více zpráv dorazí do fronty ve stejnou dobu, protokol klienta Service Bus přenáší všechny současně. Když klient obdrží další zprávu, tato zpráva je již v místní mezipaměti. Mezipaměť by měla být malá.
* Pokud používáte více klientů, nastavte počet předběžného načtení na 0. Nastavením počtu, druhý klient může přijímat druhou zprávu, zatímco první klient je stále zpracování první zprávy.

### <a name="queue-with-a-large-number-of-senders"></a>Fronta s velkým počtem odesílatelů

Cíl: Maximalizujte propustnost fronty nebo tématu s velkým počtem odesílatelů. Každý odesílatel odesílá zprávy s mírnou rychlostí. Počet přijímačů je malý.

Service Bus umožňuje až 1000 souběžných připojení k entitě zasílání zpráv (nebo 5000 pomocí AMQP). Toto omezení je vynuceno na úrovni oboru názvů a fronty/témata/odběry jsou omezeny limitem souběžných připojení na obor názvů. U front je toto číslo sdíleno mezi odesílateli a příjemci. Pokud je pro odesílatele vyžadováno všech 1000 připojení, nahraďte frontu tématem a jedním předplatným. Téma přijímá až 1000 souběžných připojení od odesílatelů, zatímco odběr přijímá dalších 1000 souběžných připojení od příjemců. Pokud je požadováno více než 1000 souběžných odesílatelů, měli by odesílatelé odesílat zprávy do protokolu Service Bus prostřednictvím protokolu HTTP.

Chcete-li maximalizovat propustnost, proveďte následující kroky:

* Pokud se každý odesílatel nachází v jiném procesu, použijte pouze jednu továrnu na proces.
* Pomocí asynchronních operací můžete využít výhod dávkování na straně klienta.
* Ke snížení počtu přenosů klientského protokolu Service Bus použijte výchozí interval dávkování 20 ms.
* Ponechte dávkový přístup do obchodu povolený. Tento přístup zvyšuje celkovou rychlost, jakou mohou být zprávy zapsány do fronty nebo tématu.
* Nastavte počet předběžného načtení na 20násobek maximální rychlosti zpracování všech příjemců z výroby. Tento počet snižuje počet přenosů protokolu klienta Service Bus.

### <a name="queue-with-a-large-number-of-receivers"></a>Fronta s velkým počtem přijímačů

Cíl: Maximalizujte míru příjmu fronty nebo předplatného s velkým počtem přijímačů. Každý příjemce přijímá zprávy mírnou rychlostí. Počet odesílatelů je malý.

Service Bus umožňuje až 1000 souběžných připojení k entitě. Pokud fronta vyžaduje více než 1000 přijímačů, nahraďte frontu tématem a více odběry. Každé předplatné může podporovat až 1000 souběžných připojení. Případně mohou příjemci přistupovat k frontě prostřednictvím protokolu HTTP.

Chcete-li maximalizovat propustnost, postupujte takto:

* Pokud se každý příjemce nachází v jiném procesu, použijte pouze jednu továrnu na proces.
* Příjemci mohou používat synchronní nebo asynchronní operace. Vzhledem k mírné rychlosti příjmu jednotlivých příjemců, dávkování požadavku Complete na straně klienta nemá vliv na propustnost příjemce.
* Ponechte dávkový přístup do obchodu povolený. Tento přístup snižuje celkové zatížení entity. Také snižuje celkovou rychlost, jakou mohou být zprávy zapsány do fronty nebo tématu.
* Nastavte počet předběžného načtení na malou hodnotu (například PrefetchCount = 10). Tento počet zabraňuje přijímače z nečinnosti, zatímco ostatní příjemci mají velký počet zpráv v mezipaměti.

### <a name="topic-with-a-small-number-of-subscriptions"></a>Téma s malým počtem předplatných

Cíl: Maximalizujte propustnost tématu s malým počtem předplatných. Zpráva je přijata mnoho předplatných, což znamená, že kombinovaná míra příjmu přes všechna předplatná je větší než rychlost odesílání. Počet odesílatelů je malý. Počet příjemců na předplatné je malý.

Chcete-li maximalizovat propustnost, postupujte takto:

* Chcete-li zvýšit celkovou rychlost odesílání do tématu, vytvořte odesílatele pomocí více továren zpráv. Pro každého odesílatele použijte asynchronní operace nebo více vláken.
* Chcete-li zvýšit celkovou míru příjmu z předplatného, použijte k vytvoření příjemců více továren zpráv. Pro každý přijímač použijte asynchronní operace nebo více vláken.
* Pomocí asynchronních operací můžete využít výhod dávkování na straně klienta.
* Ke snížení počtu přenosů klientského protokolu Service Bus použijte výchozí interval dávkování 20 ms.
* Ponechte dávkový přístup do obchodu povolený. Tento přístup zvyšuje celkovou rychlost, jakou mohou být zprávy zapsány do tématu.
* Nastavte počet předběžného načtení na 20násobek maximální rychlosti zpracování všech příjemců z výroby. Tento počet snižuje počet přenosů protokolu klienta Service Bus.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Téma s velkým počtem předplatných

Cíl: Maximalizujte propustnost tématu s velkým počtem předplatných. Zpráva je přijata mnoho předplatných, což znamená, že kombinovaná míra příjmu přes všechna předplatná je mnohem větší než rychlost odesílání. Počet odesílatelů je malý. Počet příjemců na předplatné je malý.

Témata s velkým počtem předplatných obvykle zveřejňují nízkou celkovou propustnost, pokud jsou všechny zprávy směrovány do všech předplatných. Tato nízká propustnost je způsobena skutečností, že každá zpráva je přijata mnohokrát a všechny zprávy, které jsou obsaženy v tématu a všechny jeho odběry jsou uloženy ve stejném úložišti. Předpokládá se, že počet odesílatelů a počet příjemců na odběr je malý. Service Bus podporuje až 2 000 předplatných na téma.

Chcete-li maximalizovat propustnost, vyzkoušejte následující kroky:

* Pomocí asynchronních operací můžete využít výhod dávkování na straně klienta.
* Ke snížení počtu přenosů klientského protokolu Service Bus použijte výchozí interval dávkování 20 ms.
* Ponechte dávkový přístup do obchodu povolený. Tento přístup zvyšuje celkovou rychlost, jakou mohou být zprávy zapsány do tématu.
* Nastavte počet předběžného načtení na 20násobek očekávané míry příjmu v sekundách. Tento počet snižuje počet přenosů protokolu klienta Service Bus.

<!-- .NET Standard SDK, Microsoft.Azure.ServiceBus -->
[QueueClient]: /dotnet/api/microsoft.azure.servicebus.queueclient
[MessageSender]: /dotnet/api/microsoft.azure.servicebus.core.messagesender

<!-- .NET Framework SDK, Microsoft.Azure.ServiceBus -->
[MessagingFactory]: /dotnet/api/microsoft.servicebus.messaging.messagingfactory
[BatchFlushInterval]: /dotnet/api/microsoft.servicebus.messaging.messagesender.batchflushinterval
[ForcePersistence]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage.forcepersistence
[EnablePartitioning]: /dotnet/api/microsoft.servicebus.messaging.queuedescription.enablepartitioning
[TopicDescription.EnableFiltering]: /dotnet/api/microsoft.servicebus.messaging.topicdescription.enablefilteringmessagesbeforepublishing

<!-- Local links -->
[Partitioned messaging entities]: service-bus-partitioning.md