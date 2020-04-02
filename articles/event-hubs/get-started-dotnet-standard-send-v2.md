---
title: Odesílání nebo přijímání událostí z Azure Event Hubs pomocí rozhraní .NET (nejnovější)
description: Tento článek poskytuje návod k vytvoření aplikace .NET Core, která odesílá a přijímá události do/z Centra událostí Azure pomocí nejnovějšího balíčku Azure.Messaging.EventHubs.
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
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 40d291ee17f1fdaf819d70daade735e152df8f71
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548526"
---
# <a name="send-events-to-and-receive-events-from-azure-event-hubs---net-core-azuremessagingeventhubs"></a>Odesílání událostí do a přijímání událostí z Centra událostí Azure – .NET Core (Azure.Messaging.EventHubs) 
Tento rychlý start ukazuje, jak odesílat události do centra událostí a přijímat je z centra událostí pomocí **knihovny Azure.Messaging.EventHubs** .NET Core. 

> [!IMPORTANT]
> Tento rychlý start používá novou knihovnu **Azure.Messaging.EventHubs.** Pro rychlý start, který používá starou knihovnu **Microsoft.Azure.EventHubs,** najdete v tématu [Odesílání a přijímání událostí pomocí knihovny Microsoft.Azure.EventHubs](event-hubs-dotnet-standard-getstarted-send.md). 



## <a name="prerequisites"></a>Požadavky
Pokud s Azure Event Hubs tenete nováčkem, přečtěte si [téma Přehled centra událostí,](event-hubs-about.md) než začnete tento rychlý start. 

Chcete-li tento rychlý start dokončit, potřebujete následující požadavky:

