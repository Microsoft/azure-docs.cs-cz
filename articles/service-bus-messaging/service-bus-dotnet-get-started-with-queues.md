---
title: Začínáme s frontami služby Azure Service Bus | Dokumentace Microsoftu
description: Napíšeme konzolovou aplikaci C# využívající fronty zasílání zpráv služby Service Bus.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: hero-article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/23/2019
ms.author: aschhab
ms.openlocfilehash: 2075f8f83392c9ddd1761a96958fcb6cb12dc013
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55816420"
---
# <a name="get-started-with-service-bus-queues"></a>Začínáme s frontami služby Service Bus

[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Tento kurz se zabývá následujícími kroky:

1. Pomocí webu Azure Portal vytvoříme obor názvů služby Service Bus.
2. Pomocí webu Azure Portal vytvoříme frontu služby Service Bus.
3. Napíšeme konzolovou aplikaci .NET Core pro odeslání sady zpráv do fronty.
4. Napíšeme konzolovou aplikaci .NET Core pro příjem těchto zpráv z fronty.

## <a name="prerequisites"></a>Požadavky

1. [Visual Studio 2017 s aktualizací Update 3 (verze 15.3, 26730.01)](https://www.visualstudio.com/vs) nebo novější.
2. [NET Core SDK](https://www.microsoft.com/net/download/windows) verze 2.0 nebo novější.
2. Předplatné Azure.

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-a-namespace-using-the-azure-portal"></a>Vytvoření oboru názvů služby Service Bus pomocí webu Azure Portal

> [!NOTE] 
> Obor názvů služby Service Bus a entity zasílání zpráv můžete vytvořit také pomocí [PowerShellu](/powershell/azure/get-started-azureps). Další informace najdete v tématu [Správa prostředků služby Service Bus pomocí PowerShellu](service-bus-manage-with-ps.md).

Pokud jste již vytvořili obor názvů zasílání zpráv Service Bus, přejděte k vytvoření fronty pomocí části portálu Azure.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-queue-using-the-azure-portal"></a>Vytvoření fronty pomocí webu Azure Portal

Pokud jste již vytvořili frontu služby Service Bus, přejděte do odesílat zprávy do fronty oddílu.

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="send-messages-to-the-queue"></a>Zasílání zpráv do fronty

Abychom mohli do fronty odesílat zprávy, napíšeme v sadě Visual Studio konzolovou aplikaci v jazyce C#.

### <a name="create-a-console-application"></a>Vytvoření konzolové aplikace

Spusťte sadu Visual Studio a vytvořte nový projekt **Konzolová aplikace (.NET Core)**.

### <a name="add-the-service-bus-nuget-package"></a>Přidání balíčku Service Bus NuGet

1. Klikněte pravým tlačítkem na nově vytvořený projekt a vyberte možnost **Spravovat balíčky NuGet**.
2. Klikněte na kartu **Procházet**, vyhledejte **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** a pak vyberte položku **Microsoft.Azure.ServiceBus**. Klikněte na **Instalovat** a dokončete instalaci, pak zavřete dialogové okno.
   
    ![Výběr balíčku NuGet][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>Napsání kódu pro odesílání zpráv do fronty

1. V souboru Program.cs přidejte následující příkazy `using` do horní části definice oboru názvů před deklaraci třídy:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. V rámci třídy `Program` deklarujte následující proměnné. Nastavte proměnnou `ServiceBusConnectionString` na připojovací řetězec, který jste získali při vytváření oboru názvů, a proměnnou `QueueName` nastavte na název, který jste použili při vytváření fronty:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
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
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await queueClient.CloseAsync();
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
                // Create a new message to send to the queue.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the queue.
                await queueClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
   
6. Soubor Program.cs by měl vypadat takhle.
   
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
            // Connection String for the namespace can be obtained from the Azure portal under the 
            // 'Shared Access policies' section.
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string QueueName = "<your_queue_name>";
            static IQueueClient queueClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                const int numberOfMessages = 10;
                queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");

                // Send Messages
                await SendMessagesAsync(numberOfMessages);
                        
                Console.ReadKey();

                await queueClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the queue
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the queue
                        await queueClient.SendAsync(message);
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

7. Spusťte program a podívejte se na web Azure Portal: klikněte na název vaší fronty v okně **Přehled** oboru názvů. Zobrazí se obrazovka **Základy** fronty. Všimněte si, že hodnota **Počet aktivních zpráv** pro frontu je teď **10**. Pokaždé, když spustíte aplikaci odesílatele bez načtení zpráv (jak je popsáno v další části), se tato hodnota zvýší o 10. Všimněte si také, že aktuální velikost fronty navyšuje hodnotu **Aktuální** v okně **Základy** pokaždé, když aplikace do dané fronty přidá zprávy.
   
      ![Velikost zpráv][queue-message]

## <a name="receive-messages-from-the-queue"></a>Přijetí zpráv z fronty

Pokud chcete přijímat zprávy, které jste právě odeslali, vytvořte další konzolovou aplikaci .NET Core a nainstalujte balíček NuGet **Microsoft.Azure.ServiceBus**, podobně jako předtím u aplikace odesílatele.

### <a name="write-code-to-receive-messages-from-the-queue"></a>Napsání kódu pro příjem zpráv z fronty

1. V souboru Program.cs přidejte následující příkazy `using` do horní části definice oboru názvů před deklaraci třídy:
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. V rámci třídy `Program` deklarujte následující proměnné. Nastavte proměnnou `ServiceBusConnectionString` na připojovací řetězec, který jste získali při vytváření oboru názvů, a proměnnou `QueueName` nastavte na název, který jste použili při vytváření fronty:
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

3. Nahraďte výchozí obsah metody `Main()` následujícím řádkem kódu:

    ```csharp
    MainAsync().GetAwaiter().GetResult();
    ```

4. Přímo za metodu `Main()` přidejte následující asynchronní metodu `MainAsync()`, která volá metodu `RegisterOnMessageHandlerAndReceiveMessages()`:

    ```csharp
    static async Task MainAsync()
    {
        queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register the queue message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await queueClient.CloseAsync();
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
        queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```

6. Přímo za předchozí metodu přidejte následující metodu `ProcessMessagesAsync()` pro zpracování přijatých zpráv:
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the queue Client is created in ReceiveMode.PeekLock mode (which is the default).
        await queueClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
        // If queueClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
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
   
8. Soubor Program.cs by měl vypadat takhle:
   
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
            // Connection String for the namespace can be obtained from the Azure portal under the 
            // 'Shared Access policies' section.
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string QueueName = "<your_queue_name>";
            static IQueueClient queueClient;

            static void Main(string[] args)
            {
                MainAsync().GetAwaiter().GetResult();
            }

            static async Task MainAsync()
            {
                queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");

                // Register QueueClient's MessageHandler and receive messages in a loop
                RegisterOnMessageHandlerAndReceiveMessages();
                    
                Console.ReadKey();

                await queueClient.CloseAsync();
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the MessageHandler Options in terms of exception handling, number of concurrent messages to deliver etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of Concurrent calls to the callback `ProcessMessagesAsync`, set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that will process messages
                queueClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the queueClient is created in ReceiveMode.PeekLock mode (which is default).
                await queueClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the queueClient has already been closed.
                // If queueClient has already been Closed, you may chose to not call CompleteAsync() or AbandonAsync() etc. calls 
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
4. Spusťte program a znovu se podívejte na portál. Všimněte si, že hodnoty **Počet aktivních zpráv** a **Aktuální** jsou teď **0**.
   
    ![Délka fronty][queue-message-receive]

Blahopřejeme! Vytvořili jste frontu, do této fronty jste odeslali sadu zpráv a ze stejné fronty jste tyto zprávy přijali.

## <a name="next-steps"></a>Další postup

Podívejte se na naše [úložiště GitHub s ukázkami](https://github.com/Azure/azure-service-bus/tree/master/samples), které předvádějí některé pokročilejší funkce zasílání zpráv služby Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/queue-message.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive.png

