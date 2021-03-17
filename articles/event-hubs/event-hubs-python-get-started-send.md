---
title: Posílání a přijímání událostí z Azure Event Hubs pomocí Pythonu (nejnovější)
description: Tento článek popisuje návod pro vytvoření aplikace v Pythonu, která odesílá a přijímá události z Azure Event Hubs pomocí nejnovějšího balíčku Azure-eventhub.
ms.topic: quickstart
ms.date: 02/11/2020
ms.openlocfilehash: f05f546f19a7944c049b97ba18065159db6fab67
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605560"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-by-using-python-azure-eventhub"></a>Posílání událostí a přijímání událostí z Center událostí pomocí Pythonu (Azure-eventhub)
V tomto rychlém startu se dozvíte, jak odesílat události do centra událostí a přijímat z něj události pomocí balíčku Pythonu **pro Azure-eventhub** .

## <a name="prerequisites"></a>Předpoklady
Pokud s Azure Event Hubs teprve začínáte, přečtěte si téma [přehled Event Hubs](event-hubs-about.md) před provedením tohoto rychlého startu. 

K dokončení tohoto rychlého startu potřebujete následující požadavky:

- **Microsoft Azure předplatné**. Pokud chcete používat služby Azure, včetně Azure Event Hubs, potřebujete předplatné.  Pokud nemáte existující účet Azure, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/) nebo využít výhody pro předplatitele MSDN při [vytváření účtu](https://azure.microsoft.com).
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
- **Vytvoří obor názvů Event Hubs a centrum událostí**. Prvním krokem je použití [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v [tomto článku](event-hubs-create.md). Pak Získejte **připojovací řetězec pro obor názvů Event Hubs** podle pokynů uvedených v článku [získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Připojovací řetězec použijete později v tomto rychlém startu.

## <a name="send-events"></a>Odesílání událostí
V této části vytvoříte skript v jazyce Python, který bude odesílat události do centra událostí, které jste vytvořili dříve.

1. Otevřete oblíbený editor Pythonu, například [Visual Studio Code](https://code.visualstudio.com/).
2. Vytvořte skript s názvem *Send.py*. Tento skript pošle dávku událostí do centra událostí, které jste vytvořili dříve.
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


> [!WARNING]
> Pokud spustíte tento kód v Azure Stackovém centru, dojde k chybám za běhu, pokud necílíte na konkrétní verzi rozhraní API úložiště. Důvodem je, že sada Event Hubs SDK používá nejnovější dostupné rozhraní API Azure Storage dostupné v Azure, které nemusí být k dispozici na vaší platformě služby Azure Stack hub. Centrum Azure Stack může podporovat jinou verzi sady SDK pro úložiště objektů blob, než jsou ta, která jsou běžně dostupná v Azure. Pokud jako úložiště kontrolního bodu používáte Azure blogu Storage, podívejte se na [podporovanou verzi rozhraní API Azure Storage pro sestavení centra Azure Stack](/azure-stack/user/azure-stack-acs-differences?#api-version) a cílení na verzi v kódu. 
>
> Pokud například používáte v Azure Stack centra verze 2005, nejvyšší dostupná verze služby úložiště je verze 2019-02-02. Ve výchozím nastavení používá Klientská knihovna Event Hubs SDK nejvyšší dostupnou verzi v Azure (2019-07-07 v době vydání sady SDK). V takovém případě, kromě kroků v této části, budete také muset přidat kód pro cílení na rozhraní API služby úložiště verze 2019-02-02. Příklad cílení na konkrétní verzi rozhraní API úložiště najdete v článku [synchronní](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py) a [asynchronní](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py) ukázky na GitHubu. 


### <a name="create-an-azure-storage-account-and-a-blob-container"></a>Vytvoření účtu služby Azure Storage a kontejneru objektů BLOB
Pomocí následujících kroků vytvořte účet úložiště Azure a kontejner objektů BLOB.

1. [Vytvoření účtu Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Vytvoření kontejneru objektů blob](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Získání připojovacího řetězce k účtu úložiště](../storage/common/storage-configure-connection-string.md)

Nezapomeňte si poznamenejte připojovací řetězec a název kontejneru pro pozdější použití v kódu pro příjem.


### <a name="create-a-python-script-to-receive-events"></a>Vytvoření skriptu v jazyce Python pro příjem událostí

V této části vytvoříte skript v jazyce Python pro příjem událostí z centra událostí:

1. Otevřete oblíbený editor Pythonu, například [Visual Studio Code](https://code.visualstudio.com/).
2. Vytvořte skript s názvem *recv.py*.
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
            # Call the receive method. Read from the beginning of the partition (starting_position: "-1")
            await client.receive(on_event=on_event,  starting_position="-1")

    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # Run the main method.
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > Úplný zdrojový kód včetně dalších informativních komentářů najdete na [stránce GitHub recv_with_checkpoint_store_async. py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py).


### <a name="run-the-receiver-app"></a>Spuštění aplikace příjemce

Chcete-li spustit skript, otevřete příkazový řádek, který má v cestě Python, a pak spusťte tento příkaz:

```bash
python recv.py
```

### <a name="run-the-sender-app"></a>Spuštění aplikace odesílatele

Chcete-li spustit skript, otevřete příkazový řádek, který má v cestě Python, a pak spusťte tento příkaz:

```bash
python send.py
```

V okně přijímače by se měly zobrazit zprávy, které byly odeslány do centra událostí.


## <a name="next-steps"></a>Další kroky
V tomto rychlém startu jste události odeslali a přijali asynchronně. Pokud se chcete dozvědět, jak odesílat a přijímat události synchronně, navštivte [stránku Sync_samples GitHubu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

Pro všechny ukázky (synchronní i asynchronní) na GitHubu najdete ukázky v části [Klientská knihovna pro Azure Event Hubs pro Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples).
