---
title: Posílání nebo přijímání událostí z Azure Event Hubs pomocí rozhraní .NET (nejnovější)
description: Tento článek popisuje postup vytvoření aplikace .NET Core, která odesílá a přijímá události z Azure Event Hubs pomocí nejnovějšího balíčku Azure. Messaging. EventHubs.
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 1c0f5a5fb45ee45cb9b7e399dc39ad29406b15dc
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578990"
---
# <a name="send-events-to-and-receive-events-from-azure-event-hubs---net-azuremessagingeventhubs"></a>Odesílání událostí do a příjem událostí z Azure Event Hubs – .NET (Azure. Messaging. EventHubs) 
V tomto rychlém startu se dozvíte, jak odesílat události do centra událostí a přijímat z něj události pomocí knihovny .NET **Azure. Messaging. EventHubs** . 

> [!IMPORTANT]
> V tomto rychlém startu se používá nová knihovna **Azure. Messaging. EventHubs** . Pro rychlý Start, který používá starou knihovnu **Microsoft. Azure. EventHubs** , najdete informace v tématu [posílání a přijímání událostí pomocí Microsoft. Azure. EventHubs Library](event-hubs-dotnet-standard-get-started-send-legacy.md). 



## <a name="prerequisites"></a>Požadavky
Pokud s Azure Event Hubs teprve začínáte, přečtěte si téma [přehled Event Hubs](event-hubs-about.md) před provedením tohoto rychlého startu. 

K dokončení tohoto rychlého startu potřebujete následující požadavky:

