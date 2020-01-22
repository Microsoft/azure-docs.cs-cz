---
title: Číst data z Azure Event Hubs zachycená z aplikace Python | Microsoft Docs
description: V tomto článku se dozvíte, jak napsat kód v Pythonu pro zachycení dat, která se odesílají do centra událostí, a přečtěte si data zachycených událostí z Azure Storage.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2019
ms.author: spelluru
ms.openlocfilehash: 43223f7cb9ed254340c99d235d494d1e93583c7f
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2020
ms.locfileid: "76293534"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-using-python"></a>Zaznamenání dat Event Hubs v Azure Storage a jejich čtení pomocí Pythonu 
Můžete použít konfiguraci centra událostí tak, aby data odesílaná do centra událostí byla zachycena v Azure Storage nebo Azure Data Lake Storage. V tomto článku se dozvíte, jak pomocí zápisu kódu Pythonu odesílat události do centra událostí a číst zachycená data z úložiště objektů BLOB v Azure. Další informace o této funkci najdete v tématu [Přehled funkcí Event Hubs Capture](event-hubs-capture-overview.md).

Tento příklad používá [sady Azure Python SDK](https://azure.microsoft.com/develop/python/) k předvedení funkci Capture. Sender.py program odesílá Simulovaná telemetrická dat prostředí do služby Event Hubs ve formátu JSON. Centrum událostí je nakonfigurované pro funkci Capture použít k zápisu těchto dat do úložiště objektů Blob v dávkách. Capturereader.py aplikace čte tyto objekty BLOB a vytváří soubor připojení na zařízení. Aplikace pak zapíše data do souborů CSV.

> [!IMPORTANT]
> V tomto rychlém startu se používá verze 5 sady Azure Event Hubs Python SDK. Rychlý Start, který používá starou verzi 1 sady Python SDK, najdete v [tomto článku](event-hubs-capture-python.md). Pokud používáte verzi 1 sady SDK, doporučujeme migrovat kód na nejnovější verzi. Podrobnosti najdete v [Průvodci migrací](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).

V tomto rychlém startu: 

> [!div class="checklist"]
> * Vytvořte účet úložiště objektů BLOB v Azure a kontejner ve Azure Portal.
> * Vytvořte Event Hubs obor názvů pomocí Azure Portal.
> * Vytvořte centrum událostí s povolenou funkcí Capture a připojte ho k účtu úložiště.
> * Odešlete data do centra událostí pomocí skriptu Pythonu.
> * Čtení a zpracování souborů z Event Hubs zachytávání pomocí jiného skriptu Pythonu.

## <a name="prerequisites"></a>Požadavky

- Python 2,7 a 3,5 nebo novější, s `pip` nainstalované a aktualizované.
- Předplatné Azure. Pokud ho nemáte, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- [Vytvořte obor názvů Event Hubs a centrum událostí v oboru názvů](event-hubs-create.md). Poznamenejte si název oboru názvů Event Hubs, název centra událostí a primární přístupový klíč pro obor názvů. Přístupovou klávesu získáte pomocí pokynů uvedených v článku [získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Je výchozí název klíče: **RootManageSharedAccessKey**. Pro tento kurz nepotřebujete připojovací řetězec. Potřebujete jenom primární klíč. 
- Pomocí těchto kroků vytvořte **účet Azure Storage** a **kontejner objektů BLOB**:
    1. [Vytvořte účet Azure Storage](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal).
    2. [Vytvořte v úložišti kontejner objektů BLOB](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container). 
    3. [Získejte připojovací řetězec k účtu úložiště](../storage/common/storage-configure-connection-string.md#view-and-copy-a-connection-string).

        Poznamenejte si **připojovací řetězec** a **název kontejneru**. Později je budete používat v kódu. 
- Funkci **Capture** pro centrum událostí povolte pomocí následujících pokynů: [Povolení Event Hubsho zachycení pomocí Azure Portal](event-hubs-capture-enable-through-portal.md). Vyberte účet úložiště a kontejner objektů blob, který jste vytvořili v předchozím kroku. Tuto funkci můžete také povolit při vytváření centra událostí. 

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Vytvořit skript v jazyce Python pro odesílání událostí do vašeho centra událostí
V této části vytvoříte skript v jazyce Python, který odesílá 200 událostí (10 zařízení × 20 událostí) do centra událostí. Tyto události jsou ukázkové čtení z prostředí odesílané ve formátu JSON. 

1. Otevřete oblíbený editor Pythonu, například [Visual Studio Code][Visual Studio Code].
2. Vytvořte skript volá **sender.py**. 
3. Vložte následující kód do sender.py. Podrobnosti najdete v komentářích ke kódu. 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # this scripts simulates production of events for 10 devices
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # create a producer client to produce/publish events to the event hub
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # for each device, produce 20 events 
        event_data_batch = producer.create_batch() # create a batch. you will add events to the batch later. 
        for dev in devices:
            # create a dummy reading
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # convert reading into a JSON string
            event_data_batch.add(EventData(s)) # add event data to the batch
        producer.send_batch(event_data_batch) # send the batch of events to the event hub
    
    # close the producer    
    producer.close()
    ```
4. Ve skriptech nahraďte následující hodnoty:
    1. Nahraďte `EVENT HUBS NAMESPACE CONNECTION STRING` připojovacím řetězcem pro váš obor názvů Event Hubs.
    2. Nahraďte `EVENT HUB NAME` názvem vašeho centra událostí. 
5. Spusťte skript pro odesílání událostí do centra událostí. 
6. V Azure Portal můžete ověřit, že centrum událostí přijalo zprávy. Přepněte do zobrazení **zprávy** v části **metriky** . Aktualizujte stránku, aby se graf aktualizoval. Může trvat několik sekund, než se zobrazí zpráva, že byly přijaty zprávy. 

    [![ověřte, že centrum událostí přijalo zprávy.](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Vytvoříte skript Pythonu ke čtení souborů zachytávání
V tomto příkladu jsou zachycená data uložená v Azure Blob Storage. Skript v tomto oddílu čte soubory zachycených dat z Azure Storage a generuje soubory CSV, abyste mohli snadno otevřít a zobrazit obsah. V aktuálním pracovním adresáři aplikace se zobrazí 10 souborů. Tyto soubory budou obsahovat čtení z hlediska prostředí pro 10 zařízení. 

1. V editoru Pythonu Vytvořte skript s názvem **capturereader.py**. Tento skript načte zaznamenané soubory a vytvoří soubor na zařízení k zápisu dat pouze pro toto zařízení.
2. Vložte následující kód do capturereader.py. Podrobnosti najdete v komentářích ke kódu. 
   
    ```python
    import os
    import string
    import json
    import uuid
    import avro.schema
    
    from azure.storage.blob import ContainerClient, BlobClient
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    
    
    def processBlob2(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed_json = json.loads(reading["Body"])
            if not 'id' in parsed_json:
                return
            if not parsed_json['id'] in dict:
                list = []
                dict[parsed_json['id']] = list
            else:
                list = dict[parsed_json['id']]
                list.append(parsed_json)
        reader.close()
        for device in dict.keys():
            filename = os.getcwd() + '\\' + str(device) + '.csv'
            deviceFile = open(filename, "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')
    
    def startProcessing():
        print('Processor started using path: ' + os.getcwd())
        # create a blob container client
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # list all the blobs in the container
        for blob in blob_list:
            #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # create a blob client for the blob
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # construct a file name based on the blob name
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # open the file to write. create if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # write blob contents into the file
                processBlob2(cleanName) # convert the file into a CSV file
                os.remove(cleanName) # remove the original downloaded file
                # delete the blob from the container after it's read
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
4. Nahraďte `<AZURE STORAGE CONNECTION STRING>` připojovacím řetězcem pro účet Azure Storage. Název kontejneru, který jste vytvořili v tomto kurzu, je: **Capture**. Pokud jste pro kontejner použili jiný název, nahraďte `capture` názvem kontejneru v účtu úložiště. 

## <a name="run-the-scripts"></a>Spouštění skriptů
1. Otevřete příkazový řádek, který má Python v cestě a potom spuštěním těchto příkazů nainstalujte požadované balíčky Pythonu:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. Změňte adresář na všude, kde jste uložili sender.py a capturereader.py a spusťte tento příkaz:
   
   ```
   python sender.py
   ```
   
   Tento příkaz spustí nový proces Pythonu ke spuštění odesílatele.
3. Počkejte několik minut, než zachytávání ke spuštění. Do původní příkazovém okně zadejte následující příkaz:
   
   ```
   python capturereader.py
   ```

   Tento snímek procesor používá místní adresář můžete stáhnout všechny objekty BLOB z kontejneru nebo účtu úložiště. Zpracuje všechny, které nejsou prázdné a zapisuje výsledky jako soubory .csv do místního adresáře.

## <a name="next-steps"></a>Další kroky
Podívejte se na ukázky v Pythonu [na GitHubu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples). 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
