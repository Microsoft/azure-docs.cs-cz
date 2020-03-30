---
title: Čtení dat ze zachycených center Událostí Azure z aplikace Pythonu (nejnovější)
description: Tento článek ukazuje, jak napsat kód Pythonu pro zachycení dat, která se odešle do centra událostí a číst data zachycené události z účtu úložiště Azure.
services: event-hubs
documentationcenter: ''
author: spelluru
editor: ''
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 01/30/2020
ms.author: spelluru
ms.openlocfilehash: 34583ef49b2f919391af3fe5700a558b2dc40700
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77187241"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-by-using-python-azure-eventhub-version-5"></a>Zachyťte data event hubů v Azure Storage a přečtěte si je pomocí Pythonu (azure-eventhub verze 5)

Centrum událostí můžete nakonfigurovat tak, aby se data odeslaná do centra událostí zachytila v účtu úložiště Azure nebo v Azure Data Lake Storage Storage Gen 1 nebo Gen 2. Tento článek ukazuje, jak napsat kód Pythonu k odeslání událostí do centra událostí a číst zachycená data z **úložiště objektů Blob Azure**. Další informace o této funkci naleznete v [tématu Přehled funkcí zachycení centra událostí](event-hubs-capture-overview.md).

Tento rychlý start používá [Azure Python SDK](https://azure.microsoft.com/develop/python/) k předvedení funkce zachycení. Aplikace *sender.py* odesílá simulovanou telemetrii prostředí do centra událostí ve formátu JSON. Centrum událostí je nakonfigurované tak, aby používalo funkci Zachycení k zápisu těchto dat do úložiště objektů Blob v dávkách. Aplikace *capturereader.py* tyto objekty BLOB přečte a vytvoří soubor pro každý zařízení. Aplikace pak zapíše data do souborů CSV.

> [!IMPORTANT]
> Tento rychlý start používá verzi 5 Azure Event Hubs Python SDK. Rychlý start, který používá verzi 1 sady Python SDK, naleznete v [tomto článku](event-hubs-capture-python.md). 

V tomto rychlém startu: 

> [!div class="checklist"]
> * Vytvořte účet úložiště objektů blob Azure a kontejner na webu Azure Portal.
> * Vytvořte obor názvů Event Hubs pomocí portálu Azure.
> * Vytvořte centrum událostí s povolenou funkcí Zachycení a připojte ho k účtu úložiště.
> * Odesílejte data do centra událostí pomocí skriptu Pythonu.
> * Čtení a zpracování souborů ze služby Event Hubs Capture pomocí jiného skriptu Pythonu.

## <a name="prerequisites"></a>Požadavky

- Python 2.7 a 3.5 nebo novější s nainstalovaným a aktualizovaným PIPEm.  
- Předplatné Azure. Pokud ho nemáte, [vytvořte si účet zdarma,](https://azure.microsoft.com/free/) než začnete.  
- Aktivní obor názvů Event Hubs a centrum událostí.
[Vytvořte obor názvů Event Hubs a centrum událostí v oboru názvů](event-hubs-create.md). Zaznamenejte název oboru názvů Centra událostí, název centra událostí a primární přístupový klíč pro obor názvů. Pokud chcete získat přístupový klíč, [přečtěte si informace o získání připojovacího řetězce Centra událostí](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Výchozí název klíče je *RootManageSharedAccessKey*. Pro tento rychlý start potřebujete pouze primární klíč. Nepotřebujete připojovací řetězec.  
- Účet úložiště Azure, kontejner objektů blob v účtu úložiště a připojovací řetězec k účtu úložiště. Pokud tyto položky nemáte, postupujte takto:  
    1. [Vytvoření účtu úložiště Azure](../storage/common/storage-quickstart-create-account.md?tabs=azure-portal)  
    1. [Vytvoření kontejneru objektů blob v účtu úložiště](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
    1. [Získání připojovacího řetězce k účtu úložiště](../storage/common/storage-configure-connection-string.md#view-and-copy-a-connection-string)

    Nezapomeňte zaznamenat připojovací řetězec a název kontejneru pro pozdější použití v tomto rychlém startu.  
- Povolte funkci Zachycení pro centrum událostí. Chcete-li tak učinit, postupujte podle pokynů v [povolit zachycení centra událostí pomocí portálu Azure](event-hubs-capture-enable-through-portal.md). Vyberte účet úložiště a kontejner objektů blob, který jste vytvořili v předchozím kroku. Funkci můžete také povolit při vytváření centra událostí.  

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Vytvoření skriptu Pythonu pro odesílání událostí do centra událostí
V této části vytvoříte skript Pythonu, který odešle 200 událostí (10 zařízení * 20 událostí) do centra událostí. Tyto události jsou ukázkové environmentální čtení, které je odesláno ve formátu JSON. 

1. Otevřete svůj oblíbený editor Pythonu, například [Visual Studio Code][Visual Studio Code].
2. Vytvořte skript s názvem *sender.py*. 
3. Do *sender.py*vložte následující kód . 
   
    ```python
    import time
    import os
    import uuid
    import datetime
    import random
    import json
    
    from azure.eventhub import EventHubProducerClient, EventData
    
    # This script simulates the production of events for 10 devices.
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    # Create a producer client to produce and publish events to the event hub.
    producer = EventHubProducerClient.from_connection_string(conn_str="EVENT HUBS NAMESAPCE CONNECTION STRING", eventhub_name="EVENT HUB NAME")
    
    for y in range(0,20):    # For each device, produce 20 events. 
        event_data_batch = producer.create_batch() # Create a batch. You will add events to the batch later. 
        for dev in devices:
            # Create a dummy reading.
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading) # Convert the reading into a JSON string.
            event_data_batch.add(EventData(s)) # Add event data to the batch.
        producer.send_batch(event_data_batch) # Send the batch of events to the event hub.
    
    # Close the producer.    
    producer.close()
    ```
4. Ve skriptech nahraďte následující hodnoty:  
    * Nahraďte `EVENT HUBS NAMESPACE CONNECTION STRING` připojovacím řetězcem pro obor názvů Event Hubs.  
    * Nahraďte `EVENT HUB NAME` název centra událostí.  
5. Spusťte skript a odešlete události do centra událostí.  
6. Na webu Azure Portal můžete ověřit, že centrum událostí přijalo zprávy. Přepněte do zobrazení **Zprávy** v části **Metriky.** Aktualizujte stránku a aktualizujte graf. Může trvat několik sekund, než se stránka zobrazí, než byly přijaty zprávy. 

    [![Ověřte, zda centrum událostí přijalo zprávy](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Vytvoření skriptu Pythonu pro čtení souborů pro digitalizaci
V tomto příkladu se zachycená data ukládají v úložišti objektů blob Azure. Skript v této části přečte zachycené datové soubory z vašeho účtu úložiště Azure a generuje soubory CSV, které můžete snadno otevřít a zobrazit. Zobrazí se 10 souborů v aktuálním pracovním adresáři aplikace. Tyto soubory budou obsahovat environmentální údaje pro 10 zařízení. 

1. V editoru Pythonu vytvořte skript s názvem *capturereader.py*. Tento skript přečte zachycené soubory a vytvoří soubor pro každé zařízení pro zápis dat pouze pro toto zařízení.
2. Do *capturereader.py*vložte následující kód . 
   
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
        # Create a blob container client.
        container = ContainerClient.from_connection_string("AZURE STORAGE CONNECTION STRING", container_name="BLOB CONTAINER NAME")
        blob_list = container.list_blobs() # List all the blobs in the container.
        for blob in blob_list:
            # Content_length == 508 is an empty file, so process only content_length > 508 (skip empty files).        
            if blob.size > 508:
                print('Downloaded a non empty blob: ' + blob.name)
                # Create a blob client for the blob.
                blob_client = ContainerClient.get_blob_client(container, blob=blob.name)
                # Construct a file name based on the blob name.
                cleanName = str.replace(blob.name, '/', '_')
                cleanName = os.getcwd() + '\\' + cleanName 
                with open(cleanName, "wb+") as my_file: # Open the file to write. Create it if it doesn't exist. 
                    my_file.write(blob_client.download_blob().readall()) # Write blob contents into the file.
                processBlob2(cleanName) # Convert the file into a CSV file.
                os.remove(cleanName) # Remove the original downloaded file.
                # Delete the blob from the container after it's read.
                container.delete_blob(blob.name)
    
    startProcessing()    
    ```
3. Nahraďte `AZURE STORAGE CONNECTION STRING` připojovacím řetězcem pro váš účet úložiště Azure. Název kontejneru, který jste vytvořili v tomto rychlém startu, je *zachycení*. Pokud jste pro kontejner použili jiný název, *nahraďte sběr* názvů za název kontejneru v účtu úložiště. 

## <a name="run-the-scripts"></a>Spuštění skriptů
1. Otevřete příkazový řádek, který má v cestě Python, a pak spusťte tyto příkazy k instalaci balíčků požadavků Pythonu:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. Změňte adresář na adresář, do kterého jste uložili *sender.py* a *capturereader.py*, a spusťte tento příkaz:
   
   ```
   python sender.py
   ```
   
   Tento příkaz spustí nový proces Pythonu pro spuštění odesílatele.
3. Počkejte několik minut, než se sběr spustí, a pak v původním příkazovém okně zadejte následující příkaz:
   
   ```
   python capturereader.py
   ```

   Tento procesor pro sběr dat používá místní adresář ke stažení všech objektů BLOB z účtu úložiště a kontejneru. Zpracovává všechny, které nejsou prázdné, a zapíše výsledky jako soubory CSV do místního adresáře.

## <a name="next-steps"></a>Další kroky
Podívejte se [na ukázky Pythonu na GitHubu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples). 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
