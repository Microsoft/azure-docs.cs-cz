---
title: Začínáme s tématy a odběry služby Azure Service Bus | Dokumentace Microsoftu
description: Napíšeme konzolovou aplikaci C# .NET Core využívající témata a odběry zasílání zpráv služby Service Bus.
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 12/6/2017
ms.author: sethm
ms.openlocfilehash: aa75ac48d650f28d4aaeb612f2900d705cf71b5b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29396937"
---
# <a name="get-started-with-service-bus-topics"></a>Začínáme s tématy služby Service Bus

[!INCLUDE [service-bus-selector-topics](../../includes/service-bus-selector-topics.md)]

Tento kurz se zabývá následujícími kroky:

1. Pomocí webu Azure Portal vytvoříme obor názvů služby Service Bus.
2. Pomocí webu Azure Portal vytvoříme téma služby Service Bus.
3. Pomocí webu Azure Portal vytvoříme k tomuto tématu odběr služby Service Bus.
4. Napíšeme konzolovou aplikaci .NET Core pro odeslání sady zpráv do tématu.
5. Napíšeme konzolovou aplikaci .NET Core pro příjem těchto zpráv z odběru.

## <a name="prerequisites"></a>Požadavky

1. [Visual Studio 2017 s aktualizací Update 3 (verze 15.3, 26730.01)](http://www.visualstudio.com/vs) nebo novější.
2. [NET Core SDK](https://www.microsoft.com/net/download/windows) verze 2.0 nebo novější.
2. Předplatné Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="1-create-a-namespace-using-the-azure-portal"></a>1. Vytvoření oboru názvů služby Service Bus pomocí webu Azure Portal

> [!NOTE] 
> Obor názvů služby Service Bus a entity zasílání zpráv můžete vytvořit také pomocí [PowerShellu](/powershell/azure/get-started-azureps). Další informace najdete v tématu [Správa prostředků služby Service Bus pomocí PowerShellu](service-bus-manage-with-ps.md).

Pokud už máte vytvořený obor názvů pro zasílání zpráv služby Service Bus, přejděte k části [Vytvoření tématu pomocí webu Azure Portal](#2-create-a-topic-using-the-azure-portal).

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="2-create-a-topic-using-the-azure-portal"></a>2. Vytvoření tématu pomocí webu Azure Portal

1. Přihlaste se k webu [Azure Portal][azure-portal].
2. V levém navigačním podokně portálu klikněte na **Service Bus** (pokud položku **Service Bus** nevidíte, klikněte na **Všechny služby**) nebo klikněte na **Všechny prostředky**. Klikněte na obor názvů, ve kterém chcete vytvořit téma. 
3. Otevře se okno přehledu oboru názvů. Klikněte na **Témata**:
   
    ![Vytvoření tématu][createtopic1]
4. Klikněte na **+ Téma**.
   
    ![Výběr témat][createtopic2]
5. Zadejte název tématu. U ostatních možností ponechte jejich výchozí hodnoty.
   
    ![Vyberte Nový][createtopic3]
6. V dolní části dialogového okna klikněte na **Vytvořit**.

## <a name="3-create-a-subscription-to-the-topic"></a>3. Vytvoření odběru tématu

1. V podokně prostředků na portálu klikněte na obor názvů, který jste vytvořili v kroku 1, klikněte na **Témata** a pak klikněte na název tématu, které jste vytvořili v kroku 2.
2. V horní části podokna přehledu kliknutím na **+ Odběr** přidejte odběr tohoto tématu.

    ![Vytvoření odběru][createtopic4]

3. Zadejte název odběru. U ostatních možností ponechte jejich výchozí hodnoty.

## <a name="4-send-messages-to-the-topic"></a>4. Odesílání zpráv do tématu

Abychom mohli do tématu odesílat zprávy, napíšeme v sadě Visual Studio konzolovou aplikaci v jazyce C#.

### <a name="create-a-console-application"></a>Vytvoření konzolové aplikace

Spusťte sadu Visual Studio a vytvořte nový projekt **Konzolová aplikace (.NET Core)**.

### <a name="add-the-service-bus-nuget-package"></a>Přidání balíčku Service Bus NuGet

1. Klikněte pravým tlačítkem na nově vytvořený projekt a vyberte možnost **Spravovat balíčky NuGet**.
2. Klikněte na kartu **Procházet**, vyhledejte **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** a pak vyberte položku **Microsoft.Azure.ServiceBus**. Klikněte na **Instalovat** a dokončete instalaci, pak zavřete dialogové okno.
   
    ![Výběr balíčku NuGet][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-topic"></a>Napsání kódu pro odesílání zpráv do tématu

1. V souboru Program.cs přidejte následující příkazy `using` do horní části definice oboru názvů před deklaraci třídy:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. V rámci třídy `Program` deklarujte následující proměnné. Nastavte proměnnou `ServiceBusConnectionString` na připojovací řetězec, který jste získali při vytváření oboru názvů, a proměnnou `TopicName` nastavte na název, který jste použili při vytváření tématu:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    static ITopicClient topicClient;
    ``` 

3. Nahraďte výchozí obsah metody `Main()` následujícím řádkem kódu:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```
   
4. Přímo za metodu `Main()` přidejte následující asynchronní metodu `MainAsync()`, která volá metodu pro odeslání zpráv:

    ```csharp
    static async Task MainAsync()
    {
        const int numberOfMessages = 10;
        topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await topicClient.CloseAsync();
    }
    ```

5. Přímo za metodu `MainAsync()` přidejte následující metodu `SendMessagesAsync()`, která provádí odeslání určitého počtu zpráv zadaného v proměnné `numberOfMessagesToSend` (aktuálně nastavená na hodnotu 10):

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the topic.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the topic.
                await topicClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
  
6. Soubor Program.cs odesílatele by měl vypadat takto:
   
    ```csharp
    namespace CoreSenderApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            static ITopicClient topicClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                const int numberOfMessages = 10;
                topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");

                // Send messages.
                await SendMessagesAsync(numberOfMessages);

                Console.ReadKey();

                await topicClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the topic
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the topic
                        await topicClient.SendAsync(message);
                    }
                }
                catch (Exception exception)
                {
                    Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
                }
            }
        }
    }
    ```

3. Spusťte program a podívejte se na web Azure Portal: klikněte na název vašeho tématu v okně **Přehled** oboru názvů. Zobrazí se obrazovka **Základy** tématu. Všimněte si, že v odběru uvedeném v dolní části okna je teď hodnota **Počet zpráv** pro odběr **10**. Pokaždé, když spustíte aplikaci odesílatele bez načtení zpráv (jak je popsáno v další části), se tato hodnota zvýší o 10. Všimněte si také, že aktuální velikost tématu navyšuje hodnotu **Aktuální** v okně **Základy** pokaždé, když aplikace do daného tématu přidá zprávy.
   
      ![Velikost zpráv][topic-message]

## <a name="5-receive-messages-from-the-subscription"></a>5. Příjem zpráv z odběru

Pokud chcete přijímat zprávy, které jste právě odeslali, vytvořte další konzolovou aplikaci .NET Core a nainstalujte balíček NuGet **Microsoft.Azure.ServiceBus**, podobně jako předtím u aplikace odesílatele.

### <a name="write-code-to-receive-messages-from-the-subscription"></a>Napsání kódu pro příjem zpráv z odběru

1. V souboru Program.cs přidejte následující příkazy `using` do horní části definice oboru názvů před deklaraci třídy:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. V rámci třídy `Program` deklarujte následující proměnné. Nastavte proměnnou `ServiceBusConnectionString` na připojovací řetězec, který jste získali při vytváření oboru názvů, proměnnou `TopicName` nastavte na název, který jste použili při vytváření tématu, a proměnnou `SubscriptionName` nastavte na název, který jste použili při vytváření odběru tématu:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    const string SubscriptionName = "<your_subscription_name>";
    static ISubscriptionClient subscriptionClient;
    ```

3. Nahraďte výchozí obsah metody `Main()` následujícím řádkem kódu:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. Přímo za metodu `Main()` přidejte následující asynchronní metodu `MainAsync()`, která volá metodu `RegisterOnMessageHandlerAndReceiveMessages()`:

    ```csharp
    static async Task MainAsync()
    {
        subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register subscription message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await subscriptionClient.CloseAsync();
    }
    ```

5. Přímo za metodu `MainAsync()` přidejte následující metodu, která registruje popisovač zprávy a přijímá zprávy odeslané aplikací odesílatele:

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```    

6. Přímo za předchozí metodu přidejte následující metodu `ProcessMessagesAsync()` pro zpracování přijatých zpráv:
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
        await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
        // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

7. Nakonec přidejte následující metodu, která zpracovává případné výjimky, ke kterým může dojít:
 
    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }    
    ```    

8. Soubor Program.cs příjemce by měl vypadat takto:
   
    ```csharp
    namespace CoreReceiverApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            const string SubscriptionName = "<your_subscription_name>";
            static ISubscriptionClient subscriptionClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");

                // Register subscription message handler and receive messages in a loop.
                RegisterOnMessageHandlerAndReceiveMessages();

                Console.ReadKey();

                await subscriptionClient.CloseAsync();
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that processes messages.
                subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message.
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
                await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
                // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
                // to avoid unnecessary exceptions.
            }

            static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
            {
                Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
                var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
                Console.WriteLine("Exception context for troubleshooting:");
                Console.WriteLine($"- Endpoint: {context.Endpoint}");
                Console.WriteLine($"- Entity Path: {context.EntityPath}");
                Console.WriteLine($"- Executing Action: {context.Action}");
                return Task.CompletedTask;
            }
        }
    }
    ```
9. Spusťte program a znovu se podívejte na portál. Všimněte si, že hodnoty **Počet zpráv** a **Aktuální** jsou nyní **0**.
   
    ![Délka tématu][topic-message-receive]

Blahopřejeme! Právě jste s použitím knihovny .NET Standard vytvořili téma a odběr, odeslali 10 zpráv a přijali tyto zprávy.

## <a name="next-steps"></a>Další kroky

Podívejte se na naše [úložiště GitHub s ukázkami](https://github.com/Azure/azure-service-bus/tree/master/samples), které předvádějí některé pokročilejší funkce zasílání zpráv služby Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
