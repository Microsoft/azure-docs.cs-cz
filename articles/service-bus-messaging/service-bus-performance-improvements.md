---
title: Osvědčené postupy pro zlepšení výkonu pomocí Azure Service Bus
description: Popisuje, jak použít Service Bus k optimalizaci výkonu při výměně zprostředkovaných zpráv.
ms.topic: article
ms.date: 01/15/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 4c775555f82258c532d72917220129e3913ad314
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456041"
---
# <a name="best-practices-for-performance-improvements-using-service-bus-messaging"></a>Doporučené postupy pro zlepšení výkonu pomocí zasílání zpráv Service Bus

Tento článek popisuje, jak použít Azure Service Bus k optimalizaci výkonu při výměně zprostředkovaných zpráv. První část tohoto článku popisuje různé mechanismy pro zvýšení výkonu. Druhá část poskytuje pokyny k používání Service Bus způsobem, který může nabízet nejlepší výkon v daném scénáři.

V rámci tohoto článku pojem "klient" odkazuje na libovolnou entitu, která přistupuje k Service Bus. Klient může převzít roli odesilatele nebo příjemce. Termín "odesilatel" se používá pro klienta Service Bus fronty nebo pro klienta tématu, který odesílá zprávy do fronty Service Bus nebo tématu. Pojem "příjemce" odkazuje na klienta Service Bus Queue nebo předplatného, který přijímá zprávy z fronty Service Bus nebo z předplatného.

## <a name="protocols"></a>Protokoly
Service Bus umožňuje klientům odesílat a přijímat zprávy prostřednictvím jednoho ze tří protokolů:

1. Rozšířený protokol řízení front zpráv (AMQP)
2. Protokol SBMP (Service Bus Messaging Protocol)
3. Hypertextový přenosový protokol (HTTP)

