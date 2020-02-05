---
title: Posílání nebo přijímání událostí z Azure Event Hubs pomocí rozhraní .NET (nejnovější)
description: Tento článek popisuje postup vytvoření aplikace .NET Core, která odesílá a přijímá události z Azure Event Hubs pomocí nejnovějšího balíčku Azure. Messaging. EventHubs.
services: event-hubs
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: c8c6e2741eeeadf2afc0c027da8f9cf957c29c95
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77023238"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs---net-core-azuremessagingeventhubs"></a>Odesílání událostí do nebo příjem událostí z Azure Event Hubs – .NET Core (Azure. Messaging. EventHubs) 
Event Hubs je služba, která zpracovává velké objemy dat událostí (telemetrie) z připojených zařízení a aplikací. Po shromáždění dat do Event Hubs můžete data ukládat pomocí clusteru úložiště nebo událostí procesu. Můžete například transformovat data událostí pomocí poskytovatele analýz v reálném čase. Tato rozsáhlá funkce shromažďování a zpracování událostí je klíčovou součástí moderních architektur aplikace, včetně Internet věcí (IoT). Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

V tomto kurzu se dozvíte, jak odesílat události do a přijímat události z centra událostí pomocí .NET Core SDK Event Hubs. 

> [!IMPORTANT]
> V tomto rychlém startu se používá nová knihovna **Azure. Messaging. EventHubs** . Pro rychlý Start, který používá starou knihovnu **Microsoft. Azure. EventHubs** , najdete další informace v [tomto článku](event-hubs-dotnet-standard-getstarted-send.md). 

## <a name="prerequisites"></a>Požadavky

