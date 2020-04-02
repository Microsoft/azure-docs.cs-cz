---
title: Odesílání nebo přijímání událostí z Azure Event Hubs pomocí Jazyka Java (starší verze)
description: Tento článek poskytuje návod k vytvoření aplikace Java, která odesílá nebo přijímá události do/z Azure Event Hubs pomocí starého balíčku azure-eventhubs.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 5a34ac2d1b7401d31ae518334aedc15c626b66a3
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529487"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-eventhubs"></a>Použití Jazyka Java k odesílání událostí nebo přijímání událostí z Azure Event Hubs (azure-eventhubs)

Tento rychlý start ukazuje, jak odesílat události a přijímat události z centra událostí pomocí balíčku **Azure-eventhubs** Java.

> [!WARNING]
> Tento rychlý start používá staré **balíčky azure-eventhubs** a **azure-eventhubs-eph.** Pro rychlý start, který používá nejnovější balíček **azure-messaging eventhubs,** najdete v tématu [odesílání a přijímání událostí pomocí azure-messaging eventhubs](get-started-java-send-v2.md). Pokud chcete přesunout aplikaci z používání starého balíčku na nový, [přečtěte si průvodce migrací z azure-eventhubs na azure-messaging eventhubs](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md). 


## <a name="prerequisites"></a>Požadavky

Pokud s Azure Event Hubs tečujete, přečtěte si [téma Přehled centra událostí,](event-hubs-about.md) než začnete tento rychlý start. 

Chcete-li tento rychlý start dokončit, potřebujete následující požadavky:

