---
title: Kurz – Aktualizace sortimentu zásob v maloobchodě s využitím kanálů pro publikování/odběr a filtrů témat v prostředí Azure PowerShell | Microsoft Docs
description: V tomto kurzu se naučíte, jak odesílat a přijímat zprávy z tématu a odběru a jak přidávat a používat pravidla filtrů pomocí prostředí Azure PowerShell
services: service-bus-messaging
author: spelluru
manager: timlt
ms.author: spelluru
ms.date: 09/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: fdf97234e840de5fdd2811f3cdae8dd4cdc76a22
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51229148"
---
# <a name="tutorial-update-inventory-using-powershell-and-topicssubscriptions"></a>Kurz: Aktualizace zásob pomocí prostředí PowerShell a témat/odběrů

Microsoft Azure Service Bus je víceklientská cloudová služba pro zasílání zpráv, která odesílá informace mezi aplikacemi a službami. Asynchronní operace umožňují flexibilní zprostředkované zasílání zpráv a také strukturované zasílání zpráv typu FIFO (first-in-first-out) a funkce pro publikování a přihlášení k odběru. 

Tento kurz ukazuje, jak odesílat zprávy do fronty Service Bus a přijímat je. Používá se k tomu prostředí PowerShell, kde se vytvoří obor názvů pro zasílání zpráv a v tomto oboru názvů fronta. Dále je třeba získat v oboru názvů přihlašovací údaje pro autorizaci. Postup pak ukazuje, jak do této fronty odesílat zprávy a přijímat je z ní pomocí [knihovny .NET Standard](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus).

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Pomocí prostředí Azure PowerShell vytvořit téma služby Service Bus a k němu jeden nebo více odběrů
> * Přidávat filtry témat pomocí prostředí PowerShell
> * Vytvořit dvě zprávy s různým obsahem
> * Odeslat zprávy a ověřit, že dorazily do očekávaných odběrů
> * Přijímat zprávy z odběrů

V tomto scénáři pracujeme s příkladem aktualizace sortimentu zásob pro několik prodejen. Každá prodejna nebo sada prodejen v našem příkladu přijímá zprávy s pokyny, jak konkrétně má aktualizovat sortiment. Tento kurz vysvětluje, jak tento scénář využívající odběry a filtry implementujete. Nejprve vytvoříte téma se třemi odběry, přidáte pravidla a filtry a potom z tématu a odběrů odešlete zprávy a přijmete je.

![téma](./media/service-bus-tutorial-topics-subscriptions-powershell/about-service-bus-topic.png)

Pokud ještě nemáte předplatné Azure, vytvořte si nejprve [bezplatný účet][].

## <a name="prerequisites"></a>Požadavky

Před tímto kurzem se ujistěte, že máte nainstalované tyto položky:

