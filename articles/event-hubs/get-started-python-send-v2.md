---
title: Odesílání nebo přijímání událostí z Azure Event Hubs pomocí Pythonu (nejnovější)
description: Tento článek poskytuje návod pro vytvoření aplikace Pythonu, která odesílá nebo přijímá události do/z Azure Event Hubs pomocí nejnovějšího balíčku azure-eventhub verze 5.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 02/11/2020
ms.author: spelluru
ms.openlocfilehash: 352ff91bf26c7ff4f6945431fe6e1357f030e1db
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477531"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>Odesílání událostí do nebo přijímání událostí z centra událostí pomocí Pythonu (azure-eventhub verze 5)
Tento rychlý start ukazuje, jak odesílat události do centra událostí a přijímat je pomocí balíčku **Pythonu azure-eventhub verze 5.**

> [!IMPORTANT]
> Tento rychlý start používá nejnovější balíček azure-eventhub verze 5. Pro rychlý start, který používá starý balíček azure-eventhub verze 1, najdete v tématu [Odesílání a přijímání událostí pomocí azure-eventhub verze 1](event-hubs-python-get-started-send.md). 

## <a name="prerequisites"></a>Požadavky
Pokud s Azure Event Hubs tenete nováčkem, přečtěte si [téma Přehled centra událostí,](event-hubs-about.md) než začnete tento rychlý start. 

Chcete-li tento rychlý start dokončit, potřebujete následující požadavky:

- **Předplatné Microsoft Azure**. Pokud chcete používat služby Azure, včetně Azure Event Hubs, potřebujete předplatné.  Pokud nemáte existující účet Azure, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/) nebo použít výhody předplatitele MSDN při vytváření [účtu](https://azure.microsoft.com).
- Python 2.7 nebo 3.5 nebo novější, s nainstalovaným a aktualizovaným PIPEm.
- Balíček Pythonu pro centra událostí. 

    Chcete-li balíček nainstalovat, spusťte tento příkaz v příkazovém řádku, který má v cestě Python:

    ```cmd
    pip install azure-eventhub
    ```

    Nainstalujte následující balíček pro příjem událostí pomocí úložiště objektů blob Azure jako úložiště kontrolních bodů:

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```
- **Vytvořte obor názvů Event Hubs a centrum událostí**. Prvním krokem je použití [portálu Azure](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Chcete-li vytvořit obor názvů a centrum událostí, postupujte podle postupu v [tomto článku](event-hubs-create.md). Potom získejte **připojovací řetězec pro obor názvů Event Hubs** podle následujících pokynů z článku: [Získat připojovací řetězec](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Připojovací řetězec použijete později v tomto rychlém startu.

## <a name="send-events"></a>Odesílání událostí
V této části vytvoříte skript Pythonu pro odesílání událostí do centra událostí, které jste vytvořili dříve.

1. Otevřete svůj oblíbený editor Pythonu, například [Visual Studio Code](https://code.visualstudio.com/).
2. Vytvořte skript s názvem *send.py*. Tento skript odešle dávku událostí do centra událostí, které jste vytvořili dříve.
3. Do *send.py*vložte následující kód :

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData

    async def run():
        # Create a producer client to send messages to the event hub.
        # Specify a connection string to your event hubs namespace and
            # the event hub name.
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # Create a batch.
            event_data_batch = await producer.create_batch()

            # Add events to the batch.
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))

            # Send the batch of events to the event hub.
            await producer.send_batch(event_data_batch)

    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())

    ```

    > [!NOTE]
    > Úplný zdrojový kód, včetně informačních komentářů, najdete na [stránce GitHub send_async.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).
    

## <a name="receive-events"></a>Příjem událostí
Tento rychlý start používá úložiště objektů blob Azure jako úložiště kontrolních bodů. Úložiště kontrolních bodů se používá k zachování kontrolních bodů (to znamená poslední pozice pro čtení).  

> [!NOTE]
> Pokud používáte azure zásobníku, tato platforma může podporovat jinou verzi sady Storage Blob SDK než ty, které jsou obvykle k dispozici v Azure. Například pokud používáte [ve verzi Azure Stack Hub 2002](https://docs.microsoft.com/azure-stack/user/event-hubs-overview), nejvyšší dostupná verze pro službu Storage je verze 2017-11-09. V takovém případě kromě následujících kroků v této části budete muset také přidat kód pro cílrozhraní API služby úložiště verze 2017-11-09. Příklad, jak cílit na konkrétní verzi rozhraní API úložiště, najdete [v tématu synchronní](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py) a [asynchronní ukázky](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py) na GitHubu. Další informace o verzích služeb Azure Storage, které jsou podporované v centru Azure Stack Hub, najdete v centru [Azure Stack Hub: Rozdíly a důležité informace](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences).


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Vytvoření účtu úložiště Azure a kontejneru objektů blob
Vytvořte účet úložiště Azure a kontejner objektů blob v něm pomocí následujících kroků:

1. [Vytvoření účtu Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Vytvoření kontejneru objektů blob](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Získání připojovacího řetězce k účtu úložiště](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Nezapomeňte zaznamenat připojovací řetězec a název kontejneru pro pozdější použití v přijímaném kódu.


### <a name="create-a-python-script-to-receive-events"></a>Vytvoření skriptu Pythonu pro příjem událostí

V této části vytvoříte skript Pythonu pro příjem událostí z centra událostí:

1. Otevřete svůj oblíbený editor Pythonu, například [Visual Studio Code](https://code.visualstudio.com/).
2. Vytvořte skript s názvem *recv.py*.
3. Do *recv.py*vložte následující kód :

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore


    async def on_event(partition_context, event):
        # Print the event data.
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))

        # Update the checkpoint so that the program doesn't read the events
        # that it has already read when you run it next time.
        await partition_context.update_checkpoint(event)

    async def main():
        # Create an Azure blob checkpoint store to store the checkpoints.
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")

        # Create a consumer client for the event hub.
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # Call the receive method.
            await client.receive(on_event=on_event)

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # Run the main method.
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > Úplný zdrojový kód, včetně dalších informačních komentářů, najdete na [stránce GitHub recv_with_checkpoint_store_async.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py).


### <a name="run-the-receiver-app"></a>Spuštění aplikace přijímače

Chcete-li skript spustit, otevřete příkazový řádek, který má v cestě Python, a spusťte tento příkaz:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>Spuštění aplikace odesílatele

Chcete-li skript spustit, otevřete příkazový řádek, který má v cestě Python, a spusťte tento příkaz:

```bash
python send.py
```

Okno příjemce by měl zobrazit zprávy, které byly odeslány do centra událostí.


## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste odeslali a přijali události asynchronně. Chcete-li se dozvědět, jak synchronizovaně odesílat a přijímat události, přejděte na [stránku githubu sync_samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

Pro všechny ukázky (synchronní i asynchronní) na GitHubu přejděte do [klientské knihovny Azure Event Hubs pro ukázky Pythonu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples).