AMQP je nejúčinnější, protože udržuje připojení k Service Bus. Implementuje také [dávkování](#batching-store-access) a [předběžné načítání](#prefetching). Pokud není výslovně uvedeno jinak, veškerý obsah v tomto článku předpokládá použití AMQP nebo SBMP.

> [!IMPORTANT]
> SBMP je k dispozici pouze pro .NET Framework. AMQP je výchozí hodnota pro .NET Standard.

## <a name="choosing-the-appropriate-service-bus-net-sdk"></a>Výběr vhodné Service Bus .NET SDK
Existují tři podporované Azure Service Bus sady .NET SDK. Jejich rozhraní API jsou podobná a můžou být matoucí, který z nich si vybrat. V následující tabulce najdete informace, které vám pomůžou s vaším rozhodnutím. Azure. Messaging. ServiceBus SDK je nejnovější a doporučujeme ho použít přes jiné sady SDK. Azure. Messaging. ServiceBus i Microsoft. Azure. ServiceBus sady SDK jsou moderní, výkonná a kompatibilní s více platformami. Kromě toho podporují AMQP přes objekty WebSockets a jsou součástí kolekce Azure .NET SDK pro open source projekty.

| Balíček NuGet | Primární obory názvů | Minimální počet platforem: | Protokoly |
|---------------|----------------------|---------------------|-------------|
| [Azure. Messaging. ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus) | `Azure.Messaging.ServiceBus`<br>`Azure.Messaging.ServiceBus.Administration` | .NET Core 2.0<br>.NET Framework 4.6.1<br>Mono 5,4<br>Xamarin. iOS 10,14<br>Xamarin. Mac 3,8<br>Xamarin. Android 8,0<br>Univerzální platforma Windows 10.0.16299 | AMQP<br>HTTP |
| [Microsoft. Azure. ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/) | `Microsoft.Azure.ServiceBus`<br>`Microsoft.Azure.ServiceBus.Management` | .NET Core 2.0<br>.NET Framework 4.6.1<br>Mono 5,4<br>Xamarin. iOS 10,14<br>Xamarin. Mac 3,8<br>Xamarin. Android 8,0<br>Univerzální platforma Windows 10.0.16299 | AMQP<br>HTTP |
| [WindowsAzure. ServiceBus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) | `Microsoft.ServiceBus`<br>`Microsoft.ServiceBus.Messaging` | .NET Framework 4.6.1 | AMQP<br>SBMP<br>HTTP |

Další informace o minimální podpoře .NET Standard platforem najdete v tématu [Podpora implementace rozhraní .NET](/dotnet/standard/net-standard#net-implementation-support).

## <a name="reusing-factories-and-clients"></a>Používání továrn a klientů
# <a name="azuremessagingservicebus-sdk"></a>[Azure. Messaging. ServiceBus SDK](#tab/net-standard-sdk-2)
Service Bus objekty, které pracují se službou, jako je například [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient), [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender), [ServiceBusReceiver](/dotnet/api/azure.messaging.servicebus.servicebusreceiver)a [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor), by měly být registrovány pro vkládání závislostí jako singleton (nebo instance jednou a sdílená). ServiceBusClient je možné zaregistrovat pro vkládání závislostí s [ServiceBusClientBuilderExtensions](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/src/Compatibility/ServiceBusClientBuilderExtensions.cs). 

Po odeslání nebo přijetí každé zprávy doporučujeme, abyste tyto objekty nezavřeli nebo neodstranili. Pokud chcete zavřít nebo odstranit objekty specifické pro entitu (ServiceBusSender/Receiver/procesor), dojde k odtržení odkazu na službu Service Bus. Zrušením ServiceBusClient výsledků odtrhnout připojení ke službě Service Bus. Navázání připojení je náročná operace, kterou se můžete vyhnout opětovným použitím stejného ServiceBusClient a vytvořením potřebných objektů specifických pro danou entitu ze stejné instance ServiceBusClient. Tyto objekty klienta můžete bezpečně použít pro souběžné asynchronní operace a z více vláken.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

Service Bus klientské objekty, jako jsou implementace [`IQueueClient`][QueueClient] nebo [`IMessageSender`][MessageSender] , by měly být registrovány pro vkládání závislostí jako typu Singleton (nebo instance jednou a sdílená). Po odeslání zprávy doporučujeme, abyste nezavřeli objekty pro vytváření zpráv, fronty, témata nebo předplatné, a pak je znovu vytvoříte při odeslání další zprávy. Ukončení objektu pro vytváření zpráv odstraní připojení ke službě Service Bus. Při opětovném vytvoření továrny se vytvoří nové připojení. Navázání připojení je náročná operace, kterou se můžete vyhnout opakovanému použití stejného objektu factory a klienta pro více operací. Tyto objekty klienta můžete bezpečně použít pro souběžné asynchronní operace a z více vláken.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

Service Bus objekty klienta, například `QueueClient` nebo `MessageSender` , jsou vytvořeny prostřednictvím objektu [MessagingFactory][MessagingFactory] , který poskytuje také interní správu připojení. Po odeslání zprávy doporučujeme, abyste nezavřeli objekty pro vytváření zpráv, fronty, témata nebo předplatné, a pak je znovu vytvoříte při odeslání další zprávy. Při zavírání továrny zasílání zpráv se odstraní připojení ke službě Service Bus a při opětovném vytváření továrny se vytvoří nové připojení. Navázání připojení je náročná operace, kterou se můžete vyhnout opakovanému použití stejného objektu factory a klienta pro více operací. Tyto objekty klienta můžete bezpečně použít pro souběžné asynchronní operace a z více vláken.

---

## <a name="concurrent-operations"></a>Souběžné operace
Operace, jako je například Send, Receive, DELETE atd., nějakou dobu trvá. Tato doba zahrnuje dobu, kterou služba Service Bus potřebuje k zpracování operace, a latenci žádosti a odpovědi. Chcete-li zvýšit počet operací v čase, operace musí být spuštěny souběžně.

Klient plánuje souběžné operace provedením **asynchronních** operací. Další požadavek se spustí před dokončením předchozí žádosti. Následující fragment kódu je příkladem asynchronní operace odeslání:

# <a name="azuremessagingservicebus-sdk"></a>[Azure. Messaging. ServiceBus SDK](#tab/net-standard-sdk-2)
```csharp
var messageOne = new ServiceBusMessage(body);
var messageTwo = new ServiceBusMessage(body);

var sendFirstMessageTask =
    sender.SendMessageAsync(messageOne).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #1");
    });
var sendSecondMessageTask =
    sender.SendMessageAsync(messageTwo).ContinueWith(_ =>
    {
        Console.WriteLine("Sent message #2");
    });

await Task.WhenAll(sendFirstMessageTask, sendSecondMessageTask);
Console.WriteLine("All messages sent");

```

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

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

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

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

Následující kód je příkladem asynchronní operace Receive.

# <a name="azuremessagingservicebus-sdk"></a>[Azure. Messaging. ServiceBus SDK](#tab/net-standard-sdk-2)

```csharp
var client = new ServiceBusClient(connectionString);
var options = new ServiceBusProcessorOptions 
{

      AutoCompleteMessages = false,
      MaxConcurrentCalls = 20
};
await using ServiceBusProcessor processor = client.CreateProcessor(queueName,options);
processor.ProcessMessageAsync += MessageHandler;
processor.ProcessErrorAsync += ErrorHandler;

static Task ErrorHandler(ProcessErrorEventArgs args)
{
    Console.WriteLine(args.Exception);
    return Task.CompletedTask;
};

static async Task MessageHandler(ProcessMessageEventArgs args)
{
    Console.WriteLine("Handle message");
    await args.CompleteMessageAsync(args.Message);
}

await processor.StartProcessingAsync();
```

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

Příklady úplného <a href="https://github.com/Azure/azure-service-bus/blob/master/samples/DotNet/Microsoft.Azure.ServiceBus/SendersReceiversWithQueues" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> zdrojového kódu </a>najdete v úložišti GitHub:

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
        MaxConcurrentCalls = 20
    });
