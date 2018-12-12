---
title: Příjem událostí pomocí Javy – Azure Event Hubs | Dokumentace Microsoftu
description: Tento článek poskytuje návod pro vytvoření aplikace v Javě, která bude přijímat události z Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: timlt
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: e29cf43f490bf5e8bac5e5c36b16476f93d80bfa
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53081955"
---
# <a name="receive-events-from-azure-event-hubs-using-java"></a>Příjem událostí ze služby Azure Event Hubs pomocí Javy

Event Hubs je vysoce škálovatelná služba systému, která dokáže ingestovat miliony událostí za sekundu a umožňuje aplikaci zpracovávat a analyzovat velké objemy dat vytvářené vašimi připojenými zařízeními a aplikacemi. Když Event Hubs shromáždí data, můžete je zpracovat a uložit pomocí libovolného úložného clusteru nebo poskytovatele datové analýzy v reálném čase.

Další informace najdete v tématu [Přehled služby Event Hubs][Event Hubs overview].

Tento kurz ukazuje, jak příjem událostí do centra událostí pomocí konzolové aplikace napsané v jazyce Java.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu, budete potřebovat následující:

* Vývojové prostředí Java. Pro účely tohoto kurzu předpokládáme [Eclipse](https://www.eclipse.org/).
* Aktivní účet Azure. Pokud ještě nemáte předplatné Azure, vytvořte si nejprve [bezplatný účet][].

Kód v tomto kurzu vychází z [EventProcessorSample kódu na Githubu](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), které můžete zkontrolovat zobrazíte kompletní funkční aplikaci.

## <a name="receive-messages-with-eventprocessorhost-in-java"></a>Přijímání zpráv pomocí třídy EventProcessorHost v Javě

**EventProcessorHost** je třída rozhraní Java, která zjednodušuje přijímání událostí ze služby Event Hubs tím, že spravuje trvalé kontrolní body a paralelní příjmy ze služby Event Hubs. Pomocí třídy EventProcessorHost můžete události rozdělit mezi několik příjemců, i když jsou hostované v různých uzlech. Tento příklad ukazuje způsob použití třídy EventProcessorHost pro jednoho příjemce.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Pokud chcete EventProcessorHost používat, musíte mít [účtu služby Azure Storage][Azure Storage account]:

1. Přihlaste se k [webu Azure portal][Azure portal]a klikněte na tlačítko **+ vytvořit prostředek** na levé straně obrazovky.
2. Klikněte na **Storage** a poté klikněte na **Účet úložiště**. V **vytvořit účet úložiště** okno, zadejte název účtu úložiště. Dokončete zbývající pole, vyberte požadovanou oblast a potom klikněte na tlačítko **vytvořit**.
   
    ![Vytvoření účtu úložiště](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage2.png)

3. Klikněte na nově vytvořený účet úložiště a pak klikněte na tlačítko **přístupové klíče**:
   
    ![Získání přístupových klíčů](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-storage3.png)

    Zkopírujte hodnotu key1 do dočasného umístění. Použijete ho později v tomto kurzu.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Vytvoření projektu jazyka Java pomocí hostitele EventProcessor

Klientská knihovna Java pro Event Hubs je k dispozici pro použití v projektech Maven z [centrálního úložiště Maven][Maven Package]a může být odkazováno pomocí následující deklarace závislostí uvnitř Maven soubor projektu. Aktuální verze pro artefakt azure-eventhubs-eph je 2.0.1 a aktuální verze pro artefakt azure-eventhubs je 1.0.2:    

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>1.0.2</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.0.1</version>
</dependency>
```

Pro různé typy prostředí sestavení, můžete explicitně získat nejnovější vydané soubory JAR z [centrálního úložiště Maven][Maven Package].  

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

## <a name="publishing-messages-to-eventhub"></a>Publikování zprávy do centra událostí

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

## <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Implementace vlastního CheckpointManager pro třídy EventProcessorHost (EPH)

Rozhraní API poskytuje mechanismus pro implementaci správce vlastní kontrolní bod pro scénáře, kde výchozí implementace není kompatibilní s vašemu případu použití.

Správce kontrolního bodu výchozí využívá úložiště objektů blob, ale pokud přepíšete kontrolního bodu správce používá EPH s vlastní implementaci, můžete použít jakékoli úložiště, které chcete zálohovat vaší implementace Správce kontrolního bodu.

Vytvořte třídu, která implementuje rozhraní com.microsoft.azure.eventprocessorhost.ICheckpointManager

Použít vlastní implementaci správce kontrolního bodu (com.microsoft.azure.eventprocessorhost.ICheckpointManager)

V rámci vaší implementace můžete přepsat výchozí mechanismus vytváření kontrolních bodů a implementovat vlastní kontrolními body založenými na vlastní úložiště dat (SQL Server, služby cosmos DB, Azure mezipaměti Redis atd). Doporučujeme vám, že úložiště využívané vaší implementace Správce kontrolního bodu je přístupné pro všechny instance EPH, které zpracovávají události pro skupinu příjemců.

Můžete použít libovolné úložiště dat, která je k dispozici ve vašem prostředí.

Třída com.microsoft.azure.eventprocessorhost.EventProcessorHost vám poskytne dva konstruktory, které umožňují kontrolního bodu správce pro vaše EventProcessorHost override.

## <a name="next-steps"></a>Další postup
V tomto rychlém startu jste vytvořili aplikaci Java, která přijaté zprávy z centra událostí. Zjistěte, jak odesílat události do centra událostí pomocí Javy, najdete v článku [odesílání událostí z centra událostí – Java](event-hubs-java-get-started-send.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
[Azure Storage account]: ../storage/common/storage-create-storage-account.md
[Azure portal]: https://portal.azure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png
[bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
