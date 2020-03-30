---
title: Vytvoření fronty service busu pomocí portálu Azure
description: V tomto rychlém startu se dozvíte, jak vytvořit frontu service bus pomocí portálu Azure. Potom použijete ukázkovou klientskou aplikaci k odesílání zpráv a přijímání zpráv z fronty.
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: quickstart
ms.custom: mvc
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 0c88db652efe54a497af094160fb426be7c43d16
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240643"
---
# <a name="quickstart-use-azure-portal-to-create-a-service-bus-queue"></a>Úvodní příručka: Vytvoření fronty service bus pomocí portálu Azure
Tento rychlý start popisuje, jak odesílat zprávy do fronty Service Bus a přijímat je. Používá se k tomu web [Azure Portal][Azure portal], kde se vytvoří obor názvů pro odesílání zpráv a v tomto oboru názvů fronta. Dále je třeba získat v oboru názvů přihlašovací údaje pro autorizaci. Postup pak ukazuje, jak do této fronty odesílat zprávy a přijímat je z ní pomocí [knihovny .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]

## <a name="prerequisites"></a>Požadavky

Před tímto kurzem se ujistěte, že máte nainstalované tyto položky:

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, můžete si vytvořit [bezplatný účet][] před tím, než začnete.
- [Visual Studio 2017 s aktualizací Update 3 (verze 15.3, 26730.01)](https://www.visualstudio.com/vs) nebo novější. Pomocí sady Visual Studio můžete vytvořit ukázku, která odesílá zprávy a přijímá zprávy z fronty. Ukázka je otestovat frontu, kterou jste vytvořili pomocí prostředí PowerShell. 
- [NET Core SDK](https://www.microsoft.com/net/download/windows) verze 2.0 nebo novější.

[!INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-queue-portal](../../includes/service-bus-create-queue-portal.md)]

## <a name="send-and-receive-messages"></a>Odesílání a příjem zpráv

> [!NOTE]
> Ukázka použitá v této části k odesílání a přijímání zpráv je ukázka rozhraní .NET. Ukázky pro odesílání a přijímání zpráv pomocí jiných programovacích jazyků naleznete v [tématu Service Bus ukázky](service-bus-samples.md). 
> 
> Podrobné pokyny pro odesílání a přijímání zpráv pomocí různých programovacích jazyků naleznete v následujících rychlých spuštěních:
> - [.NET](service-bus-dotnet-get-started-with-queues.md)
> - [Java](service-bus-java-how-to-use-queues.md)
> - [Soubor Node.js používající balíček azure/service-bus](service-bus-nodejs-how-to-use-queues-new-package.md)
> - [Soubor Node.js používající balíček azure-sb](service-bus-nodejs-how-to-use-queues.md)
> - [PHP](service-bus-php-how-to-use-queues.md)
> - [Python](service-bus-python-how-to-use-queues.md)
> - [Ruby](service-bus-ruby-how-to-use-queues.md)

Pokud jste zřídili obor názvů a frontu a máte potřebná pověření, můžete začít odesílat a přijímat zprávy. Kód si můžete zkontrolovat v [této složce s ukázkami na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus/TopicFilters).

Při spuštění kódu postupujte takto:

1. Pomocí následujícího příkazu naklonujte [úložiště Service Bus na GitHubu](https://github.com/Azure/azure-service-bus/):

   ```
   git clone https://github.com/Azure/azure-service-bus.git
   ```
3. Přejděte do složky s ukázkou `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`.
4. Zkopírujte připojovací řetězec a název fronty, které jste získali v části Získání přihlašovacích údajů pro správu.
5.  Do příkazového řádku zadejte následující příkaz:

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

Připojovací řetězec a název `Main()` fronty jsou předány metodě jako argumenty příkazového řádku. Metoda `Main()` deklaruje dvě proměnné řetězce, do kterých se tyto údaje načtou:

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

Metoda MainAsync() vytvoří klienta fronty s argumenty příkazového řádku, `RegisterOnMessageHandlerAndReceiveMessages()`zavolá obslužnou rutinu přijímající zprávy s názvem a odešle sadu zpráv:

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
> Prostředky služby Service Bus můžete spravovat pomocí [aplikace Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/). Průzkumník služby Service Bus umožňuje uživatelům připojit se k oboru názvů service bus a snadno spravovat entity zasílání zpráv. Nástroj poskytuje pokročilé funkce, jako je funkce importu a exportu nebo možnost testovat téma, fronty, předplatná, přenosové služby, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořil obor názvů služby Service Bus a další prostředky potřebné k odesílání a přijímání zpráv z fronty. Další informace o psaní kódu pro odesílání a přijímání zpráv, pokračujte v kurzech v části **Odesílat a přijímat zprávy.** 

> [!div class="nextstepaction"]
> [Odesílání a příjem zpráv](service-bus-dotnet-get-started-with-queues.md)


[bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Azure portal]: https://portal.azure.com/

[service-bus-flow]: ./media/service-bus-quickstart-portal/service-bus-flow.png
