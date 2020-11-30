---
title: Začínáme s frontami Azure Service Bus (Azure. Messaging. ServiceBus)
description: V tomto kurzu vytvoříte aplikaci .NET Core C# pro posílání zpráv a příjem zpráv z fronty Service Bus.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 1976848f9c91b5fd5e136e027b83456bbbe0b3b4
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96317431"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-net"></a>Posílání zpráv a příjem zpráv z Azure Service Bus front (.NET)
V tomto kurzu vytvoříte konzolovou aplikaci .NET Core pro posílání zpráv a příjem zpráv z fronty Service Bus pomocí balíčku **Azure. Messaging. ServiceBus** . 

> [!Important]
> V tomto rychlém startu se používá nový balíček Azure. Messaging. ServiceBus. Rychlý Start, který používá starý balíček Microsoft. Azure. ServiceBus, najdete v tématu [posílání a přijímání událostí pomocí balíčku Microsoft. Azure. ServiceBus](service-bus-dotnet-get-started-with-queues-legacy.md).

## <a name="prerequisites"></a>Požadavky

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat výhody pro [předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Pokud nemáte frontu, ve které byste mohli pracovat, postupujte podle kroků v tématu [použití Azure Portal k vytvoření fronty Service Bus](service-bus-quickstart-portal.md) . Poznamenejte si **připojovací řetězec** pro obor názvů Service Bus a název **fronty** , kterou jste vytvořili.

## <a name="send-messages-to-a-queue"></a>Zasílání zpráv do fronty
V tomto rychlém startu vytvoříte konzolovou aplikaci C# .NET Core, která bude odesílat zprávy do fronty.

### <a name="create-a-console-application"></a>Vytvoření konzolové aplikace
Spusťte Visual Studio a vytvořte nový projekt **Konzolová aplikace (.NET Core)** pro C#. 

### <a name="add-the-service-bus-nuget-package"></a>Přidání balíčku Service Bus NuGet

1. Klikněte pravým tlačítkem na nově vytvořený projekt a vyberte možnost **Spravovat balíčky NuGet**.
1. Vyberte **Procházet**. Vyhledejte a vyberte **[Azure. Messaging. ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)**.
1. Vyberte **instalovat** a dokončete instalaci a potom zavřete Správce balíčků NuGet.

### <a name="add-code-to-send-messages-to-the-queue"></a>Přidat kód pro posílání zpráv do fronty

1. V *program.cs* přidejte následující příkazy v `using` horní části definice oboru názvů před deklaraci třídy:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    
    using Azure.Messaging.ServiceBus;
    ```

1. Ve `Program` třídě deklarujte následující proměnné:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string queueName = "<QUEUE NAME>";
    ```

    Jako proměnnou zadejte připojovací řetězec pro obor názvů `ServiceBusConnectionString` . Zadejte název fronty.

1. Nahraďte `Main()` metodu následující **asynchronní** `Main` metodou. Volá `SendMessagesAsync()` metodu, kterou přidáte v dalším kroku k odesílání zpráv do fronty. 

    ```csharp
    public static async Task Main(string[] args)
    {    
        const int numberOfMessages = 10;
        
        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();
    }
    ```
1. Přímo za `Main()` metodu přidejte následující `SendMessagesAsync()` metodu, která provede práci odesláním počtu zpráv určených parametrem `numberOfMessagesToSend` (aktuálně nastaveným na hodnotu 10):

    ```csharp
        static async Task SendMessageAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // create a message that we can send
                ServiceBusMessage message = new ServiceBusMessage("Hello world!");

                // send the message
                await sender.SendMessageAsync(message);
                Console.WriteLine($"Sent a single message to the queue: {queueName}");
            }
        }
    ```
1. Přidejte metodu nazvanou `CreateMessages` pro vytvoření fronty (ve frontě .NET) zpráv do `Program` třídy. Obvykle tyto zprávy získáte z různých částí aplikace. Tady vytvoříme frontu ukázkových zpráv.

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message"));
            messages.Enqueue(new ServiceBusMessage("Second message"));
            messages.Enqueue(new ServiceBusMessage("Third message"));
            return messages;
        }
    ```
1. Přidejte do třídy metodu s názvem `SendMessageBatchAsync` `Program` a přidejte následující kód. Tato metoda přebírá frontu zpráv a připraví jednu nebo více dávek k odeslání do fronty Service Bus. 

    ```csharp
        static async Task SendMessageBatchAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // get the messages to be sent to the Service Bus queue
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus queue
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus queue
                while (messages.Count > 0)
                {
                    // start a new batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();

                    // add the first message to the batch
                    if (messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue once the message is added to the batch
                        messages.Dequeue();
                    }
                    else
                    {
                        // if the first message can't fit, then it is too large for the batch
                        throw new Exception($"Message {messageCount - messages.Count} is too large and cannot be sent.");
                    }

                    // add as many messages as possible to the current batch
                    while (messages.Count > 0 && messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue as it has been added to the batch
                        messages.Dequeue();
                    }
        
                    // now, send the batch
                    await sender.SendMessagesAsync(messageBatch);
        
                    // if there are any remaining messages in the .NET queue, the while loop repeats 
                }

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {queueName}");
            }
        }
    ```
1. Nahraďte `Main()` metodu následující **asynchronní** `Main` metodou. Volá jak metody Send, tak odesílají jednu zprávu a dávku zpráv do fronty. 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();
        }
    ```
