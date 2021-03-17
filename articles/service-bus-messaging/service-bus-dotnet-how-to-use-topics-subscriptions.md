---
title: Posílání zpráv do Azure Service Bus témat pomocí Azure-zasílání zpráv – ServiceBus
description: V tomto rychlém startu se dozvíte, jak odesílat zprávy pro Azure Service Bus témata pomocí balíčku Azure-Messaging-ServiceBus.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 60504bcf9e2c3f9460eee9a2e72d18767c0cfa71
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631670"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>Odeslání zpráv do Azure Service Bus tématu a příjem zpráv z předplatných do tématu (.NET)
V tomto kurzu se dozvíte, jak vytvořit konzolovou aplikaci .NET Core, která odesílá zprávy do Service Busho tématu a přijímá zprávy z předplatného tématu. 

> [!Important]
> V tomto rychlém startu se používá nový balíček **Azure. Messaging. ServiceBus** . Rychlý Start, který používá starý balíček Microsoft. Azure. ServiceBus, najdete v tématu [posílání a přijímání zpráv pomocí balíčku Microsoft. Azure. ServiceBus](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Požadavky

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat výhody pro [předplatitele sady Visual Studio nebo MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Postupujte podle kroků v [rychlém startu: pomocí Azure Portal vytvořte Service Bus téma a odběry tématu](service-bus-quickstart-topics-subscriptions-portal.md). Poznamenejte si připojovací řetězec, název tématu a název předplatného. Pro tento rychlý Start budete používat jenom jedno předplatné. 

## <a name="send-messages-to-a-topic"></a>Odeslání zprávy do tématu
V této části vytvoříte konzolovou aplikaci .NET Core v aplikaci Visual Studio, přidáte kód pro odesílání zpráv do tématu, které jste vytvořili. 

### <a name="create-a-console-application"></a>Vytvoření konzolové aplikace
Spusťte Visual Studio a vytvořte nový projekt **Konzolová aplikace (.NET Core)** pro C#. 

### <a name="add-the-service-bus-nuget-package"></a>Přidání balíčku Service Bus NuGet

1. Klikněte pravým tlačítkem na nově vytvořený projekt a vyberte možnost **Spravovat balíčky NuGet**.
1. Vyberte **Procházet**. Vyhledejte a vyberte **[Azure. Messaging. ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)**.
1. Vyberte **instalovat** a dokončete instalaci a potom zavřete Správce balíčků NuGet.

### <a name="add-code-to-send-messages-to-the-topic"></a>Přidání kódu pro odesílání zpráv do tématu 

1. V souboru Program.cs přidejte následující příkazy `using` do horní části definice oboru názvů před deklaraci třídy:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. Ve `Program` třídě deklarujte následující proměnné:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<TOPIC NAME>";
        static string subscriptionName = "<SUBSCRIPTION NAME>";
    ```

    Nahraďte následující hodnoty:
    - `<NAMESPACE CONNECTION STRING>` s připojovacím řetězcem k vašemu Service Bus oboru názvů
    - `<TOPIC NAME>` s názvem tématu
    - `<SUBSCRIPTION NAME>` s názvem předplatného
2. Přidejte metodu s názvem `SendMessageToTopicAsync` , která pošle jednu zprávu do tématu. 

    ```csharp
        static async Task SendMessageToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the topic
                ServiceBusSender sender = client.CreateSender(topicName);
                await sender.SendMessageAsync(new ServiceBusMessage("Hello, World!"));
                Console.WriteLine($"Sent a single message to the topic: {topicName}");
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
1. Přidejte do třídy metodu s názvem `SendMessageBatchAsync` `Program` a přidejte následující kód. Tato metoda přijímá frontu zpráv a připraví jednu nebo více dávek k odeslání do Service Bus tématu. 

    ```csharp
        static async Task SendMessageBatchToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {

                // create a sender for the topic 
                ServiceBusSender sender = client.CreateSender(topicName);

                // get the messages to be sent to the Service Bus topic
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus topic
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus topic
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

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {topicName}");
            }
        }
    ```
1. Nahraďte `Main()` metodu následující **asynchronní** `Main` metodou. Volá jak metody Send, tak odesílají jednu zprávu a dávku zpráv do tématu.  

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();
        }
    ```
5. Spusťte aplikaci. Měl by se zobrazit následující výstup:

    ```console
    Sent a single message to the topic: mytopic
    Sent a batch of 3 messages to the topic: mytopic
    ```
1. V Azure Portal postupujte podle následujících kroků:
    1. Přejděte do svého oboru názvů Service Bus. 
    1. Na stránce **Přehled** v dolním prostředním podokně přepněte na kartu **témata** a vyberte Service Bus téma. V následujícím příkladu je to `mytopic` .
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="Vybrat téma":::
    1. Na stránce **Service Bus téma** v grafu **zprávy** v dolní části **metriky** vidíte, že jsou k dispozici čtyři příchozí zprávy pro téma. Pokud nevidíte tuto hodnotu, počkejte několik minut a aktualizujte stránku, aby se zobrazil aktualizovaný graf. 

        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="Zprávy odeslané do tématu" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::
    4. V dolním podokně vyberte předplatné. V následujícím příkladu je to **S1**. Na stránce **Service Bus předplatné** se zobrazí **Počet aktivních zpráv** jako **4**. Předplatné přijalo čtyři zprávy, které jste poslali do tématu, ale žádný příjemce je ještě nevybral. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="Zprávy přijaté v rámci předplatného" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::
    

## <a name="receive-messages-from-a-subscription"></a>Přijímání zpráv z předplatného

1. Přidejte následující metody do `Program` třídy, která zpracovává zprávy a všechny chyby. 

    ```csharp
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. Do třídy přidejte následující metodu `ReceiveMessagesFromSubscriptionAsync` `Program` .

    ```csharp
        static async Task ReceiveMessagesFromSubscriptionAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());

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
1. Přidejte do metody volání `ReceiveMessagesFromSubscriptionAsync` metody `Main` . `SendMessagesToTopicAsync`Pokud chcete testovat pouze příjem zpráv, přidejte komentář k metodě. Pokud to neuděláte, zobrazí se další čtyři zprávy odeslané do tématu. 

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();

            // receive messages from the subscription
            await ReceiveMessagesFromSubscriptionAsync();
        }
    ```
## <a name="run-the-app"></a>Spuštění aplikace
Spusťte aplikaci. Počkejte minutu a potom stisknutím libovolné klávesy zastavte příjem zpráv. Měl by se zobrazit následující výstup (mezerník pro klíč). 

```console
Sent a single message to the topic: mytopic
Sent a batch of 3 messages to the topic: mytopic
Wait for a minute and then press any key to end the processing
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub

Stopping the receiver...
Stopped receiving messages
```

Znovu ověřte portál. 

- Na stránce **Service Bus téma** v grafu **zprávy** vidíte osm příchozích zpráv a osm odchozích zpráv. Pokud tato čísla nevidíte, počkejte pár minut a aktualizujte stránku, aby se zobrazil aktualizovaný graf. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="Odeslané a přijímané zprávy" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::
- Na stránce **Service Bus předplatné** se zobrazí **Počet aktivních zpráv** jako nula. Je to proto, že příjemce přijal zprávy z tohoto předplatného a dokončil zprávy. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="Počet aktivních zpráv v předplatném na konci" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::
    


## <a name="next-steps"></a>Další kroky
Podívejte se na následující dokumentaci a ukázky:

- [Klientská knihovna Azure Service Bus pro .NET – soubor Readme](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [Ukázky na GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [Referenční dokumentace k rozhraní .NET API](/dotnet/api/azure.messaging.servicebus?preserve-view=true)