- **Microsoft Azure předplatné**. Pokud chcete používat služby Azure, včetně Azure Event Hubs, potřebujete předplatné.  Pokud nemáte existující účet Azure, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/) nebo využít výhody pro předplatitele MSDN při [vytváření účtu](https://azure.microsoft.com).
- **Microsoft Visual Studio 2019**. Klientská knihovna Azure Event Hubs využívá nové funkce, které byly představeny v C# 8,0.  Knihovnu můžete dál používat s předchozími jazykovými verzemi jazyka C#, ale nová syntaxe nebude k dispozici. Chcete-li použít úplnou syntaxi, je doporučeno kompilovat s [.NET Core SDK](https://dotnet.microsoft.com/download) 3,0 nebo vyšší a [jazykové verze](/dotnet/csharp/language-reference/configure-language-version#override-a-default) nastavenou na `latest` . Pokud používáte Visual Studio, verze před sadou Visual Studio 2019 nejsou kompatibilní s nástroji potřebnými pro sestavení projektů v jazyce C# 8,0. Do [této](https://visualstudio.microsoft.com/vs/)části si můžete stáhnout Visual Studio 2019, včetně bezplatné edice Community.
- **Vytvoří obor názvů Event Hubs a centrum událostí**. Prvním krokem je použití [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v [tomto článku](event-hubs-create.md). Pak Získejte **připojovací řetězec pro obor názvů Event Hubs** podle pokynů uvedených v článku [získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Připojovací řetězec použijete později v tomto rychlém startu.

## <a name="send-events"></a>Odesílání událostí 
V této části se dozvíte, jak vytvořit konzolovou aplikaci .NET Core pro odesílání událostí do centra událostí. 

### <a name="create-a-console-application"></a>Vytvoření konzolové aplikace

1. Spusťte Visual Studio 2019. 
1. Vyberte **vytvořit nový projekt**. 
1. V dialogovém okně **vytvořit nový projekt** proveďte následující kroky: Pokud toto dialogové okno nevidíte, vyberte v nabídce **soubor** , vyberte možnost **Nový** a pak vyberte možnost **projekt**. 
    1. Pro programovací jazyk vyberte **C#** .
    1. Jako typ aplikace vyberte **Konzola** . 
    1. V seznamu výsledků vyberte **Konzolová aplikace (.NET Core)** . 
    1. Pak vyberte **Další**. 

        ![Dialogové okno Nový projekt](./media/getstarted-dotnet-standard-send-v2/new-send-project.png)    
1. Jako název projektu zadejte **EventHubsSender** , **EventHubsQuickStart** pro název řešení a pak kliknutím na **OK** vytvořte projekt. 

    ![Konzolová aplikace > C#](./media/getstarted-dotnet-standard-send-v2/project-solution-names.png)

### <a name="add-the-event-hubs-nuget-package"></a>Přidání balíčku NuGet služby Event Hubs

1. V nabídce vyberte **nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků** . 
1. Spuštěním následujícího příkazu nainstalujte balíček NuGet **Azure. Messaging. EventHubs** :

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Napsání kódu pro odesílání zpráv do centra událostí

1. `using`Do horní části souboru **program.cs** přidejte následující příkazy:

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Do třídy přidejte konstanty `Program` pro připojovací řetězec Event Hubs a název centra událostí. Zástupné symboly v závorkách nahraďte odpovídajícími hodnotami, které jste získali při vytváření centra událostí. Ujistěte se, že `{Event Hubs namespace connection string}` je připojovací řetězec na úrovni oboru názvů, a ne řetězec centra událostí. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

3. Nahraďte `Main` metodu následující `async Main` metodou. Podrobnosti najdete v komentářích ke kódu. 

    ```csharp
        static async Task Main()
        {
            // Create a producer client that you can use to send events to an event hub
            await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
            {
                // Create a batch of events 
                using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

                // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Third event")));

                // Use the producer client to send the batch of events to the event hub
                await producerClient.SendAsync(eventBatch);
                Console.WriteLine("A batch of 3 events has been published.");
            }
        }
    ```
5. Sestavte projekt a ujistěte se, že nejsou k dispozici žádné chyby.
6. Spusťte program a počkejte na potvrzovací zprávu. 
7. V Azure Portal můžete ověřit, že centrum událostí přijalo zprávy. Přepněte do zobrazení **zprávy** v části **metriky** . Aktualizujte stránku, aby se graf aktualizoval. Může trvat několik sekund, než se zobrazí zpráva, že byly přijaty zprávy. 

    [![Ověřte, že centrum událostí přijalo zprávy.](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Úplný zdrojový kód s dalšími informativními komentáři najdete v [tomto souboru na GitHubu](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md) .

## <a name="receive-events"></a>Příjem událostí
V této části se dozvíte, jak napsat konzolovou aplikaci .NET Core, která přijímá zprávy z centra událostí pomocí procesoru událostí. Procesor událostí zjednodušuje přijímání událostí z Center událostí tím, že spravuje trvalé kontrolní body a paralelní příjemy z těchto Center událostí. Procesor událostí je přidružený ke konkrétnímu centru událostí a skupině uživatelů. Přijímá události z více oddílů v centru událostí a předá je delegátovi obslužné rutiny pro zpracování pomocí kódu, který poskytnete. 


> [!WARNING]
> Pokud spustíte tento kód v Azure Stackovém centru, dojde k chybám za běhu, pokud necílíte na konkrétní verzi rozhraní API úložiště. Důvodem je, že sada Event Hubs SDK používá nejnovější dostupné rozhraní API Azure Storage dostupné v Azure, které nemusí být k dispozici na vaší platformě služby Azure Stack hub. Centrum Azure Stack může podporovat jinou verzi sady SDK pro úložiště objektů blob, než jsou ta, která jsou běžně dostupná v Azure. Pokud jako úložiště kontrolního bodu používáte Azure blogu Storage, podívejte se na [podporovanou verzi rozhraní API Azure Storage pro sestavení centra Azure Stack](/azure-stack/user/azure-stack-acs-differences?#api-version) a cílení na verzi v kódu. 
>
> Pokud například používáte v Azure Stack centra verze 2005, nejvyšší dostupná verze služby úložiště je verze 2019-02-02. Ve výchozím nastavení používá Klientská knihovna Event Hubs SDK nejvyšší dostupnou verzi v Azure (2019-07-07 v době vydání sady SDK). V takovém případě, kromě kroků v této části, budete také muset přidat kód pro cílení na rozhraní API služby úložiště verze 2019-02-02. Příklad cílení na konkrétní verzi rozhraní API úložiště najdete v [této ukázce na GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs). 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>Vytvoření Azure Storage a kontejneru objektů BLOB
V tomto rychlém startu použijete Azure Storage jako úložiště kontrolního bodu. Pomocí těchto kroků vytvořte účet Azure Storage. 

1. [Vytvoření účtu Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Vytvoření kontejneru objektů blob](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Získání připojovacího řetězce k účtu úložiště](../storage/common/storage-configure-connection-string.md)

    Poznamenejte si připojovací řetězec a název kontejneru. Budete je používat v kódu příjmu. 


### <a name="create-a-project-for-the-receiver"></a>Vytvořit projekt pro příjemce

1. V okně Průzkumník řešení klikněte pravým tlačítkem na řešení **EventHubQuickStart** , přejděte na **Přidat** a vyberte **Nový projekt**. 
1. Vyberte **aplikace konzoly (.NET Core)** a pak vyberte **Další**. 
1. Jako **název projektu** zadejte **EventHubsReceiver** a vyberte **vytvořit**. 

### <a name="add-the-event-hubs-nuget-package"></a>Přidání balíčku NuGet služby Event Hubs

1. V nabídce vyberte **nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků** . 
1. Spuštěním následujícího příkazu nainstalujte balíček NuGet **Azure. Messaging. EventHubs** :

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Spuštěním následujícího příkazu nainstalujte balíček NuGet **Azure. Messaging. EventHubs. Processor** :

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>Aktualizace metody Main 

1. Do `using` horní části souboru **program.cs** přidejte následující příkazy.

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Do třídy přidejte konstanty `Program` pro připojovací řetězec Event Hubs a název centra událostí. Zástupné symboly v závorkách nahraďte odpovídajícími hodnotami, které jste získali při vytváření centra událostí. Zástupné symboly v závorkách nahraďte odpovídajícími hodnotami, které jste získali při vytváření centra událostí a účtu úložiště (přístupové klíče – primární připojovací řetězec). Ujistěte se, že `{Event Hubs namespace connection string}` je připojovací řetězec na úrovni oboru názvů, a ne řetězec centra událostí.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. Nahraďte `Main` metodu následující `async Main` metodou. Podrobnosti najdete v komentářích ke kódu. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

            // Stop the processing
            await processor.StopProcessingAsync();
        }    
    ```
1. Nyní do třídy přidejte následující metody obslužné rutiny události a chyby. 

    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Write the body of the event to the console window
            Console.WriteLine("\tRecevied event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));

            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }    
    ```
1. Sestavte projekt a ujistěte se, že nejsou k dispozici žádné chyby.

    > [!NOTE]
    > Úplný zdrojový kód s dalšími informativními komentáři najdete v [tomto souboru na GitHubu](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.cs).
6. Spusťte aplikaci příjemce. 
1. Měla by se zobrazit zpráva, že byla událost přijata. 

    ![Přijatá událost](./media/getstarted-dotnet-standard-send-v2/event-received.png)

    Tyto události jsou tři události, které jste předtím odeslali do centra událostí, spuštěním programu Sender. 


## <a name="next-steps"></a>Další kroky
Podívejte se na ukázky na GitHubu. 

- [Ukázky Event Hubs na GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Ukázky procesoru událostí na GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Ukázka řízení přístupu na základě role v Azure (Azure RBAC)](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
