---
title: Publikování zpráv a zpracování zpráv z dosah technologie Kafka od společnosti prostřednictvím Event Hubs pomocí .NET
description: Tento článek poskytuje návod k vytvoření aplikace .NET Core, která odesílá a přijímá události z témat dosah Apache Atlas Kafka pomocí nejnovějšího balíčku Azure. Messaging. EventHubs.
ms.topic: quickstart
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.date: 04/15/2021
ms.openlocfilehash: 60c177c913c78dbcfcbfe1d465044b69b0e6dd2e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590413"
---
# <a name="publish-messages-to-and-process-messages-from-azure-purviews-atlas-kafka-topics-via-event-hubs-using-net"></a>Publikování zpráv a zpracování zpráv z dosah technologie Kafka od společnosti prostřednictvím Event Hubs pomocí .NET 
V tomto rychlém startu se dozvíte, jak odesílat události do a přijímat události z témat Kafka služby Azure dosah z centra událostí prostřednictvím centra událostí pomocí knihovny **Azure. Messaging. EventHubs** .NET. 

> [!IMPORTANT]
> Spravované centrum událostí se vytváří jako součást vytváření účtů dosah, viz [Vytvoření účtu dosah](create-catalog-portal.md). Můžete publikovat zprávy do ATLAS_HOOK kafkau centra událostí a dosah bude spotřebovávat a zpracovat. Dosah bude upozorňovat na změny entit centra událostí Kafka ATLAS_ENTITIES a uživatel ho může spotřebovat a zpracovat. V tomto rychlém startu se používá nová knihovna **Azure. Messaging. EventHubs** . 


## <a name="prerequisites"></a>Požadavky
Pokud s Azure Event Hubs teprve začínáte, přečtěte si téma [přehled Event Hubs](../event-hubs/event-hubs-about.md) před provedením tohoto rychlého startu. 

K dokončení tohoto rychlého startu potřebujete následující požadavky:

