---
title: Použití jazyka Java k posílání dat do a z Azure Event Hubs
description: Tento článek popisuje postup vytvoření aplikace v jazyce Java, která odesílá události do služby Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18, seo-java-august2019
ms.date: 04/15/2019
ms.author: shvija
ms.openlocfilehash: 8684e786de64f91acba0c61017083015288ca4eb
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70172786"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs"></a>Použití jazyka Java k posílání událostí nebo přijímání událostí z Azure Event Hubs

Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

V tomto kurzu se dozvíte, jak vytvářet aplikace v jazyce Java pro posílání událostí nebo přijímání událostí z centra událostí. 

> [!NOTE]
> Tento rychlý start si můžete stáhnout jako ukázku z [GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), nahradit řetězce `EventHubConnectionString` a `EventHubName`, hodnotami pro vaše centrum událostí a spustit. Alternativně můžete vytvořit vlastní řešení podle kroků v tomto kurzu.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

- Aktivní účet Azure. Pokud ještě nemáte předplatné Azure, vytvořte si nejprve [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- Vývojové prostředí Java. Tento kurz používá [Eclipse](https://www.eclipse.org/).
- **Vytvoří obor názvů Event Hubs a centrum událostí**. Prvním krokem je použití webu [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centra událostí, postupujte podle pokynů v [v tomto článku](event-hubs-create.md). Pak Získejte hodnotu přístupového klíče pro centrum událostí podle pokynů v článku: [Získá připojovací řetězec](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). V kódu, který napíšete později v tomto kurzu použijete přístupový klíč. Výchozí název klíče: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Odesílání událostí 
V této části se dozvíte, jak vytvořit aplikaci Java pro posílání událostí do centra událostí. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Přidat odkaz na knihovnu služby Azure Event Hubs

Klientská knihovna Java pro Event Hubs je k dispozici pro použití v projektech Maven z [centrálního úložiště Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Na tuto knihovnu můžete odkazovat pomocí následující deklarace závislosti v rámci souboru projektu Maven:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

Pro různé typy prostředí sestavení, můžete explicitně získat nejnovější vydané soubory JAR z [centrálního úložiště Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Pro jednoduché události vydavatele, import *com.microsoft.azure.eventhubs* balíček pro klientské třídy služby Event Hubs a *com.microsoft.azure.servicebus* jako balíček pro nástroj třídy běžné výjimky, které jsou sdíleny s klienta zasílání zpráv Azure Service Bus. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Napsání kódu pro odesílání zpráv do centra událostí

Pro následující příklad nejprve vytvořte nový projekt Maven pro aplikaci konzoly nebo prostředí v oblíbeném vývojovém prostředí Java. Přidejte třídu s názvem `SimpleSend`a do třídy přidejte následující kód:

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {
            
            
    }
 }
```

### <a name="construct-connection-string"></a>Vytvoření připojovacího řetězce

ConnectionStringBuilder třídu použijte k sestavení kompletních hodnotu připojovacího řetězce k předání do instance služby Event Hubs klienta. Nahraďte zástupné symboly hodnotami, které jste získali při vytváření obor názvů a Centrum událostí:

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("speventhubns") 
                .setEventHubName("speventhub")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("2+WMsyyy1XmUtEnRsfOmTTyGasfJgsVjGAOIN20J1Y8=");
```

### <a name="write-code-to-send-events"></a>Napsání kódu pro odesílání událostí

Vytvořte jednotný událost pomocí transformace řetězce do jeho kódování bajtů kódování UTF-8. Potom vytvořte nové instance služby Event Hubs klienta z připojovacího řetězce a odeslat zprávu:   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/SSL connection, which is expensive.
        // It is always a best practice to reuse these instances. The following sample shows this.
        final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);


        try {
            for (int i = 0; i < 10; i++) {

                String payload = "Message " + Integer.toString(i);
                byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
                EventData sendEvent = EventData.create(payloadBytes);

                // Send - not tied to any partition
                // Event Hubs service will round-robin the events across all Event Hubs partitions.
                // This is the recommended & most reliable way to send to Event Hubs.
                ehClient.sendSync(sendEvent);
            }

            System.out.println(Instant.now() + ": Send Complete...");
            System.out.println("Press Enter to stop.");
            System.in.read();
        } finally {
            ehClient.closeSync();
            executorService.shutdown();
        }

``` 

Sestavení a spusťte program a ujistěte se, že zde nejsou žádné chyby.

Blahopřejeme! Nyní jste odeslali zprávy do centra událostí.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>Obsažen Směrování zpráv do oddílů EventHub

Předtím, než zprávy jsou načítána pro spotřebitele, mají být publikována do oddílů nejprve podle vydavatele. Po publikování zprávy do centra událostí synchronně pomocí metody sendSync() com.microsoft.azure.eventhubs.EventHubClient objektu, může být zprávu odeslat do konkrétního oddílu ani distribuován do všech dostupných oddílech způsobem kruhové dotazování v závislosti na tom, jestli je nebo není zadána klíč oddílu.

Pokud je zadán řetězec představující klíč oddílu, klíč se k určení oddíl, který se k odeslání události do hashovat.

Pokud není nastaven klíč oddílu, pak zprávy budou kruhové robined na všechny dostupné oddíly.

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="receive-events"></a>Příjem událostí
Kód v tomto kurzu vychází z [EventProcessorSample kódu na Githubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), které můžete zkontrolovat zobrazíte kompletní funkční aplikaci.

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Přijímání zpráv pomocí třídy EventProcessorHost v Javě

**EventProcessorHost** je třída rozhraní Java, která zjednodušuje přijímání událostí ze služby Event Hubs tím, že spravuje trvalé kontrolní body a paralelní příjmy ze služby Event Hubs. Pomocí třídy EventProcessorHost můžete události rozdělit mezi několik příjemců, i když jsou hostované v různých uzlech. Tento příklad ukazuje způsob použití třídy EventProcessorHost pro jednoho příjemce.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Chcete-li použít EventProcessorHost, musíte mít účet [Azure Storage] [Azure Storage účet]:

1. Přihlaste se [Azure Portal](https://portal.azure.com)a na levé straně obrazovky vyberte **vytvořit prostředek** .
2. Vyberte **úložiště**a pak vyberte **účet úložiště**. V **vytvořit účet úložiště** okno, zadejte název účtu úložiště. Dokončete zbývající pole, vyberte požadovanou oblast a pak vyberte **vytvořit**.
   
    ![Vytvoření účtu úložiště](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)

3. Vyberte nově vytvořený účet úložiště a pak vyberte **přístupové klíče**:
   
    ![Získání přístupových klíčů](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

    Zkopírujte hodnotu key1 do dočasného umístění. Použijete ho později v tomto kurzu.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Vytvoření projektu jazyka Java pomocí hostitele EventProcessor

Klientská knihovna Java pro Event Hubs je k dispozici pro použití v projektech Maven z [centrálního úložiště Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)a lze na ni odkazovat pomocí následující deklarace závislosti v souboru projektu Maven: 

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.4.0</version>
</dependency>
```

Pro různé typy prostředí sestavení můžete explicitně získat nejnovější vydané soubory JAR z [úložiště \ Maven Central) [https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22 ].  

1. Pro následující příklad nejprve vytvořte nový projekt Maven pro aplikaci konzoly nebo prostředí v oblíbeném vývojovém prostředí Java. Třída se nazývá `ErrorNotificationHandler`.     
   
    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   
    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. Použijte následující kód k vytvoření nové třídy s názvem `EventProcessorSample`. Nahraďte zástupné symboly hodnotami použitými při vytváření event hubu a úložiště účtu:
   
   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";
        
           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);
        
           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);
        
           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
               try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
           {
               return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!
        
           System.out.println("End of sample");
       }
    ```
3. Vytvořte jeden další třídu s názvem `EventProcessor`, pomocí následujícího kódu:
   
    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;
                    
                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                            data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

Tento kurz používá jednu instanci třídy EventProcessorHost. Pokud chcete zvýšit propustnost, doporučujeme, spusťte několik instancí třídy EventProcessorHost, pokud možno na samostatných počítačích.  Poskytuje také redundance. V těchto případech se spolu různé instance navzájem automaticky koordinují, aby dokázaly vyrovnávat zatížení přijatých událostí. Pokud chcete, aby každý z několika příjemců zpracovával *všechny* události, musíte použít koncept **ConsumerGroup**. Když přijímáte události z různých počítačů, může být užitečné nazvat instance třídy EventProcessorHost podle počítačů (nebo rolí), ve kterých jsou nasazené.

### <a name="publishing-messages-to-eventhub"></a>Publikování zprávy do centra událostí

Předtím, než zprávy jsou načítána pro spotřebitele, mají být publikována do oddílů nejprve podle vydavatele. Je vhodné poznamenat, že po publikování zprávy do centra událostí synchronně pomocí metody sendSync() com.microsoft.azure.eventhubs.EventHubClient objektu, zpráva může odeslat do konkrétního oddílu ani distribuovat do všech dostupných oddílech kruhové dotazování způsobem v závislosti na tom, zda je nebo není zadána klíč oddílu.

Pokud je zadán řetězec představující klíč oddílu, klíč se po zahašování použije k určení oddíl, který se k odeslání události.

Pokud není nastaven klíč oddílu, pak zprávy jsou kruhové robined na všechny dostupné oddíly.

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Implementace vlastního CheckpointManager pro třídy EventProcessorHost (EPH)

Rozhraní API poskytuje mechanismus pro implementaci správce vlastní kontrolní bod pro scénáře, kde výchozí implementace není kompatibilní s vašemu případu použití.

Správce kontrolního bodu výchozí využívá úložiště objektů blob, ale pokud přepíšete kontrolního bodu správce používá EPH s vlastní implementaci, můžete použít jakékoli úložiště, které chcete zálohovat vaší implementace Správce kontrolního bodu.

Vytvořte třídu, která implementuje rozhraní com.microsoft.azure.eventprocessorhost.ICheckpointManager

Použít vlastní implementaci správce kontrolního bodu (com.microsoft.azure.eventprocessorhost.ICheckpointManager)

V rámci vaší implementace můžete přepsat výchozí mechanizmus kontrolního bodu a implementovat vlastní kontrolní body na základě vašeho vlastního úložiště dat (například SQL Server, CosmosDB a mezipaměti Azure pro Redis). Doporučujeme vám, že úložiště využívané vaší implementace Správce kontrolního bodu je přístupné pro všechny instance EPH, které zpracovávají události pro skupinu příjemců.

Můžete použít libovolné úložiště dat, která je k dispozici ve vašem prostředí.

Třída com.microsoft.azure.eventprocessorhost.EventProcessorHost vám poskytne dva konstruktory, které umožňují kontrolního bodu správce pro vaše EventProcessorHost override.


## <a name="next-steps"></a>Další kroky
Přečtěte si následující články: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkce a terminologie ve službě Azure Event Hubs](event-hubs-features.md)
- [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

