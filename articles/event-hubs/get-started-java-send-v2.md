---
title: Odesílání nebo přijímání událostí z Azure Event Hubs pomocí Javy (nejnovější)
description: Tento článek obsahuje návod k vytvoření aplikace Java, která odesílá a přijímá události do/z Azure Event Hubs pomocí nejnovějšího balíčku azure-messaging eventhubs.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 04/21/2020
ms.author: spelluru
ms.openlocfilehash: ca22f4481750abb3bd4432c8b42fbce93ede8ffd
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770880"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Použití jazyka Java k odesílání událostí nebo přijímání událostí z Azure Event Hubs (azure-messaging eventhubs)
Tento rychlý start ukazuje, jak odesílat události do centra událostí a přijímat je z centra událostí pomocí balíčku **Java azure-messaging eventhubs.**

> [!IMPORTANT]
> Tento rychlý start používá nový balíček **azure-messaging eventhubs.** Pro rychlý start, který používá staré **balíčky azure-eventhubs** a **azure-eventhubs-eph,** najdete v tématu [Odesílání a přijímání událostí pomocí azure-eventhubs a azure-eventhubs-eph](event-hubs-java-get-started-send.md). 


## <a name="prerequisites"></a>Požadavky
Pokud s Azure Event Hubs tenete nováčkem, přečtěte si [téma Přehled centra událostí,](event-hubs-about.md) než začnete tento rychlý start. 

Chcete-li tento rychlý start dokončit, potřebujete následující požadavky:

- **Předplatné Microsoft Azure**. Pokud chcete používat služby Azure, včetně Azure Event Hubs, potřebujete předplatné.  Pokud nemáte existující účet Azure, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/) nebo použít výhody předplatitele MSDN při vytváření [účtu](https://azure.microsoft.com).
- Vývojové prostředí Javy. Tento rychlý start používá [Eclipse](https://www.eclipse.org/). Java Development Kit (JDK) s verzí 8 nebo vyšší je vyžadována. 
- **Vytvořte obor názvů Event Hubs a centrum událostí**. Prvním krokem je použití [portálu Azure](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Chcete-li vytvořit obor názvů a centrum událostí, postupujte podle postupu v [tomto článku](event-hubs-create.md). Potom získejte **připojovací řetězec pro obor názvů Event Hubs** podle následujících pokynů z článku: [Získat připojovací řetězec](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Připojovací řetězec použijete později v tomto rychlém startu.

## <a name="send-events"></a>Odesílání událostí 
V této části se zobrazí, jak vytvořit java aplikaci pro odesílání událostí z centra událostí. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Přidání odkazu na knihovnu Azure Event Hubs

Knihovna klientů Java pro centra událostí je k dispozici v [centrálním úložišti Maven](https://search.maven.org/search?q=a:azure-messaging-eventhubs). Na tuto knihovnu můžete odkazovat pomocí následující deklarace závislostí uvnitř souboru projektu Maven:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Napsání kódu pro odesílání zpráv do centra událostí

Pro následující příklad nejprve vytvořte nový projekt Maven pro aplikaci konzoly nebo prostředí v oblíbeném vývojovém prostředí Java. Přidejte třídu s názvem `Sender`a přidejte do ní následující kód:

```java
import com.azure.messaging.eventhubs.*;
import static java.nio.charset.StandardCharsets.UTF_8;

public class Sender {
       public static void main(String[] args) {
    }
}
```

### <a name="connection-string-and-event-hub"></a>Připojovací řetězec a centrum událostí
Tento kód používá připojovací řetězec do oboru názvů Event Hubs a název centra událostí k vytvoření klienta Event Hubs. 

```java
String connectionString = "<CONNECTION STRING to EVENT HUBS NAMESPACE>";
String eventHubName = "<EVENT HUB NAME>";
```

### <a name="create-an-event-hubs-producer-client"></a>Vytvoření klienta výrobce centra událostí 
Tento kód vytvoří objekt klienta výrobce, který se používá k výrobě nebo odesílání událostí do centra událostí. 

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

### <a name="send-the-batch-of-events-to-the-event-hub"></a>Odeslat dávku událostí do centra událostí
Tento kód odešle dávku událostí, které jste připravili v předchozím kroku do centra událostí. Následující kód blokuje na operaci odeslání. 

```java
producer.send(batch);
```

### <a name="close-and-cleanup"></a>Zavřít a vyčistit
Tento kód zavře výrobce. 

```java
producer.close();
```
### <a name="complete-code-to-send-events"></a>Kompletní kód pro odesílání událostí
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

Sestavení programu a ujistěte se, že neexistují žádné chyby. Tento program spustíte po spuštění programu přijímače. 

## <a name="receive-events"></a>Příjem událostí
Kód v tomto kurzu je založen na [ukázku EventProcessorClient na GitHubu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/EventProcessorClientSample.java), který můžete prozkoumat zobrazit úplné pracovní aplikace.

> [!NOTE]
> Pokud používáte azure zásobníku, tato platforma může podporovat jinou verzi sady Storage Blob SDK než ty, které jsou obvykle k dispozici v Azure. Například pokud používáte [ve verzi Azure Stack Hub 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), nejvyšší dostupná verze pro službu Storage je verze 2017-11-09. V takovém případě kromě následujících kroků v této části budete muset také přidat kód pro cílrozhraní API služby úložiště verze 2017-11-09. Příklad, jak cílit na konkrétní verzi rozhraní API úložiště, najdete [v této ukázce na GitHubu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). Další informace o verzích služeb Azure Storage, které jsou podporované v centru Azure Stack Hub, najdete v centru [Azure Stack Hub: Rozdíly a důležité informace](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences).

### <a name="create-a-java-project"></a>Vytvoření projektu Java

Klientská knihovna Java pro centra událostí je k dispozici pro použití v projektech Maven z [centrálního úložiště Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)a může být odkazována pomocí následující deklarace závislostí uvnitř souboru projektu Maven: 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.0.1</version>
    </dependency>
</dependencies>
```

1. Použijte následující kód k vytvoření nové třídy s názvem `Receiver`. Nahraďte zástupné symboly hodnotami použitými při vytváření centra událostí a účtu úložiště:
   
   ```java
     import com.azure.messaging.eventhubs.*;
     import com.azure.messaging.eventhubs.models.ErrorContext;
     import com.azure.messaging.eventhubs.models.EventContext;
     import java.util.concurrent.TimeUnit;
     import java.util.function.Consumer;
    
     public class Receiver {
    
         final static String connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
         final static String eventHubName = "<EVENT HUB NAME>";
         
         public static void main(String[] args) throws Exception {
    
             // function to process events
             Consumer<EventContext> processEvent = eventContext  -> {
                 System.out.print("Received event: ");
                 // print the body of the event
                 System.out.println(eventContext.getEventData().getBodyAsString());
                 eventContext.updateCheckpoint();
             };
    
             // function to process errors
             Consumer<ErrorContext> processError = errorContext -> {
                 // print the error message
                 System.out.println(errorContext.getThrowable().getMessage());
             };
    
            
             EventProcessorClient eventProcessorClient = new EventProcessorClientBuilder()
                     .connectionString(connectionString, eventHubName)
                     .processEvent(processEvent)
                     .processError(processError)
                     .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
                     .checkpointStore(new SampleCheckpointStore())
                     .buildEventProcessorClient();
    
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
    
2. Stáhněte si soubor **SampleCheckpointStore.java** z [GitHubu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/SampleCheckpointStore.java)a přidejte ho do projektu. 
3. Sestavení programu a ujistěte se, že neexistují žádné chyby. 

## <a name="run-the-applications"></a>Spuštění aplikací
1. Nejprve spusťte aplikaci **přijímače.**
1. Potom spusťte aplikaci **odesílatele.** 
1. V okně aplikace **příjemce** potvrďte, že se zobrazí události, které byly publikovány aplikací odesílatele.
1. Stisknutím **klávesy ENTER** v okně aplikace přijímače aplikaci zastavíte. 

## <a name="next-steps"></a>Další kroky
Podívejte se [na ukázky sady Java SDK na GitHubu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)