- **Microsoft Azure předplatné**. Pokud chcete používat služby Azure, včetně Azure Event Hubs, potřebujete předplatné.  Pokud nemáte existující účet Azure, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/) nebo využít výhody pro předplatitele MSDN při [vytváření účtu](https://azure.microsoft.com).
- **Microsoft Visual Studio 2019**. Klientská knihovna Azure Event Hubs využívá nové funkce, které byly představeny v C# 8,0.  Knihovnu můžete dál používat s předchozími jazykovými verzemi jazyka C#, ale nová syntaxe nebude k dispozici. Chcete-li použít úplnou syntaxi, je doporučeno kompilovat s [.NET Core SDK](https://dotnet.microsoft.com/download) 3,0 nebo vyšší a [jazykové verze](/dotnet/csharp/language-reference/configure-language-version#override-a-default) nastavenou na `latest` . Pokud používáte Visual Studio, verze před sadou Visual Studio 2019 nejsou kompatibilní s nástroji potřebnými pro sestavení projektů v jazyce C# 8,0. Do [této](https://visualstudio.microsoft.com/vs/)části si můžete stáhnout Visual Studio 2019, včetně bezplatné edice Community.

## <a name="publish-messages-to-purview"></a>Publikování zpráv na dosah 
V této části se dozvíte, jak vytvořit konzolovou aplikaci .NET Core pro odesílání událostí do dosah prostřednictvím tématu Kafka centra událostí **ATLAS_HOOK**. 

## <a name="create-a-visual-studio-project"></a>Vytvoření projektu ve Visual Studiu

Dále vytvořte konzolovou aplikaci C# .NET v aplikaci Visual Studio:

1. Spusťte **Visual Studio**.
2. V okně Start vyberte **vytvořit novou**  >  **konzolovou aplikaci projektu (.NET Framework)**. Vyžaduje se .NET verze 4.5.2 nebo novější.
3. Do **název projektu** zadejte **PurviewKafkaProducer**.
4. Vyberte **Vytvořit** a vytvořte projekt.

### <a name="create-a-console-application"></a>Vytvoření konzolové aplikace

1. Spusťte Visual Studio 2019. 
1. Vyberte **vytvořit nový projekt**. 
1. V dialogovém okně **vytvořit nový projekt** proveďte následující kroky: Pokud toto dialogové okno nevidíte, vyberte v nabídce **soubor** , vyberte možnost **Nový** a pak vyberte možnost **projekt**. 
    1. Pro programovací jazyk vyberte **C#** .
    1. Jako typ aplikace vyberte **Konzola** . 
    1. V seznamu výsledků vyberte **Konzolová aplikace (.NET Core)** . 
    1. Pak vyberte **Další**. 


### <a name="add-the-event-hubs-nuget-package"></a>Přidání balíčku NuGet služby Event Hubs

1. V nabídce vyberte **nástroje**  >  **Správce balíčků NuGet**  >  **Konzola správce balíčků** . 
1. Spuštěním následujícího příkazu nainstalujte balíček NuGet **Azure. Messaging. EventHubs** a balíček NuGet pro **Azure. Messaging. EventHubs. producent** :

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
    
    ```cmd
    Install-Package Azure.Messaging.EventHubs.Producer
    ```    


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Napsání kódu pro odesílání zpráv do centra událostí

1. `using`Do horní části souboru **program. cs** přidejte následující příkazy:

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Do třídy přidejte konstanty `Program` pro připojovací řetězec Event Hubs a název centra událostí. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

    Obor názvů centra událostí, který je přidružený k účtu dosah, můžete získat tak, že na kartě Vlastnosti v účtu dosah vyhledáte primární a sekundární připojovací řetězce Kafka koncového bodu.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Obor názvů centra událostí":::

    Název centra událostí by měl být **ATLAS_HOOK** pro posílání zpráv do dosah.

3. Nahraďte `Main` metodu následující `async Main` metodou a přidejte `async ProduceMessage` do dosah zprávy. Podrobnosti najdete v komentářích ke kódu. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;
            
            / Create an event producer client to add events in the event hub
            EventHubProducerClient producer = new EventHubProducerClient(ehubNamespaceConnectionString, eventHubName);
            
            await ProduceMessage(producer);
        }
        
        static async Task ProduceMessage(EventHubProducerClient producer)
     
        {
            // Create a batch of events 
            using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

            // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<First event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Second event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Third event>")));

            // Use the producer client to send the batch of events to the event hub
            await producerClient.SendAsync(eventBatch);
            Console.WriteLine("A batch of 3 events has been published.");
             
        }
    ```
5. Sestavte projekt a ujistěte se, že nejsou k dispozici žádné chyby.
6. Spusťte program a počkejte na potvrzovací zprávu. 

    > [!NOTE]
    > Úplný zdrojový kód s dalšími informativními komentáři najdete v [tomto souboru na GitHubu](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md) .

### <a name="sample-create-entity-json-message-to-create-a-sql-table-with-two-columns"></a>Ukázka vytvoření zprávy JSON entity pro vytvoření tabulky SQL se dvěma sloupci

```json
    
    {
    "msgCreatedBy": "nayenama",
    "message": {
    "entities": {
    "referredEntities": {
        "-1102395743156037": {
            "typeName": "azure_sql_column",
            "attributes": {
                "owner": null,
                "userTypeId": 61,
                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID",
                "precision": 23,
                "length": 8,
                "description": "Sales Order ID",
                "scale": 3,
                "name": "OrderID",
                "data_type": "int",
                "table": {
                    "guid": "-1102395743156036",
                    "typeName": "azure_sql_table",
                    "entityStatus": "ACTIVE",
                    "displayText": "SalesOrderTable",
                    "uniqueAttributes": {
                                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                    }
            }
            },
            "guid": "-1102395743156037",
            "version": 2
        },
        "-1102395743156038": {
         "typeName": "azure_sql_column",
            "attributes": {
                "owner": null,
                "userTypeId": 61,
                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate",
                "description": "Sales Order Date",
                "scale": 3,
                "name": "OrderDate",
                "data_type": "datetime",
                "table": {
                    "guid": "-1102395743156036",
                    "typeName": "azure_sql_table",
                    "entityStatus": "ACTIVE",
                    "displayText": "SalesOrderTable",
                    "uniqueAttributes": {
                                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                    }
            }
            },
            "guid": "-1102395743156038",
            "status": "ACTIVE",
            "createdBy": "ServiceAdmin",
            "version": 0
        }
        },
        "entity": 
                {
                    "typeName": "azure_sql_table",
                    "attributes": {
                        "owner": "admin",
                        "temporary": false,
                        "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name" : "SalesOrderTable",
                        "description": "Sales Order Table added via Kafka",
                        "columns": [
                            {
                                "guid": "-1102395743156037",
                                "typeName": "azure_sql_column",
                                "uniqueAttributes": {
                                    "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID"
                                }
                            },
                            {
                                "guid": "-1102395743156038",
                                "typeName": "azure_sql_column",
                                "uniqueAttributes": {
                                    "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate"
                                }
                            }
                        ]
                        },
                        "guid": "-1102395743156036",
                    "version": 0                
                    }
                },
        "type": "ENTITY_CREATE_V2",
        "user": "admin"
    },
    "version": {
        "version": "1.0.0"
    },
    "msgCompressionKind": "NONE",
    "msgSplitIdx": 1,
    "msgSplitCount": 1
}

