---
title: Začínáme s frontami služby Azure Service Bus | Dokumentace Microsoftu
description: V tomto kurzu vytvoříte aplikace konzoly .NET Core pro odesílání zpráv a přijímání zpráv z fronty služby Service Bus.
services: service-bus-messaging
documentationcenter: .net
author: axisc
manager: timlt
editor: spelluru
ms.assetid: 68a34c00-5600-43f6-bbcc-fea599d500da
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: conceptual
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/24/2020
ms.author: aschhab
ms.openlocfilehash: 5718106aee0e60d111398efdb839945c2c7a8a06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471733"
---
# <a name="get-started-with-service-bus-queues"></a>Začínáme s frontami služby Service Bus
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]
V tomto kurzu vytvoříte aplikace konzoly .NET Core pro odesílání zpráv a přijímání zpráv z fronty služby Service Bus.

## <a name="prerequisites"></a>Požadavky

- [Visual Studio 2019](https://www.visualstudio.com/vs).
- [NET Core SDK](https://www.microsoft.com/net/download/windows) verze 2.0 nebo novější.
- Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat [výhody předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Pokud nemáte frontu pro práci s, postupujte podle kroků na [portálu Azure k vytvoření](service-bus-quickstart-portal.md) článku fronty Service Bus k vytvoření fronty.

  - Přečtěte si rychlý přehled front service bus.
  - Vytvořte obor názvů service bus.
  - Získejte připojovací řetězec.
  - Vytvořte frontu služby Service Bus.

## <a name="send-messages-to-the-queue"></a>Zasílání zpráv do fronty

Abychom mohli do fronty odesílat zprávy, napíšeme v sadě Visual Studio konzolovou aplikaci v jazyce C#.

### <a name="create-a-console-application"></a>Vytvoření konzolové aplikace

Spusťte Visual Studio a vytvořte nový projekt **konzolové aplikace (.NET Core)** pro C#. Tento příklad pojmenuje aplikaci *CoreSenderApp*.

### <a name="add-the-service-bus-nuget-package"></a>Přidání balíčku Service Bus NuGet

1. Klikněte pravým tlačítkem na nově vytvořený projekt a vyberte možnost **Spravovat balíčky NuGet**.
1. Vyberte **Procházet**. Vyhledejte a vyberte **[microsoft.azure.servicebus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)**.
1. Vyberte **Instalovat,** chcete-li dokončit instalaci, zavřete Správce balíčků NuGet.

    ![Výběr balíčku NuGet][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-queue"></a>Napsání kódu pro odesílání zpráv do fronty

1. V *Program.cs*přidejte `using` následující příkazy v horní části definice oboru názvů před deklaraci třídy:

    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. Ve `Program` třídě deklarujte následující proměnné:

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    Zadejte připojovací řetězec `ServiceBusConnectionString` pro obor názvů jako proměnnou. Zadejte název fronty.

1. Nahraďte metodu `Main()` následující **asynchronní** `Main` metodou. Volá metodu, `SendMessagesAsync()` kterou přidáte v dalším kroku k odeslání zpráv do fronty. 

    ```csharp
    public static async Task Main(string[] args)
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
1. Bezprostředně za `MainAsync()` metodu přidejte následující `SendMessagesAsync()` metodu, která provádí práci `numberOfMessagesToSend` odesílání počtu zpráv určených (aktuálně nastavena na 10):

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

Zde je to, co *váš Program.cs* soubor by měl vypadat.

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

        public static async Task Main(string[] args)
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

Spusťte program a zkontrolujte portál Azure.

Vyberte název fronty v okně **Přehled** oboru názvů, chcete-li zobrazit frontu **Essentials**.

![Přijaté zprávy s počtem a velikostí][queue-message]

Hodnota **Počet zpráv Active** pro frontu je nyní **10**. Pokaždé, když spustíte tuto aplikaci odesílatele bez načtení zpráv, tato hodnota se zvýší o 10.

Aktuální velikost fronty zvyšuje hodnotu **CURRENT** v **položkách Essentials** pokaždé, když aplikace přidá zprávy do fronty.

V další části je popsáno, jak načíst tyto zprávy.

## <a name="receive-messages-from-the-queue"></a>Přijetí zpráv z fronty

Chcete-li přijímat odeslané zprávy, vytvořte jinou **aplikaci console app (.NET Core).** Nainstalujte balíček **Microsoft.Azure.ServiceBus** NuGet, stejně jako u aplikace odesílatele.

### <a name="write-code-to-receive-messages-from-the-queue"></a>Napsání kódu pro příjem zpráv z fronty

1. V *Program.cs*přidejte `using` následující příkazy v horní části definice oboru názvů před deklaraci třídy:

    ```csharp
    using System;
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

1. Ve `Program` třídě deklarujte následující proměnné:

    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string QueueName = "<your_queue_name>";
    static IQueueClient queueClient;
    ```

    Zadejte připojovací řetězec `ServiceBusConnectionString` pro obor názvů jako proměnnou. Zadejte název fronty.

1. Nahraďte metodu `Main()` následujícím kódem:

    ```csharp
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
    ```

1. Bezprostředně za `MainAsync()` metodu přidejte následující metodu, která zaregistruje obslužnou rutinu zprávy a přijme zprávy odeslané aplikací odesílatele:

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

1. Přímo za předchozí metodu přidejte následující metodu `ProcessMessagesAsync()` pro zpracování přijatých zpráv:

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

1. Nakonec přidejte následující metodu, která zpracovává případné výjimky, ke kterým může dojít:

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

Zde je to, co *váš Program.cs* soubor by měl vypadat takto:

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

Spusťte program a znovu se podívejte na portál. Počet **aktivních zpráv** a **aktuální** hodnoty jsou nyní **0**.

![Fronta po přijetí zpráv][queue-message-receive]

Blahopřejeme! Nyní jste vytvořili frontu, odeslali do ní sadu zpráv a tyto zprávy jste obdrželi ze stejné fronty.

> [!NOTE]
> Prostředky služby Service Bus můžete spravovat pomocí [aplikace Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Průzkumník služby Service Bus umožňuje uživatelům snadno se připojit k oboru názvů service bus a spravovat entity zasílání zpráv. Nástroj poskytuje pokročilé funkce, jako je funkce importu a exportu nebo možnost testovat témata, fronty, předplatná, přenosové služby, centra oznámení a centra událostí.

## <a name="next-steps"></a>Další kroky

Podívejte se na naše [úložiště GitHub s ukázkami](https://github.com/Azure/azure-service-bus/tree/master/samples), které předvádějí některé pokročilejší funkce zasílání zpráv služby Service Bus.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-get-started-with-queues/nuget-package.png
[queue-message]: ./media/service-bus-dotnet-get-started-with-queues/messages-sent-to-essentials.png
[queue-message-receive]: ./media/service-bus-dotnet-get-started-with-queues/queue-message-receive-in-essentials.png

