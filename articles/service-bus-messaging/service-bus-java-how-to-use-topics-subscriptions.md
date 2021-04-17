---
title: Použití Azure Service Bus témata a předplatných v jazyce Java (Azure-zasílání zpráv – ServiceBus)
description: V tomto rychlém startu napíšete kód Java pomocí balíčku Azure-Messaging-ServiceBus k posílání zpráv do Azure Service Busho tématu a potom dostanete zprávy z odběrů do tohoto tématu.
ms.date: 02/13/2021
ms.topic: quickstart
ms.devlang: Java
ms.custom:
- mode-api
ms.openlocfilehash: 6fe0a3a91ebbd5b6daced95494b8eaa5b7db0c46
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536383"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-java"></a>Odeslání zpráv do Azure Service Bus tématu a příjem zpráv z předplatných do tématu (Java)
V tomto rychlém startu napíšete kód Java pomocí balíčku Azure-Messaging-ServiceBus k posílání zpráv do Azure Service Busho tématu a potom dostanete zprávy z odběrů do tohoto tématu.

> [!IMPORTANT]
> V tomto rychlém startu se používá nový balíček Azure-Messaging-ServiceBus. Rychlý Start, který používá starý balíček Azure-ServiceBus, najdete v tématu [posílání a přijímání zpráv pomocí Azure-ServiceBus](service-bus-java-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Požadavky

- Předplatné Azure. K dokončení tohoto kurzu potřebujete mít účet Azure. Můžete aktivovat výhody pro [předplatitele sady Visual Studio nebo MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Postupujte podle kroků v [rychlém startu: pomocí Azure Portal vytvořte Service Bus téma a odběry tématu](service-bus-quickstart-topics-subscriptions-portal.md). Poznamenejte si připojovací řetězec, název tématu a název předplatného. Pro tento rychlý Start budete používat jenom jedno předplatné. 
- Nainstalujte [sadu Azure SDK pro jazyk Java][Azure SDK for Java]. Pokud používáte zatmění, můžete nainstalovat [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] , který obsahuje sadu Azure SDK pro jazyk Java. Pak můžete přidat **knihovny Microsoft Azure pro jazyk Java** do projektu. Pokud používáte IntelliJ, přečtěte si téma [instalace Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-topic"></a>Odeslání zprávy do tématu
V této části vytvoříte projekt konzoly Java a přidáte kód pro odesílání zpráv do tématu, které jste vytvořili. 

### <a name="create-a-java-console-project"></a>Vytvoření projektu konzoly Java
Vytvořte projekt Java pomocí zatmění nebo nástroje podle vašeho výběru. 

### <a name="configure-your-application-to-use-service-bus"></a>Konfigurace aplikace pro použití Service Bus
Přidejte odkazy na knihovny Azure Core a Azure Service Bus. 

Pokud používáte zatmění a vytvoříte konzolovou aplikaci Java, převeďte projekt Java na Maven: klikněte pravým tlačítkem myši na projekt v okně **Průzkumníka balíčku** , vyberte **Konfigurovat**  ->  **převod na Maven projekt**. Pak přidejte závislosti do těchto dvou knihoven, jak je znázorněno v následujícím příkladu.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.myorg.sbusquickstarts</groupId>
    <artifactId>sbustopicqs</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <build>
        <sourceDirectory>src</sourceDirectory>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <release>15</release>
                </configuration>
            </plugin>
        </plugins>
    </build>
    <dependencies>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-core</artifactId>
            <version>1.13.0</version>
        </dependency>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-messaging-servicebus</artifactId>
            <version>7.0.2</version>
        </dependency>
    </dependencies>
</project>
```

### <a name="add-code-to-send-messages-to-the-topic"></a>Přidání kódu pro odesílání zpráv do tématu
1. Přidejte následující `import` příkazy do tématu souboru Java. 

    ```java
    import com.azure.messaging.servicebus.*;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. Ve třídě Definujte proměnné pro uložení připojovacího řetězce a názvu tématu, jak je znázorněno níže: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String topicName = "<TOPIC NAME>";    
    static String subName = "<SUBSCRIPTION NAME>";
    ```

    Nahraďte `<NAMESPACE CONNECTION STRING>` připojovacím řetězcem k vašemu oboru názvů Service Bus. A nahraďte `<TOPIC NAME>` názvem tématu.
3. Přidejte metodu s názvem `sendMessage` ve třídě pro odeslání jedné zprávy do tématu. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();
        
        // send one message to the topic
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the topic: " + topicName);        
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
1. Přidejte metodu s názvem metoda `sendMessageBatch` pro odeslání zpráv do tématu, které jste vytvořili. Tato metoda vytvoří `ServiceBusSenderClient` pro téma, vyvolá `createMessages` metodu pro získání seznamu zpráv, připraví jednu nebo více dávek a odešle dávky do tématu. 

    ```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the topic 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
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
            System.out.println("Sent a batch of messages to the topic: " + topicName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }

        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
        }

        //close the client
        senderClient.close();
    }
    ```

## <a name="receive-messages-from-a-subscription"></a>Přijímání zpráv z předplatného
V této části přidáte kód pro načtení zpráv z předplatného do tématu. 

1. Přidejte metodu pojmenovanou `receiveMessages` pro příjem zpráv z předplatného. Tato metoda vytvoří `ServiceBusProcessorClient` pro odběr zadáním obslužné rutiny pro zpracování zpráv a jiné pro zpracování chyb. Potom spustí procesor, počká pár sekund, vytiskne přijaté zprávy a pak procesor zastaví a ukončí.

    > [!IMPORTANT]
    > `ServiceBusTopicTest`V kódu nahraďte `ServiceBusTopicTest::processMessage` názvem vaší třídy. 

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        CountDownLatch countdownLatch = new CountDownLatch(1);

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .topicName(topicName)
            .subscriptionName(subName)
            .processMessage(ServiceBusTopicTest::processMessage)
            .processError(context -> processError(context, countdownLatch))
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }  
    ```
2. Přidejte `processMessage` metodu pro zpracování zprávy přijaté z předplatného Service Bus. 

    ```java
    private static void processMessage(ServiceBusReceivedMessageContext context) {
        ServiceBusReceivedMessage message = context.getMessage();
        System.out.printf("Processing message. Session: %s, Sequence #: %s. Contents: %s%n", message.getMessageId(),
            message.getSequenceNumber(), message.getBody());
    }    
    ```
3. Přidejte `processError` metodu pro zpracování chybových zpráv.

    ```java
    private static void processError(ServiceBusErrorContext context, CountDownLatch countdownLatch) {
        System.out.printf("Error when receiving messages from namespace: '%s'. Entity: '%s'%n",
            context.getFullyQualifiedNamespace(), context.getEntityPath());

        if (!(context.getException() instanceof ServiceBusException)) {
            System.out.printf("Non-ServiceBusException occurred: %s%n", context.getException());
            return;
        }

        ServiceBusException exception = (ServiceBusException) context.getException();
        ServiceBusFailureReason reason = exception.getReason();

        if (reason == ServiceBusFailureReason.MESSAGING_ENTITY_DISABLED
            || reason == ServiceBusFailureReason.MESSAGING_ENTITY_NOT_FOUND
            || reason == ServiceBusFailureReason.UNAUTHORIZED) {
            System.out.printf("An unrecoverable error occurred. Stopping processing with reason %s: %s%n",
                reason, exception.getMessage());

            countdownLatch.countDown();
        } else if (reason == ServiceBusFailureReason.MESSAGE_LOCK_LOST) {
            System.out.printf("Message lock lost for message: %s%n", context.getException());
        } else if (reason == ServiceBusFailureReason.SERVICE_BUSY) {
            try {
                // Choosing an arbitrary amount of time to wait until trying again.
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                System.err.println("Unable to sleep for period of time");
            }
        } else {
            System.out.printf("Error source %s, reason %s, message: %s%n", context.getErrorSource(),
                reason, context.getException());
        }
    }  
    ```
1. Aktualizujte `main` metodu na vyvolání `sendMessage` , `sendMessageBatch` metody a `receiveMessages` k vyvolání `InterruptedException` .     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Spuštění aplikace
Spusťte program a podívejte se na výstup podobný následujícímu výstupu:

```console
Sent a single message to the topic: mytopic
Sent a batch of messages to the topic: mytopic
Starting the processor
Processing message. Session: e0102f5fbaf646988a2f4b65f7d32385, Sequence #: 1. Contents: Hello, World!
Processing message. Session: 3e991e232ca248f2bc332caa8034bed9, Sequence #: 2. Contents: First message
Processing message. Session: 56d3a9ea7df446f8a2944ee72cca4ea0, Sequence #: 3. Contents: Second message
Processing message. Session: 7bd3bd3e966a40ebbc9b29b082da14bb, Sequence #: 4. Contents: Third message
```

Na stránce **Přehled** pro obor názvů Service Bus v Azure Portal můžete zobrazit počet **příchozích** a **odchozích** zpráv. Může se stát, že budete muset počkat na minutu nebo si aktualizovat stránku, aby se zobrazily nejnovější hodnoty. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Počet příchozích a odchozích zpráv" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Přepněte na kartu **témata** v prostředním podokně a vyberte téma, kde se zobrazí stránka **Service Bus téma** pro vaše téma. Na této stránce byste měli vidět čtyři příchozí a čtyři odchozí zprávy v grafu **zprávy** . 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Příchozí a odchozí zprávy" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png":::

Pokud Odkomentujete `receiveMessages` volání v `main` metodě a znovu spustíte aplikaci, zobrazí se na stránce **Service Bus téma** 8 příchozích zpráv (4 nové), ale čtyři odchozí zprávy. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Stránka aktualizovaného tématu" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png":::

Po výběru předplatného na této stránce se dostanete na stránku **Service Bus předplatné** . Na této stránce můžete zobrazit počet aktivních zpráv, počet nedoručených zpráv a další. V tomto příkladu jsou k dispozici čtyři aktivní zprávy, které příjemce ještě nepřijal. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Počet aktivních zpráv" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png":::

## <a name="next-steps"></a>Další kroky
Podívejte se na následující dokumentaci a ukázky:

- [Klientská knihovna Azure Service Bus pro Java – soubor Readme](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Ukázky na GitHubu](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Referenční dokumentace k rozhraní API v Javě](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)


[Azure SDK for Java]: /java/api/overview/azure/
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