- **Předplatné Microsoft Azure**. Pokud chcete používat služby Azure, včetně Azure Event Hubs, potřebujete předplatné.  Pokud nemáte existující účet Azure, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/) nebo použít výhody předplatitele MSDN při vytváření [účtu](https://azure.microsoft.com).
- **Microsoft Visual Studio 2019**. Klientská knihovna Azure Event Hubs využívá nové funkce, které byly zavedeny v c# 8.0.  Knihovnu můžete stále používat se staršími verzemi jazyka C#, ale některé jeho funkce nebudou k dispozici.  Chcete-li povolit tyto funkce, musíte [cílit na rozhraní .NET Core 3.0](/dotnet/standard/frameworks#how-to-specify-target-frameworks) nebo [zadat jazykovou verzi,](/dotnet/csharp/language-reference/configure-language-version#override-a-default) kterou chcete použít (8.0 nebo vyšší). Pokud používáte Visual Studio, verze před Visual Studio 2019 nejsou kompatibilní s nástroji potřebnými k vytvoření projektů C# 8.0. Visual Studio 2019, včetně bezplatné holce Community Edition, si můžete stáhnout [zde](https://visualstudio.microsoft.com/vs/)
- **Vytvořte obor názvů Event Hubs a centrum událostí**. Prvním krokem je použití [portálu Azure](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Chcete-li vytvořit obor názvů a centrum událostí, postupujte podle postupu v [tomto článku](event-hubs-create.md). Potom získejte **připojovací řetězec pro obor názvů Event Hubs** podle následujících pokynů z článku: [Získat připojovací řetězec](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Připojovací řetězec použijete později v tomto rychlém startu.

## <a name="send-events"></a>Odesílání událostí 
Tato část ukazuje, jak vytvořit konzolovou aplikaci .NET Core pro odesílání událostí do centra událostí. 

### <a name="create-a-console-application"></a>Vytvoření konzolové aplikace

1. Spusťte Visual Studio 2019. 
1. Vyberte **možnost Vytvořit nový projekt**. 
1. V **dialogovém** okně Vytvořit nový projekt proveďte následující kroky: Pokud toto dialogové okno nevidíte, vyberte v nabídce **Soubor,** vyberte **Nový**a pak vyberte **Project**. 
    1. Vyberte **C#** pro programovací jazyk.
    1. Vyberte **konzolu** pro typ aplikace. 
    1. V seznamu výsledků vyberte **Console App (.NET Core).** 
    1. Potom vyberte **Další**. 

        ![Dialogové okno Nový projekt](./media/getstarted-dotnet-standard-send-v2/new-send-project.png)    
1. Zadejte **EventHubsSender** pro název projektu **EventHubsQuickStart** pro název řešení a pak vyberte **OK** pro vytvoření projektu. 

    ![Aplikace C# > Console](./media/getstarted-dotnet-standard-send-v2/project-solution-names.png)

### <a name="add-the-event-hubs-nuget-package"></a>Přidání balíčku NuGet služby Event Hubs

1. V nabídce vyberte **nástroje,** > kterou konzolu Správce balíčků**Správce** > **balíčků** vyberte. 
1. Spusťte následující příkaz k instalaci balíčku **Azure.Messaging.EventHubs** NuGet:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Napsání kódu pro odesílání zpráv do centra událostí

1. Do horní `using` části **Program.cs** souboru přidejte následující příkazy:

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Přidejte konstanty `Program` do třídy pro připojovací řetězec Event Hubs a název centra událostí. Nahraďte zástupné symboly v závorkách správnými hodnotami, které jste získali při vytváření centra událostí. Ujistěte se, že `{Event Hubs namespace connection string}` je připojovací řetězec na úrovni oboru názvů, a ne řetězec centra událostí. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

3. Nahraďte `Main` metodu `async Main` následující metodou. Podrobnosti najdete v komentářích ke kódu. 

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
5. Sestavení projektu a ujistěte se, že neexistují žádné chyby.
6. Spusťte program a počkejte na potvrzovací zprávu. 
7. Na webu Azure Portal můžete ověřit, že centrum událostí přijalo zprávy. Přepněte do zobrazení **Zprávy** v části **Metriky.** Aktualizujte stránku a aktualizujte graf. Může trvat několik sekund, než se zobrazí, že zprávy byly přijaty. 

    [![Ověřte, zda centrum událostí přijalo zprávy](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Úplný zdrojový kód s dalšími informačními komentáři najdete [v tomto souboru na GitHubu.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample03_PublishAnEventBatch.cs)

## <a name="receive-events"></a>Příjem událostí
Tato část ukazuje, jak napsat konzolovou aplikaci .NET Core, která přijímá zprávy z centra událostí pomocí procesoru událostí. Procesor událostí zjednodušuje příjem událostí z centra událostí správou trvalých kontrolních bodů a paralelních příjemů z těchto center událostí. Procesor událostí je přidružen k určitému centru událostí a skupině spotřebitelů. Přijímá události z více oddílů v centru událostí a předává je delegátovi obslužné rutiny pro zpracování pomocí kódu, který zadáte. 


> [!NOTE]
> Pokud používáte azure zásobníku, tato platforma může podporovat jinou verzi sady Storage Blob SDK než ty, které jsou obvykle k dispozici v Azure. Například pokud používáte [ve verzi Azure Stack Hub 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), nejvyšší dostupná verze pro službu Storage je verze 2017-11-09. V takovém případě kromě následujících kroků v této části budete muset také přidat kód pro cílrozhraní API služby úložiště verze 2017-11-09. Příklad, jak cílit na konkrétní verzi rozhraní API úložiště, najdete [v této ukázce na GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample10_RunningWithDifferentStorageVersion.cs). Další informace o verzích služeb Azure Storage, které jsou podporované v centru Azure Stack Hub, najdete v centru [Azure Stack Hub: Rozdíly a důležité informace](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences).

### <a name="create-an-azure-storage-and-a-blob-container"></a>Vytvoření úložiště Azure a kontejneru objektů blob
V tomto rychlém startu použijete Azure Storage jako úložiště kontrolních bodů. Podle těchto kroků vytvořte účet Azure Storage. 

1. [Vytvoření účtu Azure Storage](/azure/storage/common/storage-account-create?tabs=azure-portal)
2. [Vytvoření kontejneru objektů blob](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Získání připojovacího řetězce k účtu úložiště](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Poznamenejte si připojovací řetězec a název kontejneru. Použijete je v přijídač. 


### <a name="create-a-project-for-the-receiver"></a>Vytvoření projektu pro příjemce

1. V okně Průzkumník řešení klepněte pravým tlačítkem myši na řešení **EventHubQuickStart,** přejděte na **Přidat**a vyberte **nový projekt**. 
1. Vyberte **Console App (.NET Core)** a vyberte **Další**. 
1. Zadejte **EventHubsReceiver** pro **název projektu**a vyberte **Vytvořit**. 

### <a name="add-the-event-hubs-nuget-package"></a>Přidání balíčku NuGet služby Event Hubs

1. V nabídce vyberte **nástroje,** > kterou konzolu Správce balíčků**Správce** > **balíčků** vyberte. 
1. Spusťte následující příkaz k instalaci balíčku **Azure.Messaging.EventHubs** NuGet:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Spusťte následující příkaz k instalaci balíčku **NuGet služby Azure.Messaging.EventHubs.Processor:**

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>Aktualizace hlavní metody 

1. V horní `using` části **souboru Program.cs** přidejte následující příkazy.

    ```csharp
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Přidejte konstanty `Program` do třídy pro připojovací řetězec Event Hubs a název centra událostí. Nahraďte zástupné symboly v závorkách správnými hodnotami, které jste získali při vytváření centra událostí. Nahraďte zástupné symboly v závorkách správnými hodnotami, které jste získali při vytváření centra událostí a účtu úložiště (přístupové klíče - primární připojovací řetězec). Ujistěte se, že `{Event Hubs namespace connection string}` je připojovací řetězec na úrovni oboru názvů, a ne řetězec centra událostí.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. Nahraďte `Main` metodu `async Main` následující metodou. Podrobnosti najdete v komentářích ke kódu. 

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
1. Nyní přidejte do třídy následující metody obslužné rutiny událostí a chyb. 

    ```csharp
        static Task ProcessEventHandler(ProcessEventArgs eventArgs)
        { 
            // Write the body of the event to the console window
            Console.WriteLine("\tReceived event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray())); 
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
1. Sestavení projektu a ujistěte se, že neexistují žádné chyby.

    > [!NOTE]
    > Úplný zdrojový kód s dalšími informačními komentáři najdete [v tomto souboru na GitHubu](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.cs).
6. Spusťte aplikaci přijímače. 
1. Měla by se zobrazit zpráva, že událost byla přijata. 

    ![Přijatá událost](./media/getstarted-dotnet-standard-send-v2/event-received.png)

    Tyto události jsou tři události, které jste odeslali do centra událostí dříve spuštěním programu odesílatele. 


## <a name="next-steps"></a>Další kroky
Podívejte se na ukázky na GitHubu. 

- [Ukázky centra událostí na GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Ukázky procesoru událostí na GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Ukázka řízení přístupu na základě rolí (RBAC)](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