``` 

## <a name="consume-messages-from-purview"></a>Využívání zpráv z dosah
V této části se dozvíte, jak napsat konzolovou aplikaci .NET Core, která přijímá zprávy z centra událostí pomocí procesoru událostí. K přijímání zpráv z dosah je nutné použít centrum událostí ATLAS_ENTITIES. procesor událostí zjednodušuje přijímání událostí z centra událostí tím, že spravuje trvalé kontrolní body a paralelní příjeme z těchto Center událostí. 

> [!WARNING]
> Pokud spustíte tento kód v Azure Stackovém centru, dojde k chybám za běhu, pokud necílíte na konkrétní verzi rozhraní API úložiště. Důvodem je, že sada Event Hubs SDK používá nejnovější dostupné rozhraní API Azure Storage dostupné v Azure, které nemusí být k dispozici na vaší platformě služby Azure Stack hub. Centrum Azure Stack může podporovat jinou verzi sady SDK pro úložiště objektů blob, než jsou ta, která jsou běžně dostupná v Azure. Pokud používáte Azure Blob Storage jako úložiště kontrolního bodu, podívejte se na [podporovanou verzi rozhraní API Azure Storage pro sestavení centra pro Azure Stack](/azure-stack/user/azure-stack-acs-differences?#api-version) a cílení na verzi v kódu. 
>
> Pokud například používáte v Azure Stack centra verze 2005, nejvyšší dostupná verze služby úložiště je verze 2019-02-02. Ve výchozím nastavení používá Klientská knihovna Event Hubs SDK nejvyšší dostupnou verzi v Azure (2019-07-07 v době vydání sady SDK). V takovém případě, kromě kroků v této části, budete také muset přidat kód pro cílení na rozhraní API služby úložiště verze 2019-02-02. Příklad cílení na konkrétní verzi rozhraní API úložiště najdete v [této ukázce na GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>Vytvoření Azure Storage a kontejneru objektů BLOB
V tomto rychlém startu použijete Azure Storage jako úložiště kontrolního bodu. Pomocí těchto kroků vytvořte účet Azure Storage. 

1. [Vytvoření účtu Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Vytvoření kontejneru objektů blob](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Získání připojovacího řetězce k účtu úložiště](../storage/common/storage-configure-connection-string.md)

    Poznamenejte si připojovací řetězec a název kontejneru. Budete je používat v kódu příjmu. 


### <a name="create-a-project-for-the-receiver"></a>Vytvořit projekt pro příjemce

1. V okně Průzkumník řešení klikněte pravým tlačítkem na řešení **EventHubQuickStart** , přejděte na **Přidat** a vyberte **Nový projekt**. 
1. Vyberte **aplikace konzoly (.NET Core)** a pak vyberte **Další**. 
1. Jako **název projektu** zadejte **PurviewKafkaConsumer** a vyberte **vytvořit**. 

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

1. Přidejte následující `using` příkazy do horní části souboru **program. cs** .

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
    
    Obor názvů centra událostí, který je přidružený k účtu dosah, můžete získat tak, že na kartě Vlastnosti v účtu dosah vyhledáte primární a sekundární připojovací řetězce Kafka koncového bodu.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Obor názvů centra událostí":::

    Název centra událostí by měl být **ATLAS_ENTITIES** pro posílání zpráv do dosah.

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
            Console.WriteLine("\tReceived event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));

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
    > Úplný zdrojový kód s dalšími informativními komentáři najdete v [tomto souboru na GitHubu](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.md).
6. Spusťte aplikaci příjemce. 

### <a name="sample-message-received-from-purview"></a>Ukázková zpráva přijatá z dosah

```json
{
    "version":
        {"version":"1.0.0",
         "versionParts":[1]
        },
         "msgCompressionKind":"NONE",
         "msgSplitIdx":1,
         "msgSplitCount":1,
         "msgSourceIP":"10.244.155.5",
         "msgCreatedBy":
         "",
         "msgCreationTime":1618588940869,
         "message":{
            "type":"ENTITY_NOTIFICATION_V2",
            "entity":{
                "typeName":"azure_sql_table",
                    "attributes":{
                        "owner":"admin",
                        "createTime":0,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name":"SalesOrderTable",
                        "description":"Sales Order Table"
                        },
                        "guid":"ead5abc7-00a4-4d81-8432-d5f6f6f60000",
                        "status":"ACTIVE",
                        "displayText":"SalesOrderTable"
                    },
                    "operationType":"ENTITY_UPDATE",
                    "eventTime":1618588940567
                }
}
```

> [!IMPORTANT]
> Atlas aktuálně podporuje následující typy operací: **ENTITY_CREATE_V2**, **ENTITY_PARTIAL_UPDATE_V2**, **ENTITY_FULL_UPDATE_V2** **ENTITY_DELETE_V2**. Doručování zpráv do dosah je aktuálně povoleno ve výchozím nastavení. Pokud scénář zahrnuje čtení z dosah, kontaktujte nás, protože je potřeba, aby byl v seznamu povolený. (zadejte ID předplatného a název účtu dosah).


## <a name="next-steps"></a>Další kroky
Podívejte se na ukázky na GitHubu. 

- [Ukázky Event Hubs na GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Ukázky procesoru událostí na GitHubu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Úvod k oznámením pro Atlas](https://atlas.apache.org/2.0.0/Notifications.html)