```

`MessageReceiver`Vytvoří se instance objektu s připojovacím řetězcem, názvem fronty a režimem příjmu náhledu. Dále `receiver` instance slouží k registraci obslužné rutiny zprávy.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

Příklady úplného <a href="https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.ServiceBus.Messaging/SendersReceiversWithQueues" target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> zdrojového kódu </a>najdete v úložišti GitHub:

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
        MaxConcurrentCalls = 20
    });
```

`MessagingFactory`Vytvoří `factory` objekt z připojovacího řetězce. S `factory` instancí instance `MessageReceiver` je vytvořena instance. Dále `receiver` instance slouží k registraci obslužné rutiny zpráv.

---

## <a name="receive-mode"></a>Režim příjmu

Když vytváříte klienta front nebo odběrů, můžete zadat režim přijímání: *prohlížet a uzamknout* nebo *přijmout a odstranit*. Výchozí režim příjmu je `PeekLock` . Při provozu ve výchozím režimu pošle klient žádost o přijetí zprávy od Service Bus. Jakmile klient obdrží zprávu, pošle požadavek na dokončení zprávy.

Při nastavení režimu příjmu na `ReceiveAndDelete` je oba kroky kombinovány v jednom požadavku. Tyto kroky omezují celkový počet operací a můžou zlepšit celkovou propustnost zpráv. Tento zvýšení výkonu přináší riziko ztráty zpráv.

Service Bus nepodporuje transakce pro operace Receive a DELETE. U všech scénářů, ve kterých bude klient chtít zprávu odložit nebo [nedoručeně](service-bus-dead-letter-queues.md) , se vyžaduje taky sémantika prohlížení zámku.

## <a name="client-side-batching"></a>Dávkování na straně klienta

Dávkování na straně klienta umožňuje klientovi nebo tématu klienta zpozdit odeslání zprávy po určitou dobu. Pokud během tohoto časového období klient odešle další zprávy, přenese zprávy v jedné dávce. Dávkování na straně klienta také způsobí, že klient fronty nebo předplatného vygeneruje více **úplných** požadavků do jediného požadavku. Dávkování je k dispozici pouze pro operace asynchronního **odeslání** a **dokončení** . Synchronní operace se okamžitě odesílají do služby Service Bus. K dávkování nedochází při operacích prohlížení a příjmu, ani k dávkování mezi klienty.

