---
title: Posílání a přijímání událostí z Azure Event Hubs pomocí Pythonu (nejnovější)
description: Tento článek popisuje návod pro vytvoření aplikace v Pythonu, která odesílá a přijímá události z Azure Event Hubs pomocí nejnovějšího balíčku Azure-eventhub verze 5.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: d977ae9ea8b78664ac1d3a318f58553da696c089
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906355"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub-version-5"></a>Posílání událostí a přijímání událostí z Center událostí pomocí Pythonu (Azure – eventhub verze 5)

Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro příjem událostí, která může přijímat a zpracovávat miliony událostí za sekundu. Centra událostí můžou zpracovávat a ukládat události, data nebo telemetrie vytvářené distribuovaným softwarem a zařízeními. Data odesílaná do centra událostí je možné transformovat a ukládat pomocí libovolného zprostředkovatele analýz v reálném čase nebo adaptérů pro dávkování či ukládání. Další informace najdete v tématu [Event Hubs přehled](event-hubs-about.md) a [Event Hubs funkce](event-hubs-features.md).

V tomto rychlém startu se dozvíte, jak vytvářet aplikace v Pythonu, které mohou odesílat události do nebo přijímat události z centra událostí.

> [!IMPORTANT]
> V tomto rychlém startu se používá verze 5 sady Azure Event Hubs Python SDK. Rychlý Start, který používá verzi 1 sady Python SDK, najdete v [tomto článku](event-hubs-python-get-started-send.md). 

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu potřebujete následující požadavky:

- Předplatné Azure. Pokud ho nemáte, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- Aktivní Event Hubs obor názvů a centrum událostí. Pokud je chcete vytvořit, postupujte podle pokynů v tématu [rychlý Start: vytvoření centra událostí pomocí Azure Portal](event-hubs-create.md). Poznamenejte si název oboru názvů a centra událostí, které se použijí později v tomto rychlém startu.
- Hodnota názvu sdíleného přístupového klíče a primárního klíče pro obor názvů Event Hubs. Podle pokynů v části [získání připojovacího řetězce centra událostí](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)Získejte název a hodnotu přístupového klíče. Název výchozího přístupového klíče je *RootManageSharedAccessKey*. Poznamenejte si název klíče a hodnotu primárního klíče, které se použijí později v tomto rychlém startu.
- Python 2,7 nebo 3,5 nebo novější, s nainstalovaným a aktualizovaným PIP
- Balíček Pythonu pro Event Hubs. 

    Chcete-li nainstalovat balíček, spusťte tento příkaz na příkazovém řádku, který má Python v cestě:

    ```cmd
    pip install azure-eventhub
    ```

    Nainstalujte následující balíček pro příjem událostí pomocí úložiště objektů BLOB v Azure jako úložiště kontrolního bodu:

    ```cmd
    pip install azure-eventhub-checkpointstoreblob-aio
    ```

## <a name="send-events"></a>Odesílání událostí
V této části vytvoříte skript v jazyce Python, který bude odesílat události do centra událostí, které jste vytvořili dříve.

1. Otevřete oblíbený editor Pythonu, například [Visual Studio Code](https://code.visualstudio.com/).
2. Vytvořte skript volá *send.py*. Tento skript pošle dávku událostí do centra událostí, které jste vytvořili dříve.
3. Vložte následující kód do *Send.py*:

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
    > Úplný zdrojový kód, včetně informativních komentářů, najdete na [stránce GitHub send_async. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py).

## <a name="receive-events"></a>Příjem událostí
V tomto rychlém startu se jako úložiště kontrolního bodu používá úložiště objektů blob Azure. Úložiště kontrolního bodu se používá k trvalému kontrolnímu bodu (to znamená poslední pozice pro čtení).  

### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Vytvoření účtu služby Azure Storage a kontejneru objektů BLOB
Pomocí následujících kroků vytvořte účet úložiště Azure a kontejner objektů BLOB.

1. [Vytvoření účtu Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Vytvoření kontejneru objektů BLOB](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Získání připojovacího řetězce k účtu úložiště](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

Nezapomeňte si poznamenejte připojovací řetězec a název kontejneru pro pozdější použití v kódu pro příjem.


### <a name="create-a-python-script-to-receive-events"></a>Vytvořit skript Pythonu pro příjem událostí

V této části vytvoříte skript v jazyce Python pro příjem událostí z centra událostí:

1. Otevřete oblíbený editor Pythonu, například [Visual Studio Code](https://code.visualstudio.com/).
2. Vytvořte skript volá *recv.py*.
3. Vložte následující kód do *recv.py*:

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
    > Úplný zdrojový kód včetně dalších informativních komentářů najdete na [stránce GitHub recv_with_checkpoint_store_async. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py).


### <a name="run-the-receiver-app"></a>Spuštění aplikace příjemce

Pro spuštění skriptu, otevřete příkazový řádek, který má Python v cestě a pak spusťte tento příkaz:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>Spuštění aplikace odesílatele

Pro spuštění skriptu, otevřete příkazový řádek, který má Python v cestě a pak spusťte tento příkaz:

```bash
python send.py
```

V okně přijímače by se měly zobrazit zprávy, které byly odeslány do centra událostí.


## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste události odeslali a přijali asynchronně. Pokud se chcete dozvědět, jak odesílat a přijímat události synchronně, navštivte [stránku Sync_samples GitHubu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

Pro všechny ukázky (synchronní i asynchronní) na GitHubu najdete ukázky v části [Klientská knihovna pro Azure Event Hubs pro Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples).
