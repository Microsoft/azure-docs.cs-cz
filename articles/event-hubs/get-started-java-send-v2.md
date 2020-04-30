---
title: Posílání a přijímání událostí z Azure Event Hubs pomocí Java (nejnovější)
description: Tento článek popisuje postup vytvoření aplikace Java, která odesílá a přijímá události z Azure Event Hubs pomocí nejnovějšího balíčku Azure-Messaging-eventhubs.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 04/21/2020
ms.author: spelluru
ms.openlocfilehash: ca22f4481750abb3bd4432c8b42fbce93ede8ffd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81770880"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Použití jazyka Java k posílání událostí nebo přijímání událostí z Azure Event Hubs (Azure-zasílání zpráv – eventhubs)
V tomto rychlém startu se dozvíte, jak odesílat události do centra událostí a přijímat z něj události pomocí balíčku Java **-Messaging-eventhubs** Java.

> [!IMPORTANT]
> V tomto rychlém startu se používá nový balíček **Azure-Messaging-eventhubs** . Pro rychlý Start, který používá staré balíčky **Azure-eventhubs** a **Azure-eventhubs-EPH** , najdete informace v tématu [posílání a přijímání událostí pomocí Azure-eventhubs a Azure-eventhubs-EPH](event-hubs-java-get-started-send.md). 


## <a name="prerequisites"></a>Požadavky
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

Pro následující příklad nejprve vytvořte nový projekt Maven pro aplikaci konzoly nebo prostředí v oblíbeném vývojovém prostředí Java. Přidejte třídu s názvem `Sender`a do třídy přidejte následující kód:

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
Kód v tomto kurzu je založený na [ukázce EventProcessorClient na GitHubu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/EventProcessorClientSample.java), kterou si můžete prohlédnout, abyste viděli úplnou funkční aplikaci.

> [!NOTE]
> Pokud používáte centrum Azure Stack, může tato platforma podporovat jinou verzi sady SDK pro úložiště objektů blob, než jaké jsou běžně dostupné v Azure. Pokud například používáte [v Azure Stack centra verze 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), nejvyšší dostupná verze služby úložiště je verze 2017-11-09. V takovém případě, kromě kroků v této části, budete také muset přidat kód pro cílení na rozhraní API služby úložiště verze 2017-11-09. Příklad cílení na konkrétní verzi rozhraní API úložiště najdete v [této ukázce na GitHubu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). Další informace o verzích služby Azure Storage podporovaných v centru Azure Stack najdete v tématu [úložiště centra pro Azure Stack: rozdíly a požadavky](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences).

### <a name="create-a-java-project"></a>Vytvoření projektu Java

Klientská knihovna Java pro Event Hubs je k dispozici pro použití v projektech Maven z [centrálního úložiště Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)a lze na ni odkazovat pomocí následující deklarace závislosti v souboru projektu Maven: 

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.0.1</version>
    </dependency>
</dependencies>
```

1. Použijte následující kód k vytvoření nové třídy s názvem `Receiver`. Zástupné symboly nahraďte hodnotami použitými při vytváření centra událostí a účtu úložiště:
   
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
    
2. Stáhněte si soubor **SampleCheckpointStore. Java** z [GitHubu](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs/SampleCheckpointStore.java)a přidejte ho do svého projektu. 
3. Sestavte program a zajistěte, aby nedocházelo k chybám. 

## <a name="run-the-applications"></a>Spuštění aplikací
1. Nejdřív spusťte aplikaci **příjemce** .
1. Pak spusťte aplikaci **sender** . 
1. V okně **přijímač** aplikace potvrďte, že vidíte události, které byly publikovány aplikací odesílatele.
1. Stisknutím klávesy **ENTER** v okně přijímače aplikace zastavte aplikaci. 

## <a name="next-steps"></a>Další kroky
Podívejte se [na ukázky Java SDK na GitHubu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs) .