# <a name="azuremessagingservicebus-sdk"></a>[Azure. Messaging. ServiceBus SDK](#tab/net-standard-sdk-2)
Funkce dávkování pro sadu .NET Standard SDK zatím nevystavuje vlastnost k manipulaci.

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

Funkce dávkování pro sadu .NET Standard SDK zatím nevystavuje vlastnost k manipulaci.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

Ve výchozím nastavení klient používá interval dávky 20 MS. Interval dávky můžete změnit nastavením vlastnosti [BatchFlushInterval][BatchFlushInterval] před vytvořením objektu pro vytváření zpráv. Toto nastavení má vliv na všechny klienty vytvořené touto továrnou.

Chcete-li zakázat dávkování, nastavte vlastnost [BatchFlushInterval][BatchFlushInterval] na **hodnotu TimeSpan. Zero**. Například:

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

Dávkování nemá vliv na počet fakturovatelných operací zasílání zpráv a je k dispozici pouze pro Service Bus klientský protokol pomocí knihovny [Microsoft. ServiceBus. Messaging](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) . Protokol HTTP nepodporuje dávkování.

> [!NOTE]
> Nastavení `BatchFlushInterval` zajistí, že dávkování bude z pohledu aplikace implicitní. tj.; aplikace vytváří `SendAsync` a `CompleteAsync` volá a neprovádí specifická volání Batch.
>
> Explicitní dávkování na straně klienta lze implementovat pomocí níže uvedeného volání metody:
> ```csharp
> Task SendBatchAsync(IEnumerable<BrokeredMessage> messages);
> ```
> Souhrnná velikost zpráv musí být menší než maximální velikost podporovaná cenovou úrovní.

---

## <a name="batching-store-access"></a>Batch – přístup k úložišti

Pokud chcete zvýšit propustnost fronty, tématu nebo předplatného, Service Bus se při zápisu do svého interního úložiště dávky vyřadí několik zpráv. 

- Když povolíte dávkování ve frontě, zapisuje se zprávy do úložiště a odstraňují se zprávy ze Storu, budou v dávce. 
- Když povolíte dávkování pro téma, zapisuje se do úložiště zprávy. 
- Když povolíte dávkování v rámci předplatného, budou se odstraňovat zprávy ze Storu. 
- Když je pro entitu povolený přístup k dávkovému ukládání, Service Bus zpozdí operaci zápisu úložiště pro tuto entitu až o 20 MS.

> [!NOTE]
> Nehrozí riziko ztráty zpráv s dávkou, a to ani v případě, že dojde k selhání Service Bus na konci intervalu dávkování 20ms.

Další operace úložiště, ke kterým dojde během tohoto intervalu, se přidají do dávky. Přístup k dávkovému úložišti má vliv jenom na operace **Send** a **Complete** . operace přijímání nejsou ovlivněny. Přístup k Batch Storu je vlastnost v entitě. Dávkování probíhá napříč všemi entitami, které umožňují přístup k dávce v dávkovém úložišti.

Při vytváření nové fronty, tématu nebo předplatného je ve výchozím nastavení povolený přístup k Batch Storu.


# <a name="azuremessagingservicebus-sdk"></a>[Azure. Messaging. ServiceBus SDK](#tab/net-standard-sdk-2)
Pokud chcete zakázat přístup pomocí dávkového úložiště, budete potřebovat instanci `ServiceBusAdministrationClient` . Vytvořte `CreateQueueOptions` z popisu fronty, na kterou vlastnost nastavuje `EnableBatchedOperations` `false` .

```csharp
var options = new CreateQueueOptions(path)
{
    EnableBatchedOperations = false
};
var queue = await administrationClient.CreateQueueAsync(options);
```


# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

