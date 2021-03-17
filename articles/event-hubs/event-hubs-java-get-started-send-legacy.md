---
title: Odesílání a příjem událostí z Azure Event Hubs pomocí jazyka Java (starší verze)
description: Tento článek popisuje postup vytvoření aplikace Java, která odesílá a přijímá události z Azure Event Hubs pomocí starého balíčku Azure-eventhubs.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: e86ca76f3eb661e1407a02b58e60b62b391f5702
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97607979"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-eventhubs"></a>Použití jazyka Java k posílání událostí nebo přijímání událostí z Azure Event Hubs (Azure-eventhubs)

V tomto rychlém startu se dozvíte, jak odesílat události do centra událostí a přijímat z něj události pomocí balíčku Java **Azure-eventhubs** .

> [!WARNING]
> V tomto rychlém startu se používají staré balíčky **Azure-eventhubs** a **Azure-eventhubs-EPH** . Rychlý Start, který používá nejnovější balíček  **Azure-Messaging-eventhubs** , najdete v tématu [posílání a přijímání událostí pomocí Azure-Messaging-eventhubs](event-hubs-java-get-started-send.md). Pokud chcete aplikaci přesunout z použití starého balíčku na nový, přečtěte si [Průvodce migrace z Azure-eventhubs do Azure-Messaging-eventhubs](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md). 

## <a name="prerequisites"></a>Předpoklady

Pokud s Azure Event Hubs teprve začínáte, přečtěte si téma [přehled Event Hubs](event-hubs-about.md) před provedením tohoto rychlého startu. 

K dokončení tohoto rychlého startu potřebujete následující požadavky:

