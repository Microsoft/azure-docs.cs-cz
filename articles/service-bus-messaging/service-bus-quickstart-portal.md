---
title: Rychlý start – použití webu Azure portal můžete vytvořit frontu služby Service Bus | Dokumentace Microsoftu
description: V tomto rychlém startu se dozvíte, jak můžete vytvořit frontu služby Service Bus pomocí webu Azure portal. Ukázková klientská aplikace pak použijete k odesílání a příjem zpráv z fronty.
services: service-bus-messaging
author: spelluru
manager: timlt
ms.service: service-bus-messaging
ms.topic: quickstart
ms.custom: mvc
ms.date: 04/10/2019
ms.author: spelluru
ms.openlocfilehash: 315f8d30b7c7559947c599edd0e18eaa5a99ac22
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2019
ms.locfileid: "67513635"
---
# <a name="quickstart-use-azure-portal-to-create-a-service-bus-queue"></a>Rychlý start: Vytvořit frontu služby Service Bus pomocí webu Azure portal
Tento rychlý start popisuje způsob odesílání a příjem zpráv z fronty Service Bus a pomocí [webu Azure portal][Azure portal] vytvořit obor názvů pro zasílání zpráv a do fronty v daném oboru názvů a získat přihlašovací údaje pro ověření na tomto obor názvů. Postup pak ukazuje, jak do této fronty odesílat zprávy a přijímat je z ní pomocí [knihovny .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Požadavky

Před tímto kurzem se ujistěte, že máte nainstalované tyto položky:

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si vytvořit [bezplatný účet][] před tím, než začnete.
- [Visual Studio 2017 s aktualizací Update 3 (verze 15.3, 26730.01)](https://www.visualstudio.com/vs) nebo novější. Sestavit ukázku, která odesílá zprávy a přijímá zprávy z fronty pomocí sady Visual Studio. Ukázka je k testování fronty, kterou jste vytvořili pomocí prostředí PowerShell. 
- [NET Core SDK](https://www.microsoft.com/net/download/windows) verze 2.0 nebo novější.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="send-and-receive-messages"></a>Odesílání a příjem zpráv

Pokud jste zřídili obor názvů a frontu a máte potřebná pověření, můžete začít odesílat a přijímat zprávy. Kód si můžete zkontrolovat v [této složce s ukázkami na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters).

Kód spustíte následovně:

1. Pomocí následujícího příkazu naklonujte [úložiště Service Bus na GitHubu](https://github.com/Azure/azure-service-bus/):

   ```
   git clone https://github.com/Azure/azure-service-bus.git
   ```
3. Přejděte do složky s ukázkou `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`.
4. Zkopírujte připojovací řetězec a název fronty, které jste získali v získat správu pověření oddílu.
5.  Na příkazovém řádku zadejte následující příkaz:

    ```
    dotnet build
    ```
6.  Přejděte do složky `bin\Debug\netcoreapp2.0`.
7.  Zadáním následujícího příkazu spusťte program. Nezapomeňte `myConnectionString` nahradit hodnotou, kterou jste předtím získali, a `myQueueName` nahraďte názvem vytvořené fronty:

    ```shell
    dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
    ``` 
8. Sledujte, jak se do fronty odešle deset zpráv a jak se následně z fronty přijmou:

   ![výstup programu](./media/service-bus-quickstart-portal/dotnet.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

K odebrání skupiny prostředků, oboru názvů a fronty můžete použít web Azure Portal.

## <a name="understand-the-sample-code"></a>Vysvětlení vzorového kódu

Tato část obsahuje další podrobnosti o chování ukázkového kódu. 

### <a name="get-connection-string-and-queue"></a>Získání připojovacího řetězce a fronty

Název připojovacího řetězce a fronty jsou předány `Main()` metody jako argumenty příkazového řádku. Metoda `Main()` deklaruje dvě proměnné řetězce, do kterých se tyto údaje načtou:

```csharp
static void Main(string[] args)
{
    string ServiceBusConnectionString = "";
    string QueueName = "";

    for (int i = 0; i < args.Length; i++)
    {
        var p = new Program();
        if (args[i] == "-ConnectionString")
        {
            Console.WriteLine($"ConnectionString: {args[i+1]}");
            ServiceBusConnectionString = args[i + 1]; 
        }
        else if(args[i] == "-QueueName")
        {
            Console.WriteLine($"QueueName: {args[i+1]}");
            QueueName = args[i + 1];
        }                
    }

    if (ServiceBusConnectionString != "" && QueueName != "")
        MainAsync(ServiceBusConnectionString, QueueName).GetAwaiter().GetResult();
    else
    {
        Console.WriteLine("Specify -Connectionstring and -QueueName to execute the example.");
        Console.ReadKey();
    }                            
}
```
 
Metoda `Main()` pak spustí asynchronní smyčku zpráv, `MainAsync()`.

### <a name="message-loop"></a>Smyčka zpráv

Metoda MainAsync() vytvoří frontu klienta s argumenty příkazového řádku, volá přijímající obslužné rutiny zpráv s názvem `RegisterOnMessageHandlerAndReceiveMessages()`a odesílá sadu zpráv:

```csharp
static async Task MainAsync(string ServiceBusConnectionString, string QueueName)
{
    const int numberOfMessages = 10;
    queueClient = new QueueClient(ServiceBusConnectionString, QueueName);

    Console.WriteLine("======================================================");
    Console.WriteLine("Press any key to exit after receiving all the messages.");
    Console.WriteLine("======================================================");

    // Register QueueClient's MessageHandler and receive messages in a loop
    RegisterOnMessageHandlerAndReceiveMessages();

    // Send Messages
    await SendMessagesAsync(numberOfMessages);

    Console.ReadKey();

    await queueClient.CloseAsync();
}
```

Metoda `RegisterOnMessageHandlerAndReceiveMessages()` jednoduše nastaví některé možnosti obslužné rutiny zpráv a potom zavolá metodu `RegisterMessageHandler()` klienta fronty, která spustí příjem:

```csharp
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
```

### <a name="send-messages"></a>Odesílání zpráv

Operace vytvoření a odeslání zpráv probíhají v metodě `SendMessagesAsync()`:

```csharp
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
```

### <a name="process-messages"></a>Zpracování zpráv

Metoda `ProcessMessagesAsync()` rozpoznává, zpracovává a dokončuje příjem zpráv:

```csharp
static async Task ProcessMessagesAsync(Message message, CancellationToken token)
{
    // Process the message
    Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

    // Complete the message so that it is not received again.
    await queueClient.CompleteAsync(message.SystemProperties.LockToken);
}
```
> [!NOTE]
> Můžete spravovat prostředky služby Service Bus s [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Exploreru umožňuje uživatelům připojit k oboru názvů služby Service Bus a správě entit pro zasílání zpráv snadno způsobem. Tento nástroj nabízí pokročilé funkce, například funkce importu/exportu nebo možnost otestovat tématu, fronty, předplatná, služby pro přenos přes, notification hubs a centra událostí. 

## <a name="next-steps"></a>Další postup

V tomto článku jste vytvořil obor názvů služby Service Bus a další prostředky potřebné k odesílání a přijímání zpráv z fronty. Další informace o psaní kódu pro odesílání a příjem zpráv, pokračovat v kurzech **odesílání a příjem zpráv** oddílu. 

> [!div class="nextstepaction"]
> [Odesílání a příjem zpráv](service-bus-dotnet-get-started-with-queues.md)


[bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png