1. [Visual Studio 2017 s aktualizací Update 3 (verze 15.3, 26730.01)](https://www.visualstudio.com/vs) nebo novější.
2. [NET Core SDK](https://www.microsoft.com/net/download/windows) verze 2.0 nebo novější.

Tento kurz vyžaduje použití nejnovější verze Azure PowerShellu. Pokud PowerShell potřebujete nainstalovat nebo upgradovat, přečtěte si téma [Instalace a konfigurace Azure PowerShellu][].

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pomocí následujících příkazů se přihlaste k Azure. Tyto kroky nejsou potřebné, pokud příkazy PowerShellu spouštíte ve službě Cloud Shell: 

1. Nainstalujte modul PowerShell služby Service Bus:

   ```azurepowershell-interactive
   Install-Module AzureRM.ServiceBus
   ```

2. Spuštěním následujícího příkazu se přihlaste k Azure:

   ```azurepowershell-interactive
   Login-AzureRmAccount
   ```

4. Nastavte aktuální kontext odběru, případně zobrazte momentálně aktivní odběr:

   ```azurepowershell-interactive
   Select-AzureRmSubscription -SubscriptionName "MyAzureSubName" 
   Get-AzureRmContext
   ```

## <a name="provision-resources"></a>Zřízení prostředků

Po přihlášení k Azure pomocí následujících příkazů zřiďte prostředky služby Service Bus. Nezapomeňte všechny zástupné symboly nahradit příslušnými hodnotami:

```azurepowershell-interactive
# Create a resource group 
New-AzureRmResourceGroup –Name my-resourcegroup –Location westus2

# Create a Messaging namespace
New-AzureRmServiceBusNamespace -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Location westus2

# Create a queue 
New-AzureRmServiceBusQueue -ResourceGroupName my-resourcegroup -NamespaceName namespace-name -Name queue-name -EnablePartitioning $False

# Get primary connection string (required in next step)
Get-AzureRmServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
```

Po spuštění rutiny `Get-AzureRmServiceBusKey` zkopírujte připojovací řetězec a název fronty, který jste vybrali, a vložte je do dočasného umístění, třeba do Poznámkového bloku. Budete je potřebovat v dalším kroku.

## <a name="send-and-receive-messages"></a>Odesílání a příjem zpráv

Pokud jste vytvořili obor názvů a frontu a máte potřebná pověření, můžete začít odesílat a přijímat zprávy. Kód si můžete zkontrolovat v [této složce s ukázkami na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted/BasicSendReceiveQuickStart).

Kód spustíte následovně:

1. Pomocí následujícího příkazu naklonujte [úložiště Service Bus na GitHubu](https://github.com/Azure/azure-service-bus/):

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Otevřete příkazový řádek PowerShellu.

3. Přejděte do složky s ukázkou `azure-service-bus\samples\DotNet\GettingStarted\BasicSendReceiveQuickStart\BasicSendReceiveQuickStart`.

4. Pokud jste to ještě neudělali, získejte pomocí následující rutiny PowerShellu připojovací řetězec. Zástupné texty `my-resourcegroup` a `namespace-name` nezapomeňte nahradit svými konkrétními hodnotami: 

   ```azurepowershell-interactive
   Get-AzureRmServiceBusKey -ResourceGroupName my-resourcegroup -Namespace namespace-name -Name RootManageSharedAccessKey
   ```
5.  Do příkazového řádku PowerShellu zadejte následující příkaz:

   ```shell
   dotnet build
   ```
6.  Přejděte do složky `\bin\Debug\netcoreapp2.0`.
7.  Zadáním následujícího příkazu spusťte program. Nezapomeňte `myConnectionString` nahradit hodnotou, kterou jste předtím získali, a `myQueueName` nahraďte názvem vytvořené fronty:

   ```shell
   dotnet BasicSendReceiveQuickStart.dll -ConnectionString "myConnectionString" -QueueName "myQueueName"
   ``` 
8. Sledujte, jak se do fronty odešle deset zpráv a jak se následně z fronty přijmou:

   ![výstup programu](./media/service-bus-quickstart-powershell/dotnet.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Spuštěním následujícího příkazu odeberte skupinu prostředků, obor názvů a všechny související prostředky:

```powershell-interactive
Remove-AzureRmResourceGroup -Name my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Vysvětlení vzorového kódu

Tato část obsahuje další podrobnosti o chování ukázkového kódu. 

### <a name="get-connection-string-and-queue"></a>Získání připojovacího řetězce a fronty

Připojovací řetězec a název fronty se předají metodě `Main()` jako argumenty příkazového řádku. Metoda `Main()` deklaruje dvě proměnné řetězce, do kterých se tyto údaje načtou:

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

Metoda `MainAsync()` vytvoří klienta fronty s argumenty příkazového řádku, zavolá obslužnou rutinu pro příjem zpráv s názvem `RegisterOnMessageHandlerAndReceiveMessages()`a odešle sadu zpráv:

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

Metoda `RegisterOnMessageHandlerAndReceiveMessages()` nastaví některé možnosti obslužné rutiny zpráv a potom zavolá metodu `RegisterMessageHandler()` klienta fronty, která spustí příjem:

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

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zřídili prostředky v prostředí Azure PowerShell a poté odesílali a přijímali zprávy z tématu služby Service Bus a jeho odběrů. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Pomocí portálu Azure Portal vytvořit téma služby Service Bus a k němu jeden nebo více odběrů
> * Přidávat filtry témat pomocí kódu .NET
> * Vytvořit dvě zprávy s různým obsahem
> * Odeslat zprávy a ověřit, že dorazily do očekávaných odběrů
> * Přijímat zprávy z odběrů

Pokud vás zajímají další příklady odesílání a přijímání zpráv, pomůžou vám začít [ukázky služby Service Bus na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Přejděte k dalšímu kurzu, kde se dozvíte více o možnostech publikování a přihlášení k odběru ve službě Service Bus.

> [!div class="nextstepaction"]
> [Aktualizace zásob pomocí prostředí PowerShell a témat/odběrů](service-bus-tutorial-topics-subscriptions-cli.md)

[bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[Instalace a konfigurace Azure PowerShellu]: /powershell/azure/install-azurerm-ps