Pokud chcete zakázat přístup pomocí dávkového úložiště, budete potřebovat instanci `ManagementClient` . Vytvořte frontu z popisu fronty, `EnableBatchedOperations` na kterou vlastnost nastavuje `false` .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = await managementClient.CreateQueueAsync(queueDescription);
```

Další informace najdete v následujících článcích:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.queuedescription.enablebatchedoperations" target="_blank">`Microsoft.Azure.ServiceBus.Management.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.subscriptiondescription.enablebatchedoperations" target="_blank">`Microsoft.Azure.ServiceBus.Management.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.management.topicdescription.enablebatchedoperations" target="_blank">`Microsoft.Azure.ServiceBus.Management.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

Pokud chcete zakázat přístup pomocí dávkového úložiště, budete potřebovat instanci `NamespaceManager` . Vytvořte frontu z popisu fronty, `EnableBatchedOperations` na kterou vlastnost nastavuje `false` .

```csharp
var queueDescription = new QueueDescription(path)
{
    EnableBatchedOperations = false
};
var queue = namespaceManager.CreateQueue(queueDescription);
```

Další informace najdete v následujících článcích:
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queuedescription.enablebatchedoperations" target="_blank">`Microsoft.ServiceBus.Messaging.QueueDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptiondescription.enablebatchedoperations" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.topicdescription.enablebatchedoperations" target="_blank">`Microsoft.ServiceBus.Messaging.TopicDescription.EnableBatchedOperations` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

Přístup k Batch Storu nemá vliv na počet fakturovatelných operací zasílání zpráv. Jedná se o vlastnost fronty, tématu nebo předplatného. Nezávisí na režimu příjmu a protokolu, který se používá mezi klientem a službou Service Bus.

## <a name="prefetching"></a>Předběžné načítání

[Předběžné načítání](service-bus-prefetch.md) umožňuje klientovi fronty nebo předplatného načítat další zprávy ze služby při přijímání zpráv. Klient ukládá tyto zprávy do místní mezipaměti. Velikost mezipaměti je určena `QueueClient.PrefetchCount` `SubscriptionClient.PrefetchCount` vlastnostmi nebo. Každý klient, který umožňuje předběžné navýšení, zachovává svou vlastní mezipaměť. Mezipaměť není sdílená mezi klienty. Pokud klient spustí operaci přijetí a jeho mezipaměť je prázdná, služba přenáší dávku zpráv. Velikost dávky se rovná velikosti mezipaměti nebo 256 KB, podle toho, která hodnota je menší. Pokud klient spustí operaci přijetí a mezipaměť obsahuje zprávu, zpráva se povede z mezipaměti.

Při předběžném načtení zprávy služba uzamkne přednačtenou zprávu. V případě zámku nelze přednačtené zprávy přijmout jiným přijímačem. Pokud příjemce nemůže dokončit zprávu před vypršením platnosti zámku, bude zpráva k dispozici ostatním příjemcům. Přednačtená kopie zprávy zůstane v mezipaměti. Přijímač, který využívá kopii v mezipaměti s vypršenou platností, obdrží při pokusu o dokončení této zprávy výjimku. Ve výchozím nastavení vyprší platnost zámku zprávy po 60 sekundách. Tato hodnota se dá prodloužit na 5 minut. Chcete-li zabránit vyčerpání zpráv s vypršenou platností, nastavte velikost mezipaměti menší, než je počet zpráv, které může klient spotřebovat v intervalu časového limitu zámku.

Při použití výchozího zámku vypršení 60 sekund je platná hodnota `PrefetchCount` 20 časů maximálního počtu zpracování všech přijímačů v továrně. Například továrna vytvoří tři přijímače a každý příjemce může zpracovat až 10 zpráv za sekundu. Počet předběžných načtení by neměl přesáhnout 20 X 3 X 10 = 600. Ve výchozím nastavení `PrefetchCount` je nastavena na hodnotu 0, což znamená, že ze služby nejsou načteny žádné další zprávy.