5. Spusťte aplikaci. Měly by se zobrazit následující zprávy. 

    ```console
    Sent a single message to the queue: myqueue
    Sent a batch of messages to the queue: myqueue
    ```       
1. V Azure Portal postupujte podle následujících kroků:
    1. Přejděte do svého oboru názvů Service Bus. 
    1. Na stránce **Přehled** Vyberte frontu v dolním podokně. 
    1. Všimněte si hodnot v části **základy** .

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png" alt-text="Přijaté zprávy s počtem a velikostí" lightbox="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png":::

    Všimněte si následujících hodnot:
    - Hodnota **Počet aktivních zpráv** pro tuto frontu je teď **4**. Pokaždé, když spustíte tuto aplikaci odesílatele bez načtení zpráv, se tato hodnota zvýší o 4.
    - Aktuální velikost fronty zvýší **aktuální** hodnotu v nástroji **Essentials**  pokaždé, když aplikace přidá zprávy do fronty.
    - V grafu **zprávy** v části dolní **metrika** vidíte, že pro frontu jsou k dispozici čtyři příchozí zprávy. 

## <a name="receive-messages-from-a-queue"></a>Přijímání zpráv z fronty
V této části přidáte kód pro načtení zpráv z fronty.

1. Přidejte následující metody do `Program` třídy, která zpracovává zprávy a všechny chyby. 

    ```csharp
        // handle received messages
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        // handle any errors when receiving messages
        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. Přidejte do třídy metodu s názvem `ReceiveMessagesAsync` `Program` a přidejte následující kód pro příjem zpráv. 

    ```csharp
        static async Task ReceiveMessagesAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());

                // add handler to process messages
                processor.ProcessMessageAsync += MessageHandler;

                // add handler to process any errors
                processor.ProcessErrorAsync += ErrorHandler;

                // start processing 
                await processor.StartProcessingAsync();

                Console.WriteLine("Wait for a minute and then press any key to end the processing");
                Console.ReadKey();

                // stop processing 
                Console.WriteLine("\nStopping the receiver...");
                await processor.StopProcessingAsync();
                Console.WriteLine("Stopped receiving messages");
            }
        }
    ```
1. Přidejte volání `ReceiveMessagesAsync` metody z `Main` metody. `SendMessagesAsync`Pokud chcete testovat pouze příjem zpráv, přidejte komentář k metodě. Pokud to neuděláte, zobrazí se do fronty další čtyři zprávy. 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();

            // receive message from the queue
            await ReceiveMessagesAsync();
        }
    ```

## <a name="run-the-app"></a>Spuštění aplikace
Spusťte aplikaci. Počkejte minutu a potom stisknutím libovolné klávesy zastavte příjem zpráv. Měl by se zobrazit následující výstup (mezerník pro klíč). 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Wait for a minute and then press any key to end the processing
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch

Stopping the receiver...
Stopped receiving messages
```

Znovu ověřte portál. 

- **Počet aktivních zpráv** a **aktuální** hodnoty jsou nyní **0**.
- V grafu **zprávy** v části dolní **metrika** vidíte, že je pro frontu k dispozici osm příchozích zpráv a osm odchozích zpráv. 

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png" alt-text="Aktivní zprávy a velikost po přijetí" lightbox="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png":::

## <a name="next-steps"></a>Další kroky
Podívejte se na následující dokumentaci a ukázky:

- [Klientská knihovna Azure Service Bus pro .NET – soubor Readme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [Ukázky na GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [Referenční dokumentace k rozhraní .NET API](https://docs.microsoft.com/dotnet/api/azure.messaging.servicebus?view=azure-dotnet-preview&preserve-view=true)