- **Předplatné Microsoft Azure**. Pokud chcete používat služby Azure, včetně Azure Event Hubs, potřebujete předplatné.  Pokud nemáte existující účet Azure, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/) nebo použít výhody předplatitele MSDN při vytváření [účtu](https://azure.microsoft.com).
- Vývojové prostředí Javy. Tento rychlý start používá [Eclipse](https://www.eclipse.org/).
- **Vytvořte obor názvů Event Hubs a centrum událostí**. Prvním krokem je použití [portálu Azure](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Chcete-li vytvořit obor názvů a centrum událostí, postupujte podle postupu v [tomto článku](event-hubs-create.md). Potom získat hodnotu přístupového klíče pro centrum událostí podle pokynů z článku: [Získat připojovací řetězec](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Přístupový klíč v kódu, který napíšete později v tomto rychlém startu. Výchozí název klíče je: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Odesílání událostí 
V této části se zobrazí, jak vytvořit java aplikaci pro odesílání událostí z centra událostí. 

> [!NOTE]
> Tento rychlý start si můžete stáhnout jako ukázku z [GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), nahradit řetězce `EventHubConnectionString` a `EventHubName`, hodnotami pro vaše centrum událostí a spustit. Případně můžete postupovat podle kroků v tomto rychlém startu a vytvořit si vlastní.

### <a name="add-reference-to-azure-event-hubs-library"></a>Přidání odkazu na knihovnu Azure Event Hubs

Klientská knihovna Java pro Event Hubs je k dispozici pro použití v projektech Maven z [Centrálního úložiště Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Na tuto knihovnu můžete odkazovat pomocí následující deklarace závislostí uvnitř souboru projektu Maven:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

Pro různé typy prostředí sestavení můžete explicitně získat nejnovější vydané soubory JAR z [centrálního úložiště Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Pro jednoduchého vydavatele událostí importujte balíček *com.microsoft.azure.eventhubs* pro třídy klientů Event Hubs a balíček *com.microsoft.azure.servicebus* pro třídy nástrojů, jako jsou běžné výjimky sdílené s klientem zasílání zpráv Služby Azure Service Bus. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Napsání kódu pro odesílání zpráv do centra událostí

Pro následující příklad nejprve vytvořte nový projekt Maven pro aplikaci konzoly nebo prostředí v oblíbeném vývojovém prostředí Java. Přidejte třídu s názvem `SimpleSend`a přidejte do ní následující kód:

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

### <a name="construct-connection-string"></a>Vytvořit připojovací řetězec

Pomocí třídy ConnectionStringBuilder vytvořte hodnotu připojovacího řetězce, která se předává instanci klienta Event Hubs. Nahraďte zástupné symboly hodnotami, které jste získali při vytváření oboru názvů a centra událostí:

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("<EVENTHUB NAMESPACE") 
                .setEventHubName("EVENT HUB")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("SHARED ACCESS KEY");
```

### <a name="write-code-to-send-events"></a>Napsat kód pro odesílání událostí

Vytvořte jedinečnou událost transformací řetězce do kódování Bajt UTF-8. Potom vytvořte novou instanci klienta Event Hubs z připojovacího řetězce a odešlete zprávu:   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/TLS connection, which is expensive.
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

Sestavení a spuštění programu a ujistěte se, že neexistují žádné chyby.

Blahopřejeme! Nyní jste odeslali zprávy do centra událostí.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>Dodatek: Způsob směrování zpráv do oddílů EventHub

Před načtení mši příjemci musí být publikovány do oddílů nejprve vydavatelé. Když jsou zprávy publikovány do centra událostí synchronně pomocí metody sendSync() na objektu com.microsoft.azure.eventhubs.EventHubClient, může být zpráva odeslána do určitého oddílu nebo distribuována do všech dostupných oddílů způsobem kruhového dotazování v závislosti na tom, zda je klíč oddílu zadán nebo ne.

Pokud je zadán řetězec představující klíč oddílu, bude kód zajistěn, aby bylo možné určit, do kterého oddílu se má událost odeslat.

Pokud není nastaven klíč oddílu, budou zprávy zaokrouhleny do všech dostupných oddílů.

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
Kód v tomto kurzu je založen na [kódu EventProcessorSample na GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), který můžete zkontrolovat, abyste viděli úplnou pracovní aplikaci.

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Přijímání zpráv pomocí třídy EventProcessorHost v Javě

**EventProcessorHost** je třída Java, která zjednodušuje příjem událostí z event hubů správou trvalých kontrolních bodů a paralelních příjemů z těchto center událostí. Pomocí EventProcessorHost, můžete rozdělit události mezi více přijímačů, i když hostované v různých uzlech. Tento příklad ukazuje způsob použití třídy EventProcessorHost pro jednoho příjemce.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Chcete-li používat EventProcessorHost, musíte mít účet [Azure Storage][Účet úložiště Azure]:

1. Přihlaste se na [portál Azure](https://portal.azure.com)a vyberte **Vytvořit prostředek** na levé straně obrazovky.
2. Vyberte **Úložiště**a pak vyberte **Účet úložiště**. V okně **Vytvořit účet úložiště** zadejte název účtu úložiště. Vyplňte zbývající pole, vyberte požadovanou oblast a pak vyberte **Vytvořit**.
   
    ![Vytvoření účtu úložiště na Webu Azure Portal](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-azure-storage-account.png)

3. Vyberte nově vytvořený účet úložiště a pak vyberte **Přístupové klíče**:
   
    ![Získání přístupových klíčů na webu Azure Portal](./media/event-hubs-dotnet-framework-getstarted-receive-eph/select-azure-storage-access-keys.png)

    Zkopírujte hodnotu key1 do dočasného umístění. Použijete ho později v tomto kurzu.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Vytvoření projektu jazyka Java pomocí hostitele EventProcessor

Klientská knihovna Java pro centra událostí je k dispozici pro použití v projektech Maven z [centrálního úložiště Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)a může být odkazována pomocí následující deklarace závislostí uvnitř souboru projektu Maven: 

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

Pro různé typy prostředí sestavení můžete explicitně získat nejnovější vydané soubory JAR z [centrálního úložiště Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22).

1. Pro následující příklad nejprve vytvořte nový projekt Maven pro aplikaci konzoly nebo prostředí v oblíbeném vývojovém prostředí Java. Třída se `ErrorNotificationHandler`nazývá .     
   
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
2. Použijte následující kód k vytvoření nové třídy s názvem `EventProcessorSample`. Nahraďte zástupné symboly hodnotami použitými při vytváření centra událostí a účtu úložiště:
   
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
3. Vytvořte ještě `EventProcessor`jednu třídu s názvem , pomocí následujícího kódu:
   
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

Tento kurz používá jednu instanci třídy EventProcessorHost. Chcete-li zvýšit propustnost, doporučujeme spustit více instancí EventProcessorHost, nejlépe na samostatných počítačích.  Poskytuje také redundanci. V těchto případech se spolu různé instance navzájem automaticky koordinují, aby dokázaly vyrovnávat zatížení přijatých událostí. Pokud chcete, aby každý z několika příjemců zpracovával *všechny* události, musíte použít koncept **ConsumerGroup**. Když přijímáte události z různých počítačů, může být užitečné nazvat instance třídy EventProcessorHost podle počítačů (nebo rolí), ve kterých jsou nasazené.

### <a name="publishing-messages-to-eventhub"></a>Publikování zpráv na EventHub

Před načtení mši příjemci musí být publikovány do oddílů nejprve vydavatelé. Stojí za zmínku, že při zprávy jsou publikovány do centra událostí synchronně pomocí sendSync() metoda na com.microsoft.azure.eventhubs.EventHubClient objektu, zpráva může být odeslána do určitého oddílu nebo distribuovány do všech dostupných oddílů způsobem kruhovédotazování v závislosti na tom, zda je zadán klíč oddílu nebo ne.

Pokud je zadán řetězec představující klíč oddílu, je zakódován klíč, který určuje, do kterého oddílu má být událost odeslána.

Pokud klíč oddílu není nastaven, jsou zprávy kruhové dotazování na všechny dostupné oddíly

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

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Implementace vlastního nástroje checkpointmanager pro EventProcessorHost (EPH)

Rozhraní API poskytuje mechanismus pro implementaci vlastního správce kontrolních bodů pro scénáře, kde výchozí implementace není kompatibilní s případem použití.

Výchozí správce kontrolních bodů používá úložiště objektů blob, ale pokud přepíšete správce kontrolních bodů používaný EPH s vlastní implementací, můžete použít libovolné úložiště, které chcete zálohovat implementaci správce kontrolních bodů.

Vytvoření třídy, která implementuje rozhraní com.microsoft.azure.eventprocessorhost.ICheckpointManager

Použijte vlastní implementaci správce kontrolních bodů (com.microsoft.azure.eventprocessorhost.ICheckpointManager)

V rámci implementace můžete přepsat výchozí mechanismus kontrolních bodů a implementovat vlastní kontrolní body na základě vlastního úložiště dat (jako je SQL Server, CosmosDB a Azure Cache for Redis). Doporučujeme, aby úložiště používané k zálohování implementace správce kontrolních bodů je přístupné všem instancím EPH, které zpracovávají události pro skupinu spotřebitelů.

Můžete použít libovolné úložiště dat, které je k dispozici ve vašem prostředí.

Třída com.microsoft.azure.eventprocessorhost.EventProcessorHost poskytuje dva konstruktory, které umožňují přepsat správce kontrolních bodů pro eventprocessorhost.


## <a name="next-steps"></a>Další kroky
Přečtěte si následující články: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkce a terminologie ve službě Azure Event Hubs](event-hubs-features.md)
- [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

