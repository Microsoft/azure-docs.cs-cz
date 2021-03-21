---
title: Posílání a přijímání událostí z Azure Event Hubs pomocí Java (nejnovější)
description: Tento článek popisuje postup vytvoření aplikace Java, která odesílá a přijímá události z Azure Event Hubs pomocí nejnovějšího balíčku Azure-Messaging-eventhubs.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 640f6c4dcb223e55e10f7cb5d7daaa44dbd41578
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102172019"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Použití jazyka Java k posílání událostí nebo přijímání událostí z Azure Event Hubs (Azure-zasílání zpráv – eventhubs)
V tomto rychlém startu se dozvíte, jak odesílat události do centra událostí a přijímat z něj události pomocí balíčku Java **-Messaging-eventhubs** Java.

> [!IMPORTANT]
> V tomto rychlém startu se používá nový balíček **Azure-Messaging-eventhubs** . Pro rychlý Start, který používá staré balíčky **Azure-eventhubs** a **Azure-eventhubs-EPH** , najdete informace v tématu [posílání a přijímání událostí pomocí Azure-eventhubs a Azure-eventhubs-EPH](event-hubs-java-get-started-send-legacy.md). 


## <a name="prerequisites"></a>Předpoklady
Pokud s Azure Event Hubs teprve začínáte, přečtěte si téma [přehled Event Hubs](event-hubs-about.md) před provedením tohoto rychlého startu. 

K dokončení tohoto rychlého startu potřebujete následující požadavky:

