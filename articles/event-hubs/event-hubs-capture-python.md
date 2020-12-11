---
title: Číst data z Azure Event Hubs zachycená z aplikace v Pythonu (nejnovější)
description: V tomto článku se dozvíte, jak napsat kód v Pythonu pro zachycení dat, která se odesílají do centra událostí, a přečtěte si data zachycených událostí z účtu služby Azure Storage.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: f513b35e300141f16ee4c4880bc54aaf37945d65
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97109908"
---
# <a name="capture-event-hubs-data-in-azure-storage-and-read-it-by-using-python-azure-eventhub"></a>Zaznamenání dat Event Hubs v Azure Storage a jejich čtení pomocí Pythonu (Azure-eventhub)

Můžete nakonfigurovat centrum událostí tak, aby data odesílaná do centra událostí byla zachycena v účtu služby Azure Storage nebo Azure Data Lake Storage Gen 1 nebo Gen 2. V tomto článku se dozvíte, jak psát kód v Pythonu pro odesílání událostí do centra událostí a čtení zachycených dat z **Azure Blob Storage**. Další informace o této funkci najdete v tématu [Přehled funkcí Event Hubs Capture](event-hubs-capture-overview.md).

V tomto rychlém startu se k předvedení funkce Capture používá [sada Azure Python SDK](https://azure.microsoft.com/develop/python/) . Aplikace *sender.py* odesílá simulovanou telemetrii v oblasti životního prostředí do Center událostí ve formátu JSON. Centrum událostí je nakonfigurované tak, aby pomocí funkce Capture napsalo tato data do služby Blob Storage v dávkách. Aplikace *capturereader.py* tyto objekty blob přečte a vytvoří pro každé zařízení soubor připojení. Aplikace pak data zapíše do souborů CSV.

V tomto rychlém startu: 

> [!div class="checklist"]
> * Vytvořte účet úložiště objektů BLOB v Azure a kontejner ve Azure Portal.
> * Vytvořte Event Hubs obor názvů pomocí Azure Portal.
> * Vytvořte centrum událostí s povolenou funkcí Capture a připojte ho k účtu úložiště.
> * Odešlete data do centra událostí pomocí skriptu Pythonu.
> * Čtení a zpracování souborů z Event Hubs zachytávání pomocí jiného skriptu Pythonu.

## <a name="prerequisites"></a>Předpoklady

- Python 2,7 a 3,5 nebo novější s nainstalovaným a aktualizovaným PIP.  
- Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.  
- Aktivní Event Hubs obor názvů a centrum událostí.
[Vytvořte obor názvů Event Hubs a centrum událostí v oboru názvů](event-hubs-create.md). Poznamenejte si název oboru názvů Event Hubs, název centra událostí a primární přístupový klíč pro obor názvů. Přístupovou klávesu získáte v tématu [získání připojovacího řetězce Event Hubs](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Výchozí název klíče je *RootManageSharedAccessKey*. V tomto rychlém startu potřebujete jenom primární klíč. Nepotřebujete připojovací řetězec.  
- Účet úložiště Azure, kontejner objektů BLOB v účtu úložiště a připojovací řetězec k účtu úložiště. Pokud tyto položky nemáte, udělejte toto:  
    1. [Vytvoření účtu úložiště Azure](../storage/common/storage-account-create.md?tabs=azure-portal)  
    1. [Vytvoření kontejneru objektů blob v účtu úložiště](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)  
    1. [Získání připojovacího řetězce k účtu úložiště](../storage/common/storage-configure-connection-string.md)

    Nezapomeňte si poznamenejte připojovací řetězec a název kontejneru pro pozdější použití v tomto rychlém startu.  
- Povolte funkci Capture pro centrum událostí. Pokud to chcete provést, postupujte podle pokynů v tématu [povolení Event Hubsho zachycení pomocí Azure Portal](event-hubs-capture-enable-through-portal.md). Vyberte účet úložiště a kontejner objektů blob, který jste vytvořili v předchozím kroku. Tuto funkci můžete povolit i při vytváření centra událostí.  

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Vytvoření skriptu v jazyce Python pro odesílání událostí do centra událostí
V této části vytvoříte skript v jazyce Python, který odesílá 200 událostí (10 zařízení × 20 událostí) do centra událostí. Tyto události představují ukázkový přečtení z prostředí, které je odesláno ve formátu JSON. 

1. Otevřete oblíbený editor Pythonu, například [Visual Studio Code][Visual Studio Code].
2. Vytvořte skript s názvem *sender.py*. 
3. Vložte následující kód do *sender.py*. 
   
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
    * Nahraďte `EVENT HUBS NAMESPACE CONNECTION STRING` připojovacím řetězcem pro váš Event Hubs obor názvů.  
    * Nahraďte `EVENT HUB NAME` názvem vašeho centra událostí.  
5. Spusťte skript pro odesílání událostí do centra událostí.  
6. V Azure Portal můžete ověřit, že centrum událostí přijalo zprávy. Přepněte do zobrazení **zprávy** v části **metriky** . Aktualizujte stránku, aby se graf aktualizoval. Stránka může trvat několik sekund, než se zobrazí zpráva, že byly přijaty zprávy. 

    [![Ověřte, že centrum událostí přijalo zprávy.](./media/get-started-capture-python-v2/messages-portal.png)](./media/get-started-capture-python-v2/messages-portal.png#lightbox)

## <a name="create-a-python-script-to-read-your-capture-files"></a>Vytvoření skriptu v jazyce Python pro čtení digitalizačních souborů
V tomto příkladu jsou zachycená data uložená v úložišti objektů BLOB v Azure. Skript v tomto oddílu přečte soubory zachycených dat z vašeho účtu úložiště Azure a generuje soubory CSV, abyste je mohli snadno otevřít a zobrazit. V aktuálním pracovním adresáři aplikace se zobrazí 10 souborů. Tyto soubory budou obsahovat čtení z hlediska prostředí pro 10 zařízení. 

1. V editoru Pythonu Vytvořte skript s názvem *capturereader.py*. Tento skript přečte zachycené soubory a vytvoří soubor pro každé zařízení, které zapisuje data pouze pro toto zařízení.
2. Vložte následující kód do *capturereader.py*. 
   
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
3. Nahraďte `AZURE STORAGE CONNECTION STRING` připojovacím řetězcem pro váš účet úložiště Azure. Název kontejneru, který jste vytvořili v rámci tohoto rychlého startu, je *Capture*. Pokud jste pro kontejner použili jiný název, nahraďte *Capture* názvem kontejneru v účtu úložiště. 

## <a name="run-the-scripts"></a>Spuštění skriptů
1. Otevřete příkazový řádek, který má v cestě Python, a potom spuštěním těchto příkazů nainstalujte balíčky požadovaných součástí Pythonu:
   
   ```
   pip install azure-storage-blob
   pip install azure-eventhub
   pip install avro-python3
   ```
2. Přejděte do adresáře, kam jste uložili *sender.py* a *capturereader.py*, a spusťte tento příkaz:
   
   ```
   python sender.py
   ```
   
   Tento příkaz spustí nový proces Pythonu, který spustí odesílatele.
3. Počkejte několik minut, než se záznam spustí, a potom v původním příkazovém okně zadejte následující příkaz:
   
   ```
   python capturereader.py
   ```

   Tento procesor zachycení používá místní adresář ke stažení všech objektů BLOB z účtu úložiště a kontejneru. Zpracovává všechny, které nejsou prázdné, a zapisuje výsledky jako soubory CSV do místního adresáře.

## <a name="next-steps"></a>Další kroky
Podívejte se [na ukázky v Pythonu na GitHubu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhub/samples). 


[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: ./event-hubs-about.md