Předběžné načítání zpráv zvyšuje celkovou propustnost pro frontu nebo odběr, protože snižuje celkový počet operací se zprávami nebo se na ně zaznamená výměna zpráv. Načítají se první zpráva, ale bude trvat déle (kvůli zvýšené velikosti zprávy). Přijímání předem načtených zpráv z mezipaměti bude rychlejší, protože klient již tyto zprávy stáhl.

Hodnota vlastnosti TTL (Time-to-Live) zprávy je serverem zkontrolována v době, kdy server odesílá klientovi zprávu. Klient při přijetí zprávy nekontroluje vlastnost TTL zprávy. Místo toho lze zprávu přijmout i v případě, že hodnota TTL zprávy byla předána, zatímco byla zpráva uložena do mezipaměti klienta.

Předběžné načítání neovlivňuje počet fakturovaných operací zasílání zpráv a je k dispozici pouze pro Service Bus klientský protokol. Protokol HTTP nepodporuje předběžné načtení. Předběžné načtení je k dispozici pro synchronní i asynchronní operace Receive.

# <a name="azuremessagingservicebus-sdk"></a>[Azure. Messaging. ServiceBus SDK](#tab/net-standard-sdk-2)
Další informace najdete v následujících `PrefetchCount` vlastnostech:

- [ServiceBusReceiver.PrefetchCount](/dotnet/api/azure.messaging.servicebus.servicebusreceiver.prefetchcount)
- [ServiceBusProcessor.PrefetchCount](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.prefetchcount)

Hodnoty pro tyto vlastnosti můžete nastavit v [ServiceBusReceiverOptions](/dotnet/api/azure.messaging.servicebus.servicebusreceiveroptions) nebo [ServiceBusProcessorOptions](/dotnet/api/azure.messaging.servicebus.servicebusprocessoroptions).

# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. ServiceBus SDK](#tab/net-standard-sdk)

Další informace najdete v následujících `PrefetchCount` vlastnostech:

* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.queueclient.prefetchcount" target="_blank">`Microsoft.Azure.ServiceBus.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.azure.servicebus.subscriptionclient.prefetchcount" target="_blank">`Microsoft.Azure.ServiceBus.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

# <a name="windowsazureservicebus-sdk"></a>[WindowsAzure. ServiceBus SDK](#tab/net-framework-sdk)

Další informace najdete v následujících `PrefetchCount` vlastnostech:

* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.queueclient.prefetchcount" target="_blank">`Microsoft.ServiceBus.Messaging.QueueClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.
* <a href="https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.subscriptionclient.prefetchcount" target="_blank">`Microsoft.ServiceBus.Messaging.SubscriptionClient.PrefetchCount` <span class="docon docon-navigate-external x-hidden-focus"></span></a>.

---

## <a name="prefetching-and-receivebatch"></a>Přednačtení a ReceiveBatch
I když koncepce předběžného načítání více zpráv má podobnou sémantiku zpracování zpráv v dávce ( `ReceiveBatch` ), existují drobné rozdíly, které je potřeba při použití těchto přístupů zachovat.

Předběžné načtení je konfigurace (nebo režim) na klientovi ( `QueueClient` a `SubscriptionClient` ) a `ReceiveBatch` jedná se o operaci (která má sémantiku požadavku-odpověď).

Při použití těchto přístupů společně Vezměte v úvahu následující případy:

* Předběžné načtení by mělo být větší než nebo rovno počtu zpráv, ze kterých očekáváte příjem `ReceiveBatch` .
* Předběžného načtení může být až n/3 krát počet zpracovaných zpráv za sekundu, kde n je výchozí doba trvání zámku.

Existují některé problémy s hladkou metodou, což znamená zachování vysokého počtu předběžného načtení, protože znamená, že je zpráva pevně nastavená na konkrétního příjemce. Doporučujeme, abyste si vyzkoušeli hodnoty předběžného načítání mezi výše uvedenými mezními hodnotami a empirickě Identifikujte, co se vejde.

## <a name="multiple-queues"></a>Více front

Pokud jedna fronta nebo téma nedokáže zpracovat očekávanou hodnotu, použijte více entit zasílání zpráv. Při použití více entit vytvořte vyhrazeného klienta pro každou entitu namísto použití stejného klienta pro všechny entity.

