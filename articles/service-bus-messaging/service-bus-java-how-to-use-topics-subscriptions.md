---
title: Použití Azure Service Bus témata a předplatných v jazyce Java (Azure-zasílání zpráv – ServiceBus)
description: V tomto rychlém startu napíšete kód Java pomocí balíčku Azure-Messaging-ServiceBus k posílání zpráv do Azure Service Busho tématu a potom dostanete zprávy z odběrů do tohoto tématu.
ms.devlang: Java
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: cd95b84c4b55279b40f95eef65cb2490a55d1780
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97630237"
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
Přidejte odkaz na knihovnu Azure Service Bus. Klientská knihovna Java pro Service Bus je k dispozici v [centrálním úložišti Maven](https://search.maven.org/search?q=a:azure-messaging-servicebus). Na tuto knihovnu můžete odkazovat pomocí následující deklarace závislosti v rámci souboru projektu Maven:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-servicebus</artifactId>
    <version>7.0.0</version>
</dependency>
```

### <a name="add-code-to-send-messages-to-the-topic"></a>Přidání kódu pro odesílání zpráv do tématu
1. Přidejte následující `import` příkazy do tématu souboru Java. 

    ```java
    import com.azure.messaging.servicebus.*;
    import com.azure.messaging.servicebus.models.*;
    import java.util.concurrent.TimeUnit;
    import java.util.function.Consumer;
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

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        // Consumer that processes a single message received from Service Bus
        Consumer<ServiceBusReceivedMessageContext> messageProcessor = context -> {
            ServiceBusReceivedMessage message = context.getMessage();
            System.out.println("Received message: " + message.getBody().toString() + " from the subscription: " + subName);
        };

        // Consumer that handles any errors that occur when receiving messages
        Consumer<Throwable> errorHandler = throwable -> {
            System.out.println("Error when receiving messages: " + throwable.getMessage());
            if (throwable instanceof ServiceBusReceiverException) {
                ServiceBusReceiverException serviceBusReceiverException = (ServiceBusReceiverException) throwable;
                System.out.println("Error source: " + serviceBusReceiverException.getErrorSource());
            }
        };

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .topicName(topicName)
            .subscriptionName(subName)
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
Spusťte program a podívejte se na výstup podobný následujícímu výstupu:

```console
Sent a batch of messages to the topic: mytopic
Starting the processor
Received message: First message from the subscription: mysub
Received message: Second message from the subscription: mysub
Received message: Third message from the subscription: mysub
Stopping and closing the processor
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
- [Ukázky na GitHubu](https://docs.microsoft.com/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Referenční dokumentace k rozhraní API v Javě](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)


[Azure SDK for Java]: /java/api/overview/azure/
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
