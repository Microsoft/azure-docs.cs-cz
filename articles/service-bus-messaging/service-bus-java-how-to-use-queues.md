---
title: Použití Azure Service Bus front s Java (Azure-Messaging-ServiceBus)
description: V tomto kurzu se naučíte používat Java k posílání a přijímání zpráv z fronty Azure Service Bus. Použijete nový balíček Azure-Messaging-ServiceBus.
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: 4eae72b38d36348a6bdc98d3cbd98a5a712b3161
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97399294"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>Posílání zpráv a příjem zpráv z front Azure Service Bus (Java)
V tomto rychlém startu vytvoříte aplikaci Java, která bude odesílat zprávy do a přijímat zprávy z fronty Azure Service Bus. 

> [!IMPORTANT]
> V tomto rychlém startu se používá nový balíček Azure-Messaging-ServiceBus. Rychlý Start, který používá starý balíček Azure-ServiceBus, najdete v tématu [posílání a přijímání zpráv pomocí Azure-ServiceBus](service-bus-java-how-to-use-queues-legacy.md).


## <a name="prerequisites"></a>Předpoklady
- Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat výhody pro [předplatitele MSDN](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Pokud nemáte frontu, ve které byste mohli pracovat, postupujte podle kroků v tématu [použití Azure Portal k vytvoření fronty Service Bus](service-bus-quickstart-portal.md) . Poznamenejte si **připojovací řetězec** pro obor názvů Service Bus a název **fronty** , kterou jste vytvořili.
- Nainstalujte [sadu Azure SDK pro jazyk Java][Azure SDK for Java]. Pokud používáte zatmění, můžete nainstalovat [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] , který obsahuje sadu Azure SDK pro jazyk Java. Pak můžete přidat **knihovny Microsoft Azure pro jazyk Java** do projektu. Pokud používáte IntelliJ, přečtěte si téma [instalace Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-queue"></a>Zasílání zpráv do fronty
V této části vytvoříte projekt konzoly Java a přidáte kód pro odesílání zpráv do fronty, kterou jste vytvořili dříve. 

### <a name="create-a-java-console-project"></a>Vytvoření projektu konzoly Java
Vytvořte projekt Java pomocí zatmění nebo nástroje podle vašeho výběru. 

### <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití Service Bus
Přidejte odkaz na knihovnu Azure Service Bus. Klientská knihovna Java pro Service Bus je k dispozici v [centrálním úložišti Maven](https://search.maven.org/search?q=a:azure-messaging-servicebus). Na tuto knihovnu můžete odkazovat pomocí následující deklarace závislosti v rámci souboru projektu Maven:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-servicebus</artifactId>
    <version>7.0.0</version>
</dependency>
```

### <a name="add-code-to-send-messages-to-the-queue"></a>Přidat kód pro posílání zpráv do fronty
1. Přidejte následující `import` příkazy do tématu souboru Java. 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. Ve třídě Definujte proměnné pro uložení připojovacího řetězce a názvu fronty, jak je znázorněno níže: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String queueName = "<QUEUE NAME>";    
    ```

    Nahraďte `<NAMESPACE CONNECTION STRING>` připojovacím řetězcem k vašemu oboru názvů Service Bus. A nahraďte `<QUEUE NAME>` názvem fronty.
3. Přidejte do třídy metodu s názvem `sendMessage` a odešlete jednu zprávu do fronty. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();
        
        // send one message to the queue
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the queue: " + queueName);        
    }
    ```
1. Chcete-li `createMessages` vytvořit seznam zpráv, přidejte do třídy metodu s názvem. Obvykle tyto zprávy získáte z různých částí aplikace. Tady vytvoříte seznam ukázkových zpráv.

    ```java
    static List<ServiceBusMessage> createMessages()
    {
        // create a list of messages and return it to the caller
        ServiceBusMessage[] messages = {
                new ServiceBusMessage("First message"),
                new ServiceBusMessage("Second message"),
                new ServiceBusMessage("Third message")
        };
        return Arrays.asList(messages);
    }
    ```
1. Přidejte metodu s názvem metoda `sendMessageBatch` pro odeslání zpráv do fronty, kterou jste vytvořili. Tato metoda vytvoří `ServiceBusSenderClient` pro frontu, vyvolá `createMessages` metodu pro získání seznamu zpráv, připraví jednu nebo více dávek a odešle dávky do fronty. 

```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();

        // Creates an ServiceBusMessageBatch where the ServiceBus.
        ServiceBusMessageBatch messageBatch = senderClient.createMessageBatch();        
        
        // create a list of messages
        List<ServiceBusMessage> listOfMessages = createMessages();
        
        // We try to add as many messages as a batch can fit based on the maximum size and send to Service Bus when
        // the batch can hold no more messages. Create a new batch for next set of messages and repeat until all
        // messages are sent.        
        for (ServiceBusMessage message : listOfMessages) {
            if (messageBatch.tryAddMessage(message)) {
                continue;
            }

            // The batch is full, so we create a new batch and send the batch.
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }
        
        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
        }

        //close the client
        senderClient.close();
    }
```

## <a name="receive-messages-from-a-queue"></a>Přijímání zpráv z fronty
V této části přidáte kód pro načtení zpráv z fronty. 

1. Přidejte metodu pojmenovanou `receiveMessages` pro příjem zpráv z fronty. Tato metoda vytvoří `ServiceBusProcessorClient` pro frontu zadáním obslužné rutiny pro zpracování zpráv a další pro zpracování chyb. Potom spustí procesor, počká pár sekund, vytiskne přijaté zprávy a pak procesor zastaví a ukončí.

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString());
        };

        // handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .queueName(queueName)
            .processMessage(messageProcessor)
            .processError(errorHandler)
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }    
    ```
2. Aktualizujte `main` metodu na vyvolání `sendMessage` , `sendMessageBatch` metody a `receiveMessages` k vyvolání `InterruptedException` .     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Spuštění aplikace
Při spuštění aplikace se v okně konzoly zobrazí následující zprávy. 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Starting the processor
Received message: Hello, World!
Received message: First message in the batch
Received message: Second message in the batch
Received message: Three message in the batch
Stopping and closing the processor
```

Na stránce **Přehled** pro obor názvů Service Bus v Azure Portal můžete zobrazit počet **příchozích** a **odchozích** zpráv. Může se stát, že budete muset počkat na minutu nebo si aktualizovat stránku, aby se zobrazily nejnovější hodnoty. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Počet příchozích a odchozích zpráv" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Na stránce s **přehledem** Vyberte frontu a přejděte na stránku **Service Bus Queue** . Na této stránce se zobrazuje počet **příchozích** a **odchozích** zpráv. Zobrazí se také další informace, jako je **aktuální velikost** fronty, **maximální velikost**, **Počet aktivních zpráv** a tak dále. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Podrobnosti fronty" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>Další kroky
Podívejte se na následující dokumentaci a ukázky:

- [Klientská knihovna Azure Service Bus pro Java – soubor Readme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Ukázky na GitHubu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/servicebus/azure-messaging-servicebus/src/samples)
- [Referenční dokumentace k rozhraní API v Javě](/java/api/overview/azure/servicebus?preserve-view=true&view=azure-java-stable)

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