## <a name="development-and-testing-features"></a>Funkce pro vývoj a testování

> [!NOTE]
> Tato část se vztahuje jenom na sadu SDK WindowsAzure. ServiceBus, jako je Microsoft. Azure. ServiceBus a Azure. Messaging. ServiceBus tuto funkci nezveřejňuje.

Service Bus má jednu funkci, která se používá specificky pro vývoj, která **by se nikdy neměla používat v produkčních konfiguracích**: [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] .

Když jsou do tématu přidána nová pravidla nebo filtry, můžete použít [`TopicDescription.EnableFilteringMessagesBeforePublishing`][TopicDescription.EnableFiltering] k ověření, že nový výraz filtru pracuje podle očekávání.

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

Pokud chcete dosáhnout maximální propustnosti napříč několika frontami, použijte nastavení s popisem pro maximalizaci propustnosti jedné fronty. K vytváření klientů, kteří odesílají nebo získávají z různých front, můžete také použít různé továrny.

### <a name="low-latency-queue"></a>Fronta s nízkou latencí

Cíl: minimalizace latence fronty nebo tématu. Počet odesílatelů a přijímačů je malý. Propustnost fronty je malá nebo střední.

* Zakažte dávkování na straně klienta. Klient okamžitě pošle zprávu.
* Zakažte přístup k dávkovému ukládání. Služba okamžitě zapíše zprávu do úložiště.
* Pokud používáte jednoho klienta, nastavte počet předběžných hodnot na 20 časů zpracování přijímače. Pokud fronta současně dorazí do fronty více zpráv, protokol klienta Service Bus odesílá všechna data ve stejnou dobu. Když klient obdrží další zprávu, tato zpráva je již v místní mezipaměti. Mezipaměť by měla být malá.
* Pokud používáte více klientů, nastavte počet předběžných hodnot na 0. Když nastavíte počet, druhý klient může obdržet druhou zprávu, zatímco první klient stále zpracovává první zprávu.

### <a name="queue-with-a-large-number-of-senders"></a>Zařadit do fronty s velkým počtem odesílatelů

Cíl: maximalizuje propustnost fronty nebo tématu s velkým počtem odesílatelů. Každý odesilatel odesílá zprávy se střední sazbou. Počet přijímačů je malý.

Service Bus umožňuje až 1000 souběžných připojení k entitě zasílání zpráv. Toto omezení se vynutilo na úrovni oboru názvů a fronty, témata nebo odběry se omezené omezením počtu souběžných připojení na obor názvů. U front se toto číslo sdílí mezi odesílateli a přijímači. Pokud se pro odesílatele vyžadují všechna připojení 1000, nahraďte frontu tématem a jedním předplatným. Téma přijímá až 1000 souběžných připojení od odesílatelů. Předplatné přijímá další 1000 souběžných připojení od přijímačů. Pokud potřebujete více než 1000 současných odesílatelů, odesílají odesílatelé zprávy do protokolu Service Bus přes HTTP.

K maximalizaci propustnosti použijte následující postup:

* Pokud je každý odesilatel v jiném procesu, použijte pro každý proces jenom jeden objekt factory.
* Pomocí asynchronních operací můžete využít dávkování na straně klienta.
* Snižte počet Service Busch přenosů klientských protokolů pomocí výchozího intervalu dávkování 20 MS.
* Nechte povolený přístup k Batch Storu. Tento přístup zvyšuje celkovou rychlost, kterou lze zprávy zapsat do fronty nebo tématu.
* Nastavte počet předběžných hodnot na 20 časů, který bude mít maximální počet zpracování všech přijímačů továrny. Tento počet snižuje počet Service Busch přenosů klientských protokolů.

### <a name="queue-with-a-large-number-of-receivers"></a>Zařazení do fronty s velkým počtem přijímačů

Cíl: Maximalizujte míru přijetí fronty nebo předplatného s velkým počtem přijímačů. Každý příjemce obdrží zprávy se střední sazbou. Počet odesílatelů je malý.

