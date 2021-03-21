---
title: Použití Azure Service Bus témata a předplatných s Pythonem Azure-ServiceBus Package verze 7.0.0
description: V tomto článku se dozvíte, jak používat Python k posílání zpráv do tématu a přijímání zpráv z předplatného.
documentationcenter: python
author: spelluru
ms.devlang: python
ms.topic: quickstart
ms.date: 11/18/2020
ms.author: spelluru
ms.custom: devx-track-python
ms.openlocfilehash: 4eba3ea055e78888d482927fa6eed5c7d41fa0ba
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98630043"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-python"></a>Odeslání zpráv do Azure Service Bus tématu a příjem zpráv z předplatných do tématu (Python)
V tomto článku se dozvíte, jak pomocí Pythonu odesílat zprávy Service Bus tématu a přijímat zprávy z odběru tématu. 

## <a name="prerequisites"></a>Předpoklady
- Předplatné Azure. Můžete aktivovat výhody pro [předplatitele sady Visual Studio nebo MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) nebo si zaregistrovat [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Postupujte podle kroků v [rychlém startu: pomocí Azure Portal vytvořte Service Bus téma a odběry tématu](service-bus-quickstart-topics-subscriptions-portal.md). Poznamenejte si připojovací řetězec, název tématu a název předplatného. Pro tento rychlý Start budete používat jenom jedno předplatné. 
- Python 2,7 nebo vyšší, s nainstalovaným balíčkem [Azure Python SDK] [balíček Azure Python]. Další informace najdete v příručce pro [instalaci Pythonu](/azure/developer/python/azure-sdk-install).

## <a name="send-messages-to-a-topic"></a>Odeslání zprávy do tématu

1. Přidejte následující příkaz import. 

    ```python
    from azure.servicebus import ServiceBusClient, ServiceBusMessage
    ```
2. Přidejte následující konstanty. 

    ```python
    CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
    TOPIC_NAME = "<TOPIC NAME>"
    SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"
    ```
    
    > [!IMPORTANT]
    > - Nahraďte `<NAMESPACE CONNECTION STRING>` připojovacím řetězcem pro váš obor názvů.
    > - Nahraďte `<TOPIC NAME>` názvem tématu.
    > - Nahraďte `<SUBSCRIPTION NAME>` názvem předplatného tématu. 
3. Přidejte metodu pro odeslání jedné zprávy.

    ```python
    def send_single_message(sender):
        # create a Service Bus message
        message = ServiceBusMessage("Single Message")
        # send the message to the topic
        sender.send_messages(message)
        print("Sent a single message")
    ```

    Odesilatel je objekt, který funguje jako klient pro téma, které jste vytvořili. Později ho vytvoříte a odešlete jako argument této funkce. 
4. Přidejte metodu pro odeslání seznamu zpráv.

    ```python
    def send_a_list_of_messages(sender):
        # create a list of messages
        messages = [ServiceBusMessage("Message in list") for _ in range(5)]
        # send the list of messages to the topic
        sender.send_messages(messages)
        print("Sent a list of 5 messages")
    ```
5. Přidejte metodu pro odeslání dávky zpráv.

    ```python
    def send_batch_message(sender):
        # create a batch of messages
        batch_message = sender.create_message_batch()
        for _ in range(10):
            try:
                # add a message to the batch
                batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
            except ValueError:
                # ServiceBusMessageBatch object reaches max_size.
                # New ServiceBusMessageBatch object can be created here to send more data.
                break
        # send the batch of messages to the topic
        sender.send_messages(batch_message)
        print("Sent a batch of 10 messages")
    ```
6. Vytvořte klienta Service Bus a pak objekt odesílatele tématu, který odešle zprávy.

    ```python
    # create a Service Bus client using the connection string
    servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)
    with servicebus_client:
        # get a Topic Sender object to send messages to the topic
        sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
        with sender:
            # send one message        
            send_single_message(sender)
            # send a list of messages
            send_a_list_of_messages(sender)
            # send a batch of messages
            send_batch_message(sender)
    
    print("Done sending messages")
    print("-----------------------")
    ```
 
## <a name="receive-messages-from-a-subscription"></a>Přijímání zpráv z předplatného
Po příkazu Print přidejte následující kód. Tento kód průběžně přijímá nové zprávy, dokud neobdrží žádné nové zprávy po dobu 5 ( `max_wait_time` ) sekund. 

```python
with servicebus_client:
    # get the Subscription Receiver object for the subscription    
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            # complete the message so that the message is removed from the subscription
            receiver.complete_message(msg)
```

## <a name="full-code"></a>Úplný kód

```python
from azure.servicebus import ServiceBusClient, ServiceBusMessage

CONNECTION_STR = "<NAMESPACE CONNECTION STRING>"
TOPIC_NAME = "<TOPIC NAME>"
SUBSCRIPTION_NAME = "<SUBSCRIPTION NAME>"

def send_single_message(sender):
    message = ServiceBusMessage("Single Message")
    sender.send_messages(message)
    print("Sent a single message")

def send_a_list_of_messages(sender):
    messages = [ServiceBusMessage("Message in list") for _ in range(5)]
    sender.send_messages(messages)
    print("Sent a list of 5 messages")

def send_batch_message(sender):
    batch_message = sender.create_message_batch()
    for _ in range(10):
        try:
            batch_message.add_message(ServiceBusMessage("Message inside a ServiceBusMessageBatch"))
        except ValueError:
            # ServiceBusMessageBatch object reaches max_size.
            # New ServiceBusMessageBatch object can be created here to send more data.
            break
    sender.send_messages(batch_message)
    print("Sent a batch of 10 messages")

servicebus_client = ServiceBusClient.from_connection_string(conn_str=CONNECTION_STR, logging_enable=True)

with servicebus_client:
    sender = servicebus_client.get_topic_sender(topic_name=TOPIC_NAME)
    with sender:
        send_single_message(sender)
        send_a_list_of_messages(sender)
        send_batch_message(sender)

print("Done sending messages")
print("-----------------------")

with servicebus_client:
    receiver = servicebus_client.get_subscription_receiver(topic_name=TOPIC_NAME, subscription_name=SUBSCRIPTION_NAME, max_wait_time=5)
    with receiver:
        for msg in receiver:
            print("Received: " + str(msg))
            receiver.complete_message(msg)
```

## <a name="run-the-app"></a>Spuštění aplikace
Při spuštění aplikace by se měl zobrazit následující výstup: 

```console
Sent a single message
Sent a list of 5 messages
Sent a batch of 10 messages
Done sending messages
-----------------------
Received: Single Message
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message in list
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
Received: Message inside a ServiceBusMessageBatch
```

V Azure Portal přejděte do svého oboru názvů Service Bus. Na stránce **Přehled** ověřte, že počet **příchozích** a **odchozích** zpráv je 16. Pokud se počty nezobrazuje, aktualizujte stránku po několik minut. 

:::image type="content" source="./media/service-bus-python-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Počet příchozích a odchozích zpráv":::

Vyberte téma v dolním podokně, kde se zobrazí stránka **Service Bus téma** pro vaše téma. Na této stránce byste měli vidět tři příchozí a tři odchozí zprávy v grafu **zprávy** . 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Příchozí a odchozí zprávy":::

Po výběru předplatného na této stránce se dostanete na stránku **Service Bus předplatné** . Na této stránce můžete zobrazit počet aktivních zpráv, počet nedoručených zpráv a další. V tomto příkladu byly přijaty všechny zprávy, takže počet aktivních zpráv je nula. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Počet aktivních zpráv":::

Pokud Odkomentujete kód pro příjem, zobrazí se počet aktivních zpráv jako 16. 

:::image type="content" source="./media/service-bus-python-how-to-use-topics-subscriptions/active-message-count-2.png" alt-text="Počet aktivních zpráv – bez příjmu":::

## <a name="next-steps"></a>Další kroky
Podívejte se na následující dokumentaci a ukázky: 

- [Klientská knihovna Azure Service Bus pro Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus)
- [Ukázky](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/servicebus/azure-servicebus/samples). 
    - Složka **sync_samples** obsahuje ukázky, které ukazují, jak pracovat s Service Bus synchronním způsobem. V tomto rychlém startu jste použili tuto metodu. 
    - Složka **async_samples** obsahuje ukázky, které ukazují, jak pracovat s Service Bus asynchronním způsobem. 
- [Referenční dokumentace k Azure-ServiceBus](/python/api/azure-servicebus/azure.servicebus?preserve-view=true)