- **Microsoft Azure předplatné**. Pokud chcete používat služby Azure, včetně Azure Event Hubs, potřebujete předplatné.  Pokud nemáte existující účet Azure, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/) nebo využít výhody pro předplatitele MSDN při [vytváření účtu](https://azure.microsoft.com).
- **Microsoft Visual Studio 2019**. Klientská knihovna Azure Event Hubs využívá nové funkce, které byly představeny v C# 8,0.  Knihovnu můžete dál používat se staršími verzemi nástroje C#, ale některé z jejích funkcí nebudou dostupné.  Pokud chcete tyto funkce povolit, musíte [cílit na .NET Core 3,0](/dotnet/standard/frameworks#how-to-specify-target-frameworks) nebo [zadat jazykovou verzi](/dotnet/csharp/language-reference/configure-language-version#override-a-default) , kterou chcete použít (8,0 nebo vyšší). Pokud používáte Visual Studio, verze před sadou Visual Studio 2019 nejsou kompatibilní s nástroji potřebnými pro sestavení C# projektů 8,0. Visual Studio 2019, včetně bezplatné komunity, si můžete stáhnout [tady](https://visualstudio.microsoft.com/vs/) .
- **Vytvoří obor názvů Event Hubs a centrum událostí**. Prvním krokem je použití webu [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centra událostí, postupujte podle pokynů v [v tomto článku](event-hubs-create.md). Pak Získejte **připojovací řetězec pro obor názvů Event Hubs** podle pokynů uvedených v článku [získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Připojovací řetězec použijete později v tomto kurzu.

## <a name="send-events"></a>Odesílání událostí 
V této části se dozvíte, jak vytvořit konzolovou aplikaci .NET Core pro odesílání událostí do centra událostí. 

### <a name="create-a-console-application"></a>Vytvoření konzolové aplikace

1. Spusťte Visual Studio 2019. 
1. Vyberte **vytvořit nový projekt**. 
1. V dialogovém okně **vytvořit nový projekt** proveďte následující kroky: Pokud toto dialogové okno nevidíte, vyberte v nabídce **soubor** , vyberte možnost **Nový**a pak vyberte možnost **projekt**. 
    1. Vyberte **C#** programovací jazyk.
    1. Jako typ aplikace vyberte **Konzola** . 
    1. V seznamu výsledků vyberte **Konzolová aplikace (.NET Core)** . 
    1. Pak vyberte **Next** (Další). 

        ![Dialogové okno Nový projekt](./media/getstarted-dotnet-standard-send-v2/new-send-project.png)    
1. Jako název projektu zadejte **EventHubsSender** , **EventHubsQuickStart** pro název řešení a pak kliknutím na **OK** vytvořte projekt. 

    ![C#> Konzolová aplikace](./media/getstarted-dotnet-standard-send-v2/project-solution-names.png)

### <a name="add-the-event-hubs-nuget-package"></a>Přidání balíčku NuGet služby Event Hubs

1. V nabídce vyberte **nástroje** > **správce balíčků NuGet** > **Konzola správce balíčků** . 
1. Spuštěním následujícího příkazu nainstalujte balíček NuGet **Azure. Messaging. EventHubs** :

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Napsání kódu pro odesílání zpráv do centra událostí

1. Do horní části souboru **program.cs** přidejte následující příkazy `using`:

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Přidejte konstanty do třídy `Program` pro připojovací řetězec Event Hubs a název centra událostí. Zástupné symboly v závorkách nahraďte odpovídajícími hodnotami, které jste získali při vytváření centra událostí. Ujistěte se, že `{Event Hubs namespace connection string}` je připojovací řetězec na úrovni oboru názvů, a ne řetězec centra událostí. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

3. Metodu `Main` nahraďte následující `async Main` metodou. Podrobnosti najdete v komentářích ke kódu. 

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

    [![ověřte, že centrum událostí přijalo zprávy.](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Úplný zdrojový kód s dalšími informativními komentáři najdete v [tomto souboru na GitHubu](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample03_PublishAnEventBatch.cs) .

## <a name="receive-events"></a>Příjem událostí
V této části se dozvíte, jak napsat konzolovou aplikaci .NET Core, která přijímá zprávy z centra událostí pomocí procesoru událostí. Procesor událostí zjednodušuje přijímání událostí z Center událostí tím, že spravuje trvalé kontrolní body a paralelní příjem z těchto Center událostí. Procesor událostí je přidružený ke konkrétnímu centru událostí a skupině uživatelů. Přijímá události z více oddílů v centru událostí a předá je delegátovi obslužné rutiny pro zpracování pomocí kódu, který poskytnete. 


### <a name="create-an-azure-storage-and-a-blob-container"></a>Vytvoření Azure Storage a kontejneru objektů BLOB
V tomto rychlém startu použijete Azure Storage jako úložiště kontrolního bodu. Pomocí těchto kroků vytvořte účet Azure Storage. 

1. [Vytvoření účtu Azure Storage](/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [Vytvoření kontejneru objektů BLOB](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Získání připojovacího řetězce k účtu úložiště](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Poznamenejte si připojovací řetězec a název kontejneru. Budete je používat v kódu příjmu. 


### <a name="create-a-project-for-the-receiver"></a>Vytvořit projekt pro příjemce

1. V okně Průzkumník řešení klikněte pravým tlačítkem na řešení **EventHubQuickStart** , přejděte na **Přidat**a vyberte **Nový projekt**. 
1. Vyberte **aplikace konzoly (.NET Core)** a pak vyberte **Další**. 
1. Event **EventHubsReceiver** pro **název projektu**a vyberte **vytvořit**. 

### <a name="add-the-event-hubs-nuget-package"></a>Přidání balíčku NuGet služby Event Hubs

1. V nabídce vyberte **nástroje** > **správce balíčků NuGet** > **Konzola správce balíčků** . 
1. Spuštěním následujícího příkazu nainstalujte balíček NuGet **Azure. Messaging. EventHubs** :

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Spuštěním následujícího příkazu nainstalujte balíček NuGet **Azure. Messaging. EventHubs. Processor** :

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>Aktualizace metody Main 

1. Do horní části souboru **program.cs** přidejte následující příkazy `using`.

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Přidejte konstanty do třídy `Program` pro připojovací řetězec Event Hubs a název centra událostí. Zástupné symboly v závorkách nahraďte odpovídajícími hodnotami, které jste získali při vytváření centra událostí. Zástupné symboly v závorkách nahraďte odpovídajícími hodnotami, které jste získali při vytváření centra událostí a účtu úložiště (přístupové klíče – primární připojovací řetězec). Ujistěte se, že `{Event Hubs namespace connection string}` je připojovací řetězec na úrovni oboru názvů, a ne řetězec centra událostí.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. Metodu `Main` nahraďte následující `async Main` metodou. Podrobnosti najdete v komentářích ke kódu. 

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
        static Task ProcessEventHandler(ProcessEventArgs eventArgs)
        { 
            // Write the body of the event to the console window
            Console.WriteLine("\tRecevied event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray())); 
            return Task.CompletedTask; 
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

- [Ukázky služby Event Hubs na Githubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Ukázky procesoru událostí na GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