Service Bus povoluje až 1000 souběžných připojení k entitě. Pokud fronta vyžaduje více než 1000 přijímačů, nahraďte frontu tématem a několika odběry. Každé předplatné může podporovat až 1000 souběžných připojení. Přijímačé taky můžou ke frontě přistupovat přes protokol HTTP.

Pokud chcete maximalizovat propustnost, postupujte podle těchto pokynů:

* Pokud je každý příjemce v jiném procesu, použijte pro každý proces jenom jeden objekt factory.
* Přijímačé můžou používat synchronní nebo asynchronní operace. Vzhledem k střední míře příjmu jednotlivého přijímače dávka na straně klienta s úplným požadavkem neovlivní propustnost přijímače.
* Nechte povolený přístup k Batch Storu. Tento přístup omezuje celkové zatížení entity. Zároveň se tím snižuje celková rychlost, kterou lze zprávy zapsat do fronty nebo tématu.
* Nastavte počet předběžných hodnot na malou hodnotu (například PrefetchCount = 10). Tento počet brání přijímačům v nečinnosti, zatímco ostatní příjemci mají velký počet zpráv uložených v mezipaměti.

### <a name="topic-with-a-few-subscriptions"></a>Téma s několika předplatnými

Cíl: maximalizace propustnosti tématu s několika předplatnými. Celá řada předplatných obdrží zprávu, což znamená, že celková míra příjmu u všech předplatných je větší než rychlost odesílání. Počet odesílatelů je malý. Počet přijímačů na předplatné je malý.

Pokud chcete maximalizovat propustnost, postupujte podle těchto pokynů:

* Pro zvýšení celkové míry odeslání do tématu použijte k vytvoření odesílatelů více tovární zpráv. Pro každého odesilatele použijte asynchronní operace nebo více vláken.
* Pro zvýšení celkové míry příjmu z předplatného použijte k vytvoření přijímačů více zpráv. Pro každého příjemce použijte asynchronní operace nebo více vláken.
* Pomocí asynchronních operací můžete využít dávkování na straně klienta.
* Snižte počet Service Busch přenosů klientských protokolů pomocí výchozího intervalu dávkování 20 MS.
* Nechte povolený přístup k Batch Storu. Tento přístup zvyšuje celkovou rychlost, kterou lze do tématu zapsat zprávy.
* Nastavte počet předběžných hodnot na 20 časů, který bude mít maximální počet zpracování všech přijímačů továrny. Tento počet snižuje počet Service Busch přenosů klientských protokolů.

### <a name="topic-with-a-large-number-of-subscriptions"></a>Téma s velkým počtem předplatných

Cíl: maximalizace propustnosti tématu s velkým počtem předplatných. Celá řada předplatných obdrží zprávu, což znamená, že celková míra příjmu u všech předplatných je mnohem větší než rychlost odesílání. Počet odesílatelů je malý. Počet přijímačů na předplatné je malý.

Témata s velkým počtem předplatných obvykle zveřejňují nízkou celkovou propustnost, pokud jsou všechny zprávy směrovány do všech předplatných. Je to proto, že každá zpráva je přijata mnohokrát a všechny zprávy v tématu a všechny její odběry jsou uloženy ve stejném úložišti. Předpokladem je, že počet odesílatelů a počet příjemců na předplatné je malý. Service Bus podporuje až 2 000 předplatných na jedno téma.

Pokud chcete maximalizovat propustnost, zkuste provést následující kroky:

* Pomocí asynchronních operací můžete využít dávkování na straně klienta.
* Snižte počet Service Busch přenosů klientských protokolů pomocí výchozího intervalu dávkování 20 MS.
* Nechte povolený přístup k Batch Storu. Tento přístup zvyšuje celkovou rychlost, kterou lze do tématu zapsat zprávy.
* Nastavte počet předplatných na hodnotu 20 časů očekávané míry příjmu v sekundách. Tento počet snižuje počet Service Busch přenosů klientských protokolů.

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