- **Microsoft Azure předplatné**. Pokud chcete používat služby Azure, včetně Azure Event Hubs, potřebujete předplatné.  Pokud nemáte existující účet Azure, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/) nebo využít výhody pro předplatitele MSDN při [vytváření účtu](https://azure.microsoft.com).
- Java development environment. V tomto rychlém startu se používá [zatmění](https://www.eclipse.org/).
- **Vytvoří obor názvů Event Hubs a centrum událostí**. Prvním krokem je použití [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v [tomto článku](event-hubs-create.md). Pak Získejte hodnotu přístupového klíče pro centrum událostí podle pokynů uvedených v článku [získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Přístupovou klávesu použijete v kódu, který napíšete později v tomto rychlém startu. Výchozí název klíče je: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Odesílání událostí 
V této části se dozvíte, jak vytvořit aplikaci Java pro posílání událostí do centra událostí. 

> [!NOTE]
> Tento rychlý start si můžete stáhnout jako ukázku z [GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend), nahradit řetězce `EventHubConnectionString` a `EventHubName`, hodnotami pro vaše centrum událostí a spustit. Případně můžete postupovat podle kroků v tomto rychlém startu a vytvořit si vlastní.

### <a name="add-reference-to-azure-event-hubs-library"></a>Přidat odkaz na knihovnu Azure Event Hubs

Klientská knihovna Java pro Event Hubs je k dispozici pro použití v projektech Maven z [centrálního úložiště Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Na tuto knihovnu můžete odkazovat pomocí následující deklarace závislosti v rámci souboru projektu Maven:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

Pro různé typy prostředí sestavení můžete explicitně získat nejnovější vydané soubory JAR z [centrálního úložiště Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

Pro jednoduchého vydavatele událostí importujte balíček *com. Microsoft. Azure. eventhubs* pro klientské třídy Event Hubs a balíček *com. Microsoft. Azure. ServiceBus* pro třídy nástrojů, jako jsou například běžné výjimky, které jsou sdíleny s klientem Azure Service Bus Messaging. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Napsání kódu pro odesílání zpráv do centra událostí

Pro následující příklad nejprve vytvořte nový projekt Maven pro aplikaci konzoly nebo prostředí v oblíbeném vývojovém prostředí Java. Přidejte třídu s názvem `SimpleSend` a do třídy přidejte následující kód:

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

### <a name="construct-connection-string"></a>Sestavit připojovací řetězec

Třídu ConnectionStringBuilder použijte k vytvoření hodnoty připojovacího řetězce, která bude předána instanci klienta Event Hubs. Zástupné symboly nahraďte hodnotami, které jste získali při vytváření oboru názvů a centra událostí:

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("<EVENTHUB NAMESPACE") 
                .setEventHubName("EVENT HUB")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("SHARED ACCESS KEY");
```

### <a name="write-code-to-send-events"></a>Napsání kódu pro odesílání událostí

Pomocí transformace řetězce na jeho kódování UTF-8 Vytvořte událost v jednotném čísle. Pak z připojovacího řetězce vytvořte novou instanci klienta Event Hubs a odešlete zprávu:   

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

Sestavte a spusťte program a ujistěte se, že nejsou k dispozici žádné chyby.

Blahopřejeme vám. Nyní jste odeslali zprávy do centra událostí.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>Příloha: způsob směrování zpráv do oddílů EventHub

Předtím, než se zprávy načtou, musí je nejdřív publikovat na oddíly, které budou vydavatelé vyčítat. Pokud jsou zprávy v centru událostí synchronně vydávány pomocí metody sendSync () v objektu com. Microsoft. Azure. eventhubs. EventHubClient, zpráva může být odeslána na konkrétní oddíl nebo distribuována do všech dostupných oddílů v kruhovém dotazování v závislosti na tom, zda je klíč oddílu zadán nebo nikoli.

Když je zadán řetězec představující klíč oddílu, klíč bude použit k určení, který oddíl má odeslat událost.

Pokud není nastaven klíč oddílu, budou se zprávy dotazovat na všechny dostupné oddíly.

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
Kód v tomto kurzu vychází z [EventProcessorSample kódu na GitHubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), který můžete prostudovat, abyste viděli úplnou funkční aplikaci.

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Přijímání zpráv pomocí třídy EventProcessorHost v Javě

**EventProcessorHost** je třída Java, která zjednodušuje přijímání událostí z Event Hubs tím, že spravuje trvalé kontrolní body a paralelní příjem z těchto Event Hubs. Pomocí EventProcessorHost můžete rozdělit události mezi několik přijímačů, a to i v případě, že jsou hostovány v různých uzlech. Tento příklad ukazuje způsob použití třídy EventProcessorHost pro jednoho příjemce.

### <a name="create-a-storage-account"></a>Vytvoření účtu úložiště

Chcete-li použít EventProcessorHost, musíte mít účet [Azure Storage] [Azure Storage účet]:

1. Přihlaste se [Azure Portal](https://portal.azure.com)a na levé straně obrazovky vyberte **vytvořit prostředek** .
2. Vyberte **úložiště** a pak vyberte **účet úložiště**. V okně **vytvořit účet úložiště** zadejte název účtu úložiště. Dokončete zbývající pole, vyberte požadovanou oblast a pak vyberte **vytvořit**.

    ![Vytvořte účet úložiště v Azure Portal](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-azure-storage-account.png)

3. Vyberte nově vytvořený účet úložiště a pak vyberte **přístupové klíče**:

    ![Získání přístupových klíčů v Azure Portal](./media/event-hubs-dotnet-framework-getstarted-receive-eph/select-azure-storage-access-keys.png)

    Zkopírujte hodnotu klíč1 do dočasného umístění. Použijete ho později v tomto kurzu.

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

Pro různé typy prostředí sestavení můžete explicitně získat nejnovější vydané soubory JAR z [centrálního úložiště Maven](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22).

1. Pro následující příklad nejprve vytvořte nový projekt Maven pro aplikaci konzoly nebo prostředí v oblíbeném vývojovém prostředí Java. Třída je volána `ErrorNotificationHandler` .     

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
2. Použijte následující kód k vytvoření nové třídy s názvem `EventProcessorSample`. Zástupné symboly nahraďte hodnotami použitými při vytváření centra událostí a účtu úložiště:

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
   }
   ```
3. Vytvořte jednu další třídu s názvem `EventProcessor` pomocí následujícího kódu:

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

Tento kurz používá jednu instanci třídy EventProcessorHost. Pro zvýšení propustnosti doporučujeme spouštět více instancí EventProcessorHost, pokud je to možné v samostatných počítačích.  Poskytuje také redundanci. V těchto případech se spolu různé instance navzájem automaticky koordinují, aby dokázaly vyrovnávat zatížení přijatých událostí. Pokud chcete, aby každý z několika příjemců zpracovával *všechny* události, musíte použít koncept **ConsumerGroup**. Když přijímáte události z různých počítačů, může být užitečné nazvat instance třídy EventProcessorHost podle počítačů (nebo rolí), ve kterých jsou nasazené.

### <a name="publishing-messages-to-eventhub"></a>Publikování zpráv do centra EventHub

Předtím, než se zprávy načtou, musí je nejdřív publikovat na oddíly, které budou vydavatelé vyčítat. Je potřeba poznamenat, že pokud se zprávy v centru událostí asynchronně publikují pomocí metody sendSync () v objektu com. Microsoft. Azure. eventhubs. EventHubClient, zpráva se dá poslat do konkrétního oddílu nebo distribuovat do všech dostupných oddílů v rámci kruhového dotazování v závislosti na tom, jestli je zadaný klíč oddílu, nebo ne.

Když je zadán řetězec představující klíč oddílu, klíč se vyhodnotí pomocí algoritmu hash pro určení oddílu, do kterého se má událost odeslat.

Pokud není nastaven klíč oddílu, budou se zprávy s kruhovým dotazováním na všechny dostupné oddíly.

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

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Implementace vlastního CheckpointManager pro EventProcessorHost (EPH)

Rozhraní API poskytuje mechanismus pro implementaci vlastního správce kontrolních bodů pro scénáře, ve kterých není výchozí implementace kompatibilní s vaším případem použití.

Výchozí správce kontrolních bodů používá úložiště objektů blob, ale pokud přepíšete správce kontrolního bodu, který používá EPH s vlastní implementací, můžete použít jakékoli úložiště, které chcete použít pro implementaci správce kontrolních bodů.

Vytvořte třídu, která implementuje rozhraní com. Microsoft. Azure. eventprocessorhost. ICheckpointManager

Použití vlastní implementace správce kontrolních bodů (com. Microsoft. Azure. eventprocessorhost. ICheckpointManager)

V rámci vaší implementace můžete přepsat výchozí mechanizmus kontrolního bodu a implementovat vlastní kontrolní body na základě vašeho vlastního úložiště dat (například SQL Server, CosmosDB a mezipaměti Azure pro Redis). Pro všechny instance EPH, které zpracovávají události pro skupinu uživatelů, doporučujeme, aby úložiště použité pro obnovení vaší implementace správce kontrolních bodů bylo dostupné.

Můžete použít libovolné úložiště dat, které je k dispozici ve vašem prostředí.

Třída com. Microsoft. Azure. eventprocessorhost. EventProcessorHost poskytuje dva konstruktory, které umožňují přepsat Správce kontrolních bodů pro váš EventProcessorHost.

## <a name="next-steps"></a>Další kroky
Přečtěte si následující články: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkce a terminologie ve službě Azure Event Hubs](event-hubs-features.md)
- [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)