- **Microsoft Azure předplatné**. Pokud chcete používat služby Azure, včetně Azure Event Hubs, potřebujete předplatné.  Pokud nemáte existující účet Azure, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/) nebo využít výhody pro předplatitele MSDN při [vytváření účtu](https://azure.microsoft.com).
- Java development environment. V tomto rychlém startu se používá [zatmění](https://www.eclipse.org/). Vyžaduje se sada Java Development Kit (JDK) s verzí 8 nebo vyšší. 
- **Vytvoří obor názvů Event Hubs a centrum událostí**. Prvním krokem je použití [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v [tomto článku](event-hubs-create.md). Pak Získejte **připojovací řetězec pro obor názvů Event Hubs** podle pokynů uvedených v článku [získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Připojovací řetězec použijete později v tomto rychlém startu.

## <a name="send-events"></a>Odesílání událostí 
V této části se dozvíte, jak vytvořit aplikaci Java pro posílání událostí do centra událostí. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Přidat odkaz na knihovnu Azure Event Hubs

Klientská knihovna Java pro Event Hubs je k dispozici v [centrálním úložišti Maven](https://search.maven.org/search?q=a:azure-messaging-eventhubs). Na tuto knihovnu můžete odkazovat pomocí následující deklarace závislosti v rámci souboru projektu Maven:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Napsání kódu pro odesílání zpráv do centra událostí

Pro následující příklad nejprve vytvořte nový projekt Maven pro aplikaci konzoly nebo prostředí v oblíbeném vývojovém prostředí Java. Přidejte třídu s názvem `Sender` a do třídy přidejte následující kód:

```java
import com.azure.messaging.eventhubs.*;
import static java.nio.charset.StandardCharsets.UTF_8;

public class Sender {
       public static void main(String[] args) {
    }
}
```

### <a name="connection-string-and-event-hub"></a>Připojovací řetězec a centrum událostí
Tento kód používá připojovací řetězec k oboru názvů Event Hubs a název centra událostí pro sestavení klienta Event Hubs. 

```java
String connectionString = "<CONNECTION STRING to EVENT HUBS NAMESPACE>";
String eventHubName = "<EVENT HUB NAME>";
```

### <a name="create-an-event-hubs-producer-client"></a>Vytvoření klienta Event Hubsho producenta 
Tento kód vytvoří objekt klienta producenta, který se používá k vytvoření nebo odeslání událostí do centra událostí. 

```java
EventHubProducerClient producer = new EventHubClientBuilder()
    .connectionString(connectionString, eventHubName)
    .buildProducerClient();
```

### <a name="prepare-a-batch-of-events"></a>Příprava dávky událostí
Tento kód připraví dávku událostí. 

```java
EventDataBatch batch = producer.createBatch();
batch.tryAdd(new EventData("First event"));
batch.tryAdd(new EventData("Second event"));
batch.tryAdd(new EventData("Third event"));
batch.tryAdd(new EventData("Fourth event"));
batch.tryAdd(new EventData("Fifth event"));
```

### <a name="send-the-batch-of-events-to-the-event-hub"></a>Odeslání dávky událostí do centra událostí
Tento kód pošle dávku událostí, které jste připravili v předchozím kroku, do centra událostí. Následující bloky kódu na operaci odeslání. 

```java
producer.send(batch);
```

### <a name="close-and-cleanup"></a>Zavřít a vyčistit
Tento kód zavírá producenta. 

```java
producer.close();
```
### <a name="complete-code-to-send-events"></a>Dokončení kódu pro odesílání událostí
Tady je kompletní kód pro odesílání událostí do centra událostí. 

```java
import com.azure.messaging.eventhubs.*;

public class Sender {
    public static void main(String[] args) {
        final String connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        final String eventHubName = "EVENT HUB NAME";

        // create a producer using the namespace connection string and event hub name
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // prepare a batch of events to send to the event hub    
        EventDataBatch batch = producer.createBatch();
        batch.tryAdd(new EventData("First event"));
        batch.tryAdd(new EventData("Second event"));
        batch.tryAdd(new EventData("Third event"));
        batch.tryAdd(new EventData("Fourth event"));
        batch.tryAdd(new EventData("Fifth event"));

        // send the batch of events to the event hub
        producer.send(batch);

        // close the producer
        producer.close();
    }
}
```

Sestavte program a zajistěte, aby nedocházelo k chybám. Po spuštění programu přijímače budete tento program spouštět. 

## <a name="receive-events"></a>Příjem událostí
Kód v tomto kurzu je založený na [ukázce EventProcessorClient na GitHubu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorBlobCheckpointStoreSample.java), kterou si můžete prohlédnout, abyste viděli úplnou funkční aplikaci.

> [!WARNING]
> Pokud spustíte tento kód v Azure Stackovém centru, dojde k chybám za běhu, pokud necílíte na konkrétní verzi rozhraní API úložiště. Důvodem je, že sada Event Hubs SDK používá nejnovější dostupné rozhraní API Azure Storage dostupné v Azure, které nemusí být k dispozici na vaší platformě služby Azure Stack hub. Centrum Azure Stack může podporovat jinou verzi sady SDK pro úložiště objektů blob, než jsou ta, která jsou běžně dostupná v Azure. Pokud jako úložiště kontrolního bodu používáte Azure blogu Storage, podívejte se na [podporovanou verzi rozhraní API Azure Storage pro sestavení centra Azure Stack](/azure-stack/user/azure-stack-acs-differences?#api-version) a cílení na verzi v kódu. 
>
> Pokud například používáte v Azure Stack centra verze 2005, nejvyšší dostupná verze služby úložiště je verze 2019-02-02. Ve výchozím nastavení používá Klientská knihovna Event Hubs SDK nejvyšší dostupnou verzi v Azure (2019-07-07 v době vydání sady SDK). V takovém případě, kromě kroků v této části, budete také muset přidat kód pro cílení na rozhraní API služby úložiště verze 2019-02-02. Příklad cílení na konkrétní verzi rozhraní API úložiště najdete v [této ukázce na GitHubu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). 


### <a name="create-an-azure-storage-and-a-blob-container"></a>Vytvoření Azure Storage a kontejneru objektů BLOB
V tomto rychlém startu použijete jako úložiště kontrolního bodu Azure Storage (konkrétně Blob Storage). Kontrolní bod je proces, při kterém procesor událostí označí nebo potvrdí pozici poslední úspěšné zpracovávané události v rámci oddílu. Označení kontrolního bodu se obvykle provádí ve funkci, která události zpracovává. Další informace o vytváření kontrolních bodů najdete v tématu [procesor událostí](event-processor-balance-partition-load.md).

Pomocí těchto kroků vytvořte účet Azure Storage. 

1. [Vytvoření účtu Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Vytvoření kontejneru objektů blob](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Získání připojovacího řetězce k účtu úložiště](../storage/common/storage-configure-connection-string.md)

    Poznamenejte si **připojovací řetězec** a **název kontejneru**. Budete je používat v kódu příjmu. 

### <a name="add-event-hubs-libraries-to-your-java-project"></a>Přidání knihoven Event Hubs do projektu Java
Do souboru pom.xml přidejte následující závislosti. 

- [Azure-zasílání zpráv – eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs)
- [Azure-zasílání zpráv – eventhubs-checkpointstore – objekt BLOB](https://search.maven.org/search?q=a:azure-messaging-eventhubs-checkpointstore-blob)

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.1.1</version>
    </dependency>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs-checkpointstore-blob</artifactId>
        <version>1.5.0</version>
    </dependency>
</dependencies>
```

1. Do horní části souboru Java přidejte následující příkazy pro **Import** . 

    ```java
    import com.azure.messaging.eventhubs.EventHubClientBuilder;
    import com.azure.messaging.eventhubs.EventProcessorClient;
    import com.azure.messaging.eventhubs.EventProcessorClientBuilder;
    import com.azure.messaging.eventhubs.checkpointstore.blob.BlobCheckpointStore;
    import com.azure.messaging.eventhubs.models.ErrorContext;
    import com.azure.messaging.eventhubs.models.EventContext;
    import com.azure.storage.blob.BlobContainerAsyncClient;
    import com.azure.storage.blob.BlobContainerClientBuilder;
    import java.util.function.Consumer;
    import java.util.concurrent.TimeUnit;
    ```
2. Vytvořte třídu s názvem `Receiver` a přidejte do třídy následující řetězcové proměnné. Nahraďte zástupné symboly správnými hodnotami. 
    ```java
    private static final String EH_NAMESPACE_CONNECTION_STRING = "<EVENT HUBS NAMESPACE CONNECTION STRING>";
    private static final String eventHubName = "<EVENT HUB NAME>";
    private static final String STORAGE_CONNECTION_STRING = "<AZURE STORAGE CONNECTION STRING>";
    private static final String STORAGE_CONTAINER_NAME = "<AZURE STORAGE CONTAINER NAME>";
    ```
3. `main`Do třídy přidejte následující metodu. 

    ```java
    public static void main(String[] args) throws Exception {
        // Create a blob container client that you use later to build an event processor client to receive and process events
        BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
            .connectionString(STORAGE_CONNECTION_STRING) 
            .containerName(STORAGE_CONTAINER_NAME) 
            .buildAsyncClient();
    
        // Create a builder object that you will use later to build an event processor client to receive and process events and errors.
        EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
            .connectionString(EH_NAMESPACE_CONNECTION_STRING, eventHubName) 
            .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
            .processEvent(PARTITION_PROCESSOR) 
            .processError(ERROR_HANDLER) 
            .checkpointStore(new BlobCheckpointStore(blobContainerAsyncClient)); 

        // Use the builder object to create an event processor client 
        EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();

        System.out.println("Starting event processor");
        eventProcessorClient.start();

        System.out.println("Press enter to stop.");
        System.in.read();

        System.out.println("Stopping event processor");
        eventProcessorClient.stop();
        System.out.println("Event processor stopped.");

        System.out.println("Exiting process");
    }    
    ```
4. Přidejte dvě pomocné metody ( `PARTITION_PROCESSOR` a `ERROR_HANDLER` ), které zpracovávají události a chyby `Receiver` třídy. 

    ```java
    public static final Consumer<EventContext> PARTITION_PROCESSOR = eventContext -> {
        System.out.printf("Processing event from partition %s with sequence number %d with body: %s %n", 
                eventContext.getPartitionContext().getPartitionId(), eventContext.getEventData().getSequenceNumber(), eventContext.getEventData().getBodyAsString());

        if (eventContext.getEventData().getSequenceNumber() % 10 == 0) {
            eventContext.updateCheckpoint();
        }
    };
    
    public static final Consumer<ErrorContext> ERROR_HANDLER = errorContext -> {
        System.out.printf("Error occurred in partition processor for partition %s, %s.%n",
            errorContext.getPartitionContext().getPartitionId(),
            errorContext.getThrowable());
    };    
    ```
3. Úplný kód by měl vypadat takto: 

    ```java

    import com.azure.messaging.eventhubs.EventHubClientBuilder;
    import com.azure.messaging.eventhubs.EventProcessorClient;
    import com.azure.messaging.eventhubs.EventProcessorClientBuilder;
    import com.azure.messaging.eventhubs.checkpointstore.blob.BlobCheckpointStore;
    import com.azure.messaging.eventhubs.models.ErrorContext;
    import com.azure.messaging.eventhubs.models.EventContext;
    import com.azure.storage.blob.BlobContainerAsyncClient;
    import com.azure.storage.blob.BlobContainerClientBuilder;
    import java.util.function.Consumer;
    import java.util.concurrent.TimeUnit;
    
    public class Receiver {
        
        private static final String EH_NAMESPACE_CONNECTION_STRING = "<EVENT HUBS NAMESPACE CONNECTION STRING>";
        private static final String eventHubName = "<EVENT HUB NAME>";
        private static final String STORAGE_CONNECTION_STRING = "<AZURE STORAGE CONNECTION STRING>";
        private static final String STORAGE_CONTAINER_NAME = "<AZURE STORAGE CONTAINER NAME>";
    
        public static final Consumer<EventContext> PARTITION_PROCESSOR = eventContext -> {
        System.out.printf("Processing event from partition %s with sequence number %d with body: %s %n", 
                eventContext.getPartitionContext().getPartitionId(), eventContext.getEventData().getSequenceNumber(), eventContext.getEventData().getBodyAsString());

            if (eventContext.getEventData().getSequenceNumber() % 10 == 0) {
                eventContext.updateCheckpoint();
            }
        };
        
        public static final Consumer<ErrorContext> ERROR_HANDLER = errorContext -> {
            System.out.printf("Error occurred in partition processor for partition %s, %s.%n",
                errorContext.getPartitionContext().getPartitionId(),
                errorContext.getThrowable());
        };
        
        public static void main(String[] args) throws Exception {
            BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
                .connectionString(STORAGE_CONNECTION_STRING)
                .containerName(STORAGE_CONTAINER_NAME)
                .buildAsyncClient();
    
            EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
                .connectionString(EH_NAMESPACE_CONNECTION_STRING, eventHubName)
                .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
                .processEvent(PARTITION_PROCESSOR)
                .processError(ERROR_HANDLER)
                .checkpointStore(new BlobCheckpointStore(blobContainerAsyncClient));
    
            EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();

            System.out.println("Starting event processor");
            eventProcessorClient.start();
        
            System.out.println("Press enter to stop.");
            System.in.read();
        
            System.out.println("Stopping event processor");
            eventProcessorClient.stop();
            System.out.println("Event processor stopped.");
        
            System.out.println("Exiting process");
        }
        
    }
    ```
3. Sestavte program a zajistěte, aby nedocházelo k chybám. 

## <a name="run-the-applications"></a>Spuštění aplikací
1. Nejdřív spusťte aplikaci **příjemce** .
1. Pak spusťte aplikaci **sender** . 
1. V okně **přijímač** aplikace potvrďte, že vidíte události, které byly publikovány aplikací odesílatele.
1. Stisknutím klávesy **ENTER** v okně přijímače aplikace zastavte aplikaci. 

## <a name="next-steps"></a>Další kroky
Podívejte se na následující ukázky na GitHubu:

- [Ukázky Azure-zasílání zpráv eventhubs](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [ukázky Azure-Messaging-eventhubs-checkpointstore-BLOB](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob)  
