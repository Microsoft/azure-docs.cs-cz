---
title: Posílání a přijímání událostí pomocí Pythonu – Azure Event Hubs | Microsoft Docs
description: Tento článek popisuje návod pro vytvoření aplikace v Pythonu, která odesílá události do Azure Event Hubs.
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 01/08/2020
ms.author: spelluru
ms.openlocfilehash: a137f274744a6acec22d036f9f4c5c4a5174cc14
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2020
ms.locfileid: "75942591"
---
# <a name="send-events-to-or-receive-events-from-event-hubs-using-python"></a>Odesílání událostí do nebo příjem událostí z Event Hubs pomocí Pythonu

Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs najdete v tématech [Přehled služby Event Hubs](event-hubs-about.md) a [Funkce služby Event Hubs](event-hubs-features.md).

V tomto kurzu se dozvíte, jak vytvářet aplikace v Pythonu pro odesílání událostí nebo přijímání událostí z centra událostí. 

> [!IMPORTANT]
> V tomto rychlém startu se používá verze 5 sady Azure Event Hubs Python SDK. Rychlý Start, který používá starou verzi 1 sady Python SDK, najdete v [tomto článku](event-hubs-python-get-started-send.md). Pokud používáte verzi 1 sady SDK, doporučujeme migrovat kód na nejnovější verzi. Podrobnosti najdete v [Průvodci migrací](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).


## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

- Předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- Obor názvů Active Event Hubs a centrum událostí vytvořené podle pokynů v tématu [rychlý Start: vytvoření centra událostí pomocí Azure Portal](event-hubs-create.md). Poznamenejte si název oboru názvů a centra událostí, které použijete později v tomto návodu. 
- Hodnota názvu sdíleného přístupového klíče a primárního klíče pro obor názvů Event Hubs. Pomocí pokynů uvedených v části [získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)Získejte název a hodnotu přístupového klíče. Název výchozího přístupového klíče je **RootManageSharedAccessKey**. Zkopírujte název klíče a hodnotu primárního klíče pro pozdější použití v tomto návodu. 
- Python 2,7 a 3,5 nebo novější, s `pip` nainstalované a aktualizované.
- Balíček Pythonu pro Event Hubs. Chcete-li nainstalovat balíček, spusťte tento příkaz na příkazovém řádku, který má Python v cestě: 
    
    ```cmd
    pip install azure-eventhub
    ```

    Nainstalujte tento balíček pro příjem událostí pomocí úložiště objektů BLOB v Azure jako úložiště kontrolního bodu. 

    ```cmd
    pip install azure-eventhub-checkpointstoreblobaio
    ```

## <a name="send-events"></a>Odesílání událostí
V této části vytvoříte skript v jazyce Python, který bude odesílat události do centra událostí, které jste vytvořili dříve. 

1. Otevřete oblíbený editor Pythonu, například [Visual Studio Code](https://code.visualstudio.com/)
2. Vytvořte skript volá **send.py**. Tento skript pošle dávku událostí do centra událostí, které jste vytvořili dříve. 
3. Vložte následující kód do send.py. Podrobnosti najdete v komentářích ke kódu. 

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubProducerClient
    from azure.eventhub import EventData
    
    async def run():
        # create a producer client to send messages to the event hub
        # specify connection string to your event hubs namespace and 
            # the event hub name
        producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESPACE - CONNECTION STRING", eventhub_name="EVENT HUB NAME")
        async with producer:
            # create a batch
            event_data_batch = await producer.create_batch()
    
            # add events to the batch
            event_data_batch.add(EventData('First event '))
            event_data_batch.add(EventData('Second event'))
            event_data_batch.add(EventData('Third event'))
    
            # send the batch of events to the event hub
            await producer.send_batch(event_data_batch)
    
    loop = asyncio.get_event_loop()
    loop.run_until_complete(run())
    
    ```

    > [!NOTE]
    > Úplný zdrojový kód s velmi užitečnými komentáři najdete v [tomto souboru na GitHubu](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/send_async.py) .

## <a name="receive-events"></a>Příjem událostí
V tomto rychlém startu se jako úložiště kontrolního bodu používá Blob Storage Azure. Úložiště kontrolního bodu se používá k trvalému kontrolnímu bodu (poslední umístění pro čtení).  

### <a name="create-an-azure-storage-and-a-blob-container"></a>Vytvoření Azure Storage a kontejneru objektů BLOB
Pomocí těchto kroků vytvořte účet Azure Storage v něm kontejner objektů BLOB. 

1. [Vytvoření účtu Azure Storage](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)
2. [Vytvoření kontejneru objektů BLOB](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Získání připojovacího řetězce k účtu úložiště](../storage/common/storage-configure-connection-string.md?#view-and-copy-a-connection-string)

    Poznamenejte si připojovací řetězec a název kontejneru. Budete je používat v kódu příjmu. 


### <a name="create-python-script-to-receive-events"></a>Vytvoření skriptu v jazyce Python pro příjem událostí

V této části vytvoříte skript v jazyce Python pro příjem událostí z centra událostí:

1. Otevřete oblíbený editor Pythonu, například [Visual Studio Code](https://code.visualstudio.com/)
2. Vytvořte skript volá **recv.py**.
3. Vložte následující kód do recv.py. Podrobnosti najdete v komentářích ke kódu. 

    ```python
    import asyncio
    from azure.eventhub.aio import EventHubConsumerClient
    from azure.eventhub.extensions.checkpointstoreblobaio import BlobCheckpointStore
    
    
    async def on_event(partition_context, event):
        # print the event data 
        print("Received the event: \"{}\" from the partition with ID: \"{}\"".format(event.body_as_str(encoding='UTF-8'), partition_context.partition_id))
    
        # update the checkpoint so that the program doesn't read the events 
        # that it has already read when you run it next time
        await partition_context.update_checkpoint(event)
    
    async def main():
        # create an Azure blob checkpoint store to store the checkpoints 
        checkpoint_store = BlobCheckpointStore.from_connection_string("AZURE STORAGE CONNECTION STRING", "BLOB CONTAINER NAME")
    
        # create a consumer client for the event hub
        client = EventHubConsumerClient.from_connection_string("EVENT HUBS NAMESPACE CONNECTION STRING", consumer_group="$Default", eventhub_name="EVENT HUB NAME", checkpoint_store=checkpoint_store)
        async with client:
            # call the receive method
            await client.receive(on_event=on_event)
    
    if __name__ == '__main__':
        loop = asyncio.get_event_loop()
        # run the main method
        loop.run_until_complete(main())    
    ```

    > [!NOTE]
    > Úplný zdrojový kód s velmi užitečnými komentáři najdete v [tomto souboru na GitHubu](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/samples/async_samples/recv_with_checkpoint_store_async.py) .


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
V tomto rychlém startu jste události odeslali a přijímali asynchronně. Informace o tom, jak odesílat a přijímat události synchronně, najdete v tématu ukázky v [tomto umístění](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples/sync_samples).

Všechny ukázky (synchronizace i asynchronní) najdete [tady](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples)na GitHubu. 
