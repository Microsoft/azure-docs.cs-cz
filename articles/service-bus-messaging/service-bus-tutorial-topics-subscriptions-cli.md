---
title: Kurz – Aktualizace sortimentu zásob v maloobchodě s využitím kanálů pro publikování/odběr a filtrů témat pomocí Azure CLI | Microsoft Docs
description: V tomto kurzu se naučíte, jak odesílat a přijímat zprávy z tématu a odběru a jak přidávat a používat pravidla filtrů pomocí rozhraní Azure CLI
services: service-bus-messaging
author: spelluru
manager: timlt
ms.author: spelluru
ms.date: 05/22/2018
ms.topic: tutorial
ms.service: service-bus-messaging
ms.custom: mvc
ms.openlocfilehash: b68b3f311312a952ab55c15083ff5ed562443be2
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43701999"
---
# <a name="tutorial-update-inventory-using-cli-and-topicssubscriptions"></a>Kurz: Aktualizace zásob pomocí rozhraní CLI a témat/odběrů

Microsoft Azure Service Bus je víceklientská cloudová služba pro zasílání zpráv, která odesílá informace mezi aplikacemi a službami. Asynchronní operace umožňují flexibilní zprostředkované zasílání zpráv a také strukturované zasílání zpráv typu FIFO (first-in-first-out) a funkce pro publikování a přihlášení k odběru. Tento kurz ukazuje, jak můžete témata a odběry ve službě Service Bus využít ve scénáři aktualizace maloobchodních zásob. Pracuje se při tom s kanály publikování a přihlášení k odběru s využitím rozhraní Azure CLI a jazyka Java.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Pomocí rozhraní Azure CLI vytvořit téma služby Service Bus a k němu jeden nebo více odběrů
> * Přidávat filtry témat pomocí rozhraní Azure CLI
> * Vytvořit dvě zprávy s různým obsahem
> * Odeslat zprávy a ověřit, že dorazily do očekávaných odběrů
> * Přijímat zprávy z odběrů

V tomto scénáři pracujeme s příkladem aktualizace sortimentu zásob pro několik prodejen. Každá prodejna nebo sada prodejen v našem příkladu přijímá zprávy s pokyny, jak konkrétně má aktualizovat sortiment. Tento kurz vysvětluje, jak tento scénář využívající odběry a filtry implementujete. Nejprve vytvoříte téma se třemi odběry, přidáte pravidla a filtry a potom z tématu a odběrů odešlete zprávy a přijmete je.

![téma](./media/service-bus-tutorial-topics-subscriptions-cli/about-service-bus-topic.png)

Pokud ještě nemáte předplatné Azure, můžete si vytvořit [bezplatný účet][] před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K vývoji aplikace funkcí Service Bus pomocí Javy potřebujete mít nainstalované následující součásti:

