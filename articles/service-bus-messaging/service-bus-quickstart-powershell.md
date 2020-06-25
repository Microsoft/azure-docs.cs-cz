---
title: Vytvoření fronty Service Bus pomocí Azure PowerShell
description: V tomto rychlém startu se dozvíte, jak pomocí Azure PowerShell vytvořit frontu Service Bus. Pak použijete ukázkovou aplikaci k posílání zpráv do fronty a jejich přijímání.
author: spelluru
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.openlocfilehash: f1fecfd7ba9f35d06b680d43248bf82aeb54a27b
ms.sourcegitcommit: 61d92af1d24510c0cc80afb1aebdc46180997c69
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2020
ms.locfileid: "85337239"
---
# <a name="quickstart-use-azure-powershell-to-create-a-service-bus-queue"></a>Rychlý Start: použití Azure PowerShell k vytvoření fronty Service Bus
Tento rychlý start popisuje, jak odesílat zprávy do fronty Service Bus a přijímat je. Používá se k tomu prostředí PowerShell, kde se vytvoří obor názvů pro zasílání zpráv a v tomto oboru názvů fronta. Dále je třeba získat v oboru názvů přihlašovací údaje pro autorizaci. Postup pak ukazuje, jak do této fronty odesílat zprávy a přijímat je z ní pomocí [knihovny .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [howto-service-bus-queues](../../includes/howto-service-bus-queues.md)]


## <a name="prerequisites"></a>Požadavky

Před tímto kurzem se ujistěte, že máte nainstalované tyto položky:

- Předplatné Azure. Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet][], ještě než začnete. 
- [Visual Studio 2017 s aktualizací Update 3 (verze 15.3, 26730.01)](https://www.visualstudio.com/vs) nebo novější. Pomocí sady Visual Studio vytvoříte ukázku, která odesílá zprávy do fronty a přijímá z ní zprávu. Ukázka je testování fronty, kterou jste vytvořili na portálu. 
- [NET Core SDK](https://www.microsoft.com/net/download/windows) verze 2.0 nebo novější.

Tento rychlý start vyžaduje použití nejnovější verze Azure PowerShellu. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace a konfigurace Azure PowerShell][]. Pokud jste obeznámeni s Azure Cloud Shell, můžete ji použít bez instalace Azure PowerShell na svém počítači. Podrobnosti o Azure Cloud Shell najdete v tématu [přehled Azure Cloud Shell](../cloud-shell/overview.md)

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

1. Pokud jste to ještě neudělali, nainstalujte modul PowerShell služby Service Bus:

   ```azurepowershell-interactive
   Install-Module Az.ServiceBus
   ```

2. Spuštěním následujícího příkazu se přihlaste k Azure:

   ```azurepowershell-interactive
   Login-AzAccount
   ```

3. Zadáním následujících příkazů nastavte aktuální kontext předplatného, případně zobrazte momentálně aktivní předplatné:

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "MyAzureSubName" 
   Get-AzContext
   ```

## <a name="provision-resources"></a>Zřízení prostředků

Do příkazového řádku PowerShell zadejte následující příkazy, kterými zřídíte prostředky služby Service Bus. Nezapomeňte všechny zástupné symboly nahradit příslušnými hodnotami:

```azurepowershell-interactive
# Create a resource group 
New-AzResourceGroup –Name my-resourcegroup –Location eastus

# Create a Messaging namespace
New-AzServiceBusNamespace -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Location eastus

# Create a queue 
New-AzServiceBusQueue -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Name queue-name -EnablePartitioning $False

# Get primary connection string (required in next step)
Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
```

Po spuštění rutiny `Get-AzServiceBusKey` zkopírujte vybraný připojovací řetězec a název fronty a vložte je do dočasného umístění, třeba do Poznámkového bloku. Budete je potřebovat v dalším kroku.

## <a name="send-and-receive-messages"></a>Odesílání a příjem zpráv

Pokud jste vytvořili obor názvů a frontu a máte potřebná pověření, můžete začít odesílat a přijímat zprávy. Kód si můžete zkontrolovat v [této složce s ukázkami na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveQuickStart).

Při spuštění kódu postupujte takto:

1. Pomocí následujícího příkazu naklonujte [úložiště Service Bus na GitHubu](https://github.com/Azure/azure-service-bus/):

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

3. Přejděte do složky s ukázkou `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`.

4. Pokud jste to ještě neudělali, získejte pomocí následující rutiny PowerShellu připojovací řetězec. Zástupné texty `my-resourcegroup` a `namespace-name` nezapomeňte nahradit svými konkrétními hodnotami: 

   ```azurepowershell-interactive
   Get-AzServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
   ```

5. Do příkazového řádku PowerShellu zadejte následující příkaz:

   ```shell
   dotnet build
   ```

6. Přejděte do složky `bin\Debug\netcoreapp2.0`.

7. Zadáním následujícího příkazu spusťte program. Nezapomeňte `myConnectionString` nahradit hodnotou, kterou jste předtím získali, a `myQueueName` nahraďte názvem vytvořené fronty:

   ```shell
   dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
   ``` 

8. Sledujte, jak se do fronty odešle deset zpráv a jak se následně z fronty přijmou:

   ![výstup programu](./media/service-bus-quickstart-powershell/dotnet.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Spuštěním následujícího příkazu odeberte skupinu prostředků, obor názvů a všechny související prostředky:

```powershell-interactive
Remove-AzResourceGroup -Name my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Vysvětlení vzorového kódu

Tato část obsahuje další podrobnosti o chování ukázkového kódu. 

### <a name="get-connection-string-and-queue"></a>Získání připojovacího řetězce a fronty

Připojovací řetězec a název fronty jsou předány `Main()` metodě jako argumenty příkazového řádku. Metoda `Main()` deklaruje dvě proměnné řetězce, do kterých se tyto údaje načtou:

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

Metoda MainAsync () vytvoří klienta fronty s argumenty příkazového řádku, zavolá obslužnou rutinu přijímající zprávy s názvem `RegisterOnMessageHandlerAndReceiveMessages()` a odešle sadu zpráv:

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
> Prostředky Service Bus můžete spravovat pomocí [Service Bus Exploreru](https://github.com/paolosalvatori/ServiceBusExplorer/). Service Bus Explorer umožňuje uživatelům připojit se k oboru názvů Service Bus a snadno spravovat entity zasílání zpráv. Tento nástroj poskytuje pokročilé funkce, jako jsou funkce importu a exportu, nebo možnost testovat témata, fronty, odběry, služby Relay, centra oznámení a centra událostí. 

## <a name="next-steps"></a>Další kroky

V tomto článku jste vytvořil obor názvů služby Service Bus a další prostředky potřebné k odesílání a přijímání zpráv z fronty. Další informace o psaní kódu pro posílání a přijímání zpráv najdete v kurzech v části **posílání a přijímání zpráv** . 

> [!div class="nextstepaction"]
> [Odesílání a příjem zpráv](service-bus-dotnet-get-started-with-queues.md)

[bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Instalace a konfigurace Azure PowerShellu]: /powershell/azure/install-Az-ps