- Nejnovější verzi sady [Java Development Kit](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
- [Azure CLI](https://docs.microsoft.com/cli/azure)
- [Apache Maven](https://maven.apache.org) verze 3.0 nebo novější.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Pokud se rozhodnete nainstalovat a místně používat rozhraní příkazového řádku, musíte mít Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="service-bus-topics-and-subscriptions"></a>Témata a odběry služby Service Bus

Každý [odběr tématu](service-bus-messaging-overview.md#topics) může přijímat kopie všech zpráv. Témata jsou co do protokolu a sémantiky plně kompatibilní s frontami služby Service Bus. Témata služby Service Bus podporují širokou škálu pravidel pro výběr s podmínkami filtrů, včetně volitelných akcí, kterými se nastavují nebo upravují vlastnosti zprávy. Při každé shodě s pravidlem se vytvoří zpráva. Další informace o pravidlech, filtrech a akcích získáte pomocí tohoto [odkazu](topic-filters.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Po instalaci rozhraní příkazového řádku otevřete příkazový řádek a pomocí následujících příkazů se přihlaste k Azure. Tyto kroky nejsou nutné, pokud používáte cloudové prostředí:

1. Pokud používáte místní rozhraní Azure CLI, přihlaste se k Azure spuštěním následujícího příkazu. Tento krok přihlášení není potřeba, pokud tyto příkazy spouštíte ve službě Cloud Shell:

   ```azurecli-interactive
   az login
   ```

2. Nastavte aktuální kontext předplatného na předplatné Azure, které chcete použít:

   ```azurecli-interactive
   az account set --subscription Azure_subscription_name
   ```

## <a name="use-cli-to-provision-resources"></a>Zřízení prostředků pomocí rozhraní CLI

Pomocí následujících příkazů zřiďte prostředky služby Service Bus. Nezapomeňte všechny zástupné symboly nahradit příslušnými hodnotami:

```azurecli-interactive
# Create a resource group
az group create --name myResourcegroup --location eastus

# Create a Service Bus messaging namespace with a unique name
namespaceName=myNameSpace$RANDOM
az servicebus namespace create \
   --resource-group myResourceGroup \
   --name $namespaceName \
   --location eastus

# Create a Service Bus topic
az servicebus topic create --resource-group myResourceGroup \
   --namespace-name $namespaceName \
   --name myTopic

# Create subscription 1 to the topic
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S1

# Create filter 1 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MyFilter --filter-sql-expression "StoreId IN ('Store1','Store2','Store3')"

# Create filter 2 - use custom properties
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S1 --name MySecondFilter --filter-sql-expression "StoreId = 'Store4'"

# Create subscription 2
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S2

# Create filter 3 - use message header properties via IN list and 
# combine with custom properties.
az servicebus rule create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --subscription-name S2 --name MyFilter --filter-sql-expression "sys.To IN ('Store5','Store6','Store7') OR StoreId = 'Store8'"

# Create subscription 3
az servicebus subscription create --resource-group myResourceGroup --namespace-name $namespaceName --topic-name myTopic --name S3

# Create filter 4 - Get everything except messages for subscription 1 and 2. 
# Also modify and add an action; in this case set the label to a specified value. 
# Assume those stores might not be part of your main store, so you only add 
# specific items to them. For that, you flag them specifically.
az servicebus rule create --resource-group DemoGroup --namespace-name DemoNamespaceSB --topic-name tutorialtest1
 --subscription-name S3 --name MyFilter --filter-sql-expression "sys.To NOT IN ('Store1','Store2','Store3','Store4','Sto
re5','Store6','Store7','Store8') OR StoreId NOT IN ('Store1','Store2','Store3','Store4','Store5','Store6','Store7','Stor
e8')" --action-sql-expression "SET sys.Label = 'SalesEvent'"

# Get the connection string
connectionString=$(az servicebus namespace authorization-rule keys list \
   --resource-group myResourceGroup \
   --namespace-name  $namespaceName \
   --name RootManageSharedAccessKey \
   --query primaryConnectionString --output tsv)
```

Po dokončení všech příkazů zkopírujte vybraný připojovací řetězec a název fronty a vložte je do dočasného umístění, třeba do Poznámkového bloku. Budete je potřebovat v dalším kroku.

## <a name="create-filter-rules-on-subscriptions"></a>Vytvoření pravidel filtrů u odběrů

Pokud zřídíte obor názvů a téma s odběry a máte potřebná pověření, můžete u odběrů vytvořit pravidla filtrů a poté odesílat a přijímat zprávy. Kód můžete zkontrolovat v [této složce s ukázkami na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/Java/quickstarts-and-tutorials/tutorial-topics-subscriptions-filters-java/src/main/java/com/microsoft/azure/).

## <a name="send-and-receive-messages"></a>Odesílání a příjem zpráv

1. Přesvědčte se, že je služba Cloud Shell spuštěná a zobrazuje příkazový řádek Bash.

2. Pomocí následujícího příkazu naklonujte [úložiště Service Bus na GitHubu](https://github.com/Azure/azure-service-bus/):

   ```shell
   git clone https://github.com/Azure/azure-service-bus.git
   ```

2. Přejděte do složky s ukázkou `azure-service-bus/samples/Java/quickstarts-and-tutorials/quickstart-java/tutorial-topics-subscriptions-filters-java`. Připomínáme, že v prostředí Bash se v příkazech rozlišují velká a malá písmena a cesty je třeba oddělovat pomocí lomítek.

3. Proveďte následující příkaz, kterým vytvoříte aplikaci:
   
   ```shell
   mvn clean package -DskipTests
   ```
4. Program spustíte následujícím příkazem. Nezapomeňte zástupné symboly nahradit připojovacím řetězcem a názvem tématu, které jste získali v předchozím kroku:

   ```shell
  java -jar .\target\tutorial-topics-subscriptions-filters-1.0.0-jar-with-dependencies.jar -c "myConnectionString" -t "myTopicName"
   ```

   Sledujte, jak se do tématu odešle deset zpráv a jak se následně z jednotlivých odběrů přijmou:

   ![výstup programu](./media/service-bus-tutorial-topics-subscriptions-cli/service-bus-tutorial-topics-subscriptions-cli.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Spuštěním následujícího příkazu odeberte skupinu prostředků, obor názvů a všechny související prostředky:

```azurecli-interactive
az group delete --resource-group my-resourcegroup
```

## <a name="understand-the-sample-code"></a>Vysvětlení vzorového kódu

Tato část obsahuje další podrobnosti o chování ukázkového kódu.

### <a name="get-connection-string-and-queue"></a>Získání připojovacího řetězce a fronty

Nejprve kód deklaruje sadu proměnných, které řídí další provádění programu:

```java
    public String ConnectionString = null;
    public String TopicName = null;
    static final String[] Subscriptions = {"S1","S2","S3"};
    static final String[] Store = {"Store1","Store2","Store3","Store4","Store5","Store6","Store7","Store8","Store9","Store10"};
    static final String SysField = "sys.To";
    static final String CustomField = "StoreId";    
    int NrOfMessagesPerStore = 1; // Send at least 1.
```

Připojovací řetězec a název tématu jsou jediné hodnoty přidané prostřednictvím parametrů příkazového řádku a předané do proměnné `main()`. Samotné provedení kódu se aktivuje v metodě `run()` a odešle, poté přijímá zprávy z tématu:

```java
public static void main(String[] args) {
        TutorialTopicsSubscriptionsFilters app = new TutorialTopicsSubscriptionsFilters();
        try {
            app.runApp(args);
            app.run();
        } catch (Exception e) {
            System.out.printf("%s", e.toString());
        }
        System.exit(0);
    }
}

public void run() throws Exception {
        // Send sample messages.
        this.sendMessagesToTopic();

        // Receive messages from subscriptions.
        this.receiveAllMessages();
}
```

### <a name="create-topic-client-to-send-messages"></a>Vytvoření klienta tématu k odesílání zpráv

K odesílání a přijímání zpráv vytvoří metoda `sendMessagesToTopic()` instanci klienta tématu, která používá připojovací řetězec a název tématu, a potom zavolá další metodu, která zprávy odešle:

```java
public void sendMessagesToTopic() throws Exception, ServiceBusException {
         // Create client for the topic.
        TopicClient topicClient = new TopicClient(new ConnectionStringBuilder(ConnectionString, TopicName));

        // Create a message sender from the topic client.

        System.out.printf("\nSending orders to topic.\n");

        // Now we can start sending orders.
        CompletableFuture.allOf(
                SendOrders(topicClient,Store[0]),
                SendOrders(topicClient,Store[1]),
                SendOrders(topicClient,Store[2]),
                SendOrders(topicClient,Store[3]),
                SendOrders(topicClient,Store[4]),
                SendOrders(topicClient,Store[5]),
                SendOrders(topicClient,Store[6]),
                SendOrders(topicClient,Store[7]),
                SendOrders(topicClient,Store[8]),
                SendOrders(topicClient,Store[9])                
        ).join();

        System.out.printf("\nAll messages sent.\n");
    }

     public CompletableFuture<Void> SendOrders(TopicClient topicClient, String store) throws Exception {

        for(int i = 0;i<NrOfMessagesPerStore;i++) {
            Random r = new Random();
            final Item item = new Item(r.nextInt(5),r.nextInt(5),r.nextInt(5));         
            IMessage message = new Message(GSON.toJson(item,Item.class).getBytes(UTF_8)); 
            // We always set the Sent to field
            message.setTo(store);    
            final String StoreId = store;
            Double priceToString = item.getPrice();
            final String priceForPut = priceToString.toString();
            message.setProperties(new HashMap<String, String>() {{
                // Additionally we add a customer store field. In reality you would use sys.To or a customer property but not both. 
                // This is just for demo purposes.
                put("StoreId", StoreId);
                // Adding more potential filter / rule and action able fields
                put("Price", priceForPut);
                put("Color", item.getColor());
                put("Category", item.getItemCategory());
            }});
                        
            System.out.printf("Sent order to Store %s. Price=%f, Color=%s, Category=%s\n", StoreId, item.getPrice(), item.getColor(), item.getItemCategory());            
            topicClient.sendAsync(message);
        }
               
        return new CompletableFuture().completedFuture(null);         
    }
```

### <a name="receive-messages-from-the-individual-subscriptions"></a>Příjem zpráv z jednotlivých odběrů

Metoda `receiveAllMessages()` volá metodu `receiveAllMessageFromSubscription()`, která poté pro každé volání vytvoří klienta odběru a přijímá zprávy z jednotlivých odběrů:

```java
public void receiveAllMessages() throws Exception {     
    System.out.printf("\nStart Receiving Messages.\n");
    
    CompletableFuture.allOf(
            receiveAllMessageFromSubscription(Subscriptions[0]),
            receiveAllMessageFromSubscription(Subscriptions[1]),
            receiveAllMessageFromSubscription(Subscriptions[2]) 
            ).join();
}

public CompletableFuture<Void> receiveAllMessageFromSubscription(String subscription) throws Exception {
        
        int receivedMessages = 0;

        // Create subscription client.
        IMessageReceiver subscriptionClient = ClientFactory.createMessageReceiverFromConnectionStringBuilder(new ConnectionStringBuilder(ConnectionString, TopicName+"/subscriptions/"+ subscription), ReceiveMode.PEEKLOCK);

        // Create a receiver from the subscription client and receive all messages.
        System.out.printf("\nReceiving messages from subscription %s.\n\n", subscription);

        while (true)
        {
            // This will make the connection wait for N seconds if new messages are available. 
            // If no additional messages come we close the connection. This can also be used to realize long polling.
            // In case of long polling you would obviously set it more to e.g. 60 seconds.
            IMessage receivedMessage = subscriptionClient.receive(Duration.ofSeconds(1));
            if (receivedMessage != null)
            {
                if ( receivedMessage.getProperties() != null ) {                                                                                
                    System.out.printf("StoreId=%s\n", receivedMessage.getProperties().get("StoreId"));                                                                                          
                    
                    // Show the label modified by the rule action
                    if(receivedMessage.getLabel() != null)
                        System.out.printf("Label=%s\n", receivedMessage.getLabel());   
                }
                
                byte[] body = receivedMessage.getBody();
                Item theItem = GSON.fromJson(new String(body, UTF_8), Item.class);
                System.out.printf("Item data. Price=%f, Color=%s, Category=%s\n", theItem.getPrice(), theItem.getColor(), theItem.getItemCategory());                          
                
                subscriptionClient.complete(receivedMessage.getLockToken());
                receivedMessages++;
            }
            else
            {
                // No more messages to receive.
                subscriptionClient.close();
                break;
            }
        }
        System.out.printf("\nReceived %s messages from subscription %s.\n", receivedMessages, subscription);
        
        return new CompletableFuture().completedFuture(null);
}
```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zřídili prostředky pomocí rozhraní Azure CLI a poté odesílali a přijímali zprávy z tématu služby Service Bus a jeho odběrů. Naučili jste se tyto postupy:

> [!div class="checklist"]
> * Pomocí portálu Azure Portal vytvořit téma služby Service Bus a k němu jeden nebo více odběrů
> * Přidávat filtry témat pomocí kódu .NET
> * Vytvořit dvě zprávy s různým obsahem
> * Odeslat zprávy a ověřit, že dorazily do očekávaných odběrů
> * Přijímat zprávy z odběrů

Pokud vás zajímají další příklady odesílání a přijímání zpráv, pomůžou vám začít [ukázky služby Service Bus na GitHubu](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/GettingStarted).

Přejděte k dalšímu kurzu, kde se dozvíte více o možnostech publikování a přihlášení k odběru ve službě Service Bus.

> [!div class="nextstepaction"]
> [Aktualizace zásob pomocí prostředí PowerShell a témat/odběrů](service-bus-tutorial-topics-subscriptions-portal.md)

[bezplatný účet]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
[fully qualified domain name]: https://wikipedia.org/wiki/Fully_qualified_domain_name
[Install Azure CLI 2.0]: /cli/azure/install-azure-cli
[az group create]: /cli/azure/group#az_group_create