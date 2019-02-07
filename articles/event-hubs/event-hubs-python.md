---
title: Použití Pythonu k odesílání a příjem zpráv – Azure Event Hubs | Dokumentace Microsoftu
description: Zjistěte, jak odesílat události pro příjem událostí z a zachycení událostí streamování pomocí Event Hubs pomocí Pythonu.
keywords: ''
documentationcenter: ''
services: event-hubs
author: ShubhaVijayasarathy
manager: ''
editor: ''
ms.assetid: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/26/2018
ms.author: shvija
ms.openlocfilehash: 88fdaec9e19c082a6fe981dc4d9a0e015335f1e2
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818614"
---
# <a name="how-to-use-azure-event-hubs-from-a-python-application"></a>Jak z aplikace v Pythonu pomocí služby Azure Event Hubs
Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Další informace najdete v tématu [Úvod do služby Event Hubs](event-hubs-what-is-event-hubs.md). 

Tento článek obsahuje odkazy na články, které ukazují, jak provádět následující úlohy z aplikace napsané v **Python**:

- [Odesílání událostí do centra událostí](#send-events-to-event-hubs)
- [Příjem událostí z centra událostí](#receive-events-from-event-hubs)
- Čtení zachycená data události ze služby Azure storage. 

## <a name="prerequisites"></a>Požadavky
- Vytvoření centra událostí pomocí jednoho z těchto rychlých startů: [Azure portal](event-hubs-create.md), [rozhraní příkazového řádku Azure](event-hubs-quickstart-cli.md), [prostředí Azure PowerShell](event-hubs-quickstart-powershell.md), [šablony Azure Resource Manageru](event-hubs-resource-manager-namespace-event-hub.md). 
- Python 3.4 nebo novější na svém počítači.

## <a name="install-python-package"></a>Instalovat balíček Pythonu

Chcete-li nainstalovat balíček Pythonu pro službu Event Hubs, otevřete příkazový řádek, který má Python v cestě a spusťte tento příkaz: 

```bash
pip install azure-eventhub
```

## <a name="send-events-to-event-hubs"></a>Odesílání událostí do služby Event Hubs
Následující kód ukazuje, jak odesílat události do centra událostí z aplikace v Pythonu: 

1. Vytvoření proměnné k uložení adresy URL z centra událostí, název klíče a hodnoty klíče. 

    ```python
    # Import classes from Event Hubs python package
    from azure.eventhub import EventHubClient, Receiver, Offset
    
    # Address can be in either of these formats:
    # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
    # "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
    # For example:
    ADDRESS = "amqps://<MyEventHubNamspaceName>.servicebus.windows.net/<MyEventHubName>"
    
    # SAS policy and key are not required if they are encoded in the URL
    USER = "<Name of the access key. Default name: RootManageSharedAccessKey>"
    KEY = "<The access key>"
    ```

2. Vytvoření klienta služby Event Hubs, přidání odesílatele, spustit klienta, odesílání událostí pomocí odesílatele a poté zastavte klient po dokončení. 

    ```python
    # Create an Event Hubs client
    client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
    
    # Add a sender to the client
    sender = client.add_sender(partition="0")
    
    # Run the Event Hub client
    client.run()
    
    # Send event to the event hub
    sender.send(EventData("<MyEventData>"))
    
    # Stop the Event Hubs client
    client.stop()
    
    ```

Úplný kurz o tom, jak odesílat události do centra událostí z aplikace napsané v Pythonu, najdete v tématu [v tomto článku](event-hubs-python-get-started-send.md).

## <a name="receive-events-from-event-hubs"></a>Příjem událostí ze služby Event Hubs
Následující kód ukazuje, jak přijímat události z centra událostí z aplikace v Pythonu: 

```python

# Create an Event Hubs client
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)

# Add a receiver to the client
receiver = client.add_receiver(CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)

# Run the Event Hubs client
client.run()

# Receive event data from the event hub
for event_data in receiver.receive(timeout=100):
    last_offset = event_data.offset
    last_sn = event_data.sequence_number
    print("Received: {}, {}".format(last_offset, last_sn))

# Stop the Event Hubs client
client.stop()
```

Úplný kurz o tom, jak přijímat události z centra událostí z aplikace napsané v Pythonu, najdete v tématu [v tomto článku](event-hubs-python-get-started-receive.md)

## <a name="read-capture-event-data-from-azure-storage"></a>Data události sběr dat pro čtení ze služby Azure Storage
Následující kód ukazuje, jak číst data zaznamenané události, která je uložena v **úložiště objektů blob v Azure** z aplikace v Pythonu: Povolit **zachycení** funkce pro Centrum událostí podle pokynů od: [Povolení funkce Event Hubs Capture pomocí webu Azure portal](event-hubs-capture-enable-through-portal.md). Před testováním kód pak odešlete některé události do centra událostí. 

```python
import os
import string
import json
import avro.schema
from avro.datafile import DataFileReader, DataFileWriter
from avro.io import DatumReader, DatumWriter
from azure.storage.blob import BlockBlobService

def processBlob(filename):
    reader = DataFileReader(open(filename, 'rb'), DatumReader())
    dict = {}
    for reading in reader:
        parsed_json = json.loads(reading["Body"])
        if not 'id' in parsed_json:
            return
        if not dict.has_key(parsed_json['id']):
            list = []
            dict[parsed_json['id']] = list
        else:
            list = dict[parsed_json['id']]
            list.append(parsed_json)
    reader.close()
    for device in dict.keys():
        deviceFile = open(device + '.csv', "a")
        for r in dict[device]:
            deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\n')

def startProcessing(accountName, key, container):
    print 'Processor started using path: ' + os.getcwd()
    block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
    generator = block_blob_service.list_blobs(container)
    for blob in generator:
        #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
        if blob.properties.content_length > 508:
            print('Downloaded a non empty blob: ' + blob.name)
            cleanName = string.replace(blob.name, '/', '_')
            block_blob_service.get_blob_to_path(container, blob.name, cleanName)
            processBlob(cleanName)
            os.remove(cleanName)
        block_blob_service.delete_blob(container, blob.name)
startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'capture')

```

Úplný kurz o tom, jak číst zachycená data Event Hubs ve službě Azure blob storage z aplikace napsané v Pythonu, najdete v tématu [v tomto článku](event-hubs-capture-python.md)

## <a name="github-samples"></a>Ukázky Githubu
Můžete najít další ukázky Pythonu v [úložiště Git azure event hubs python](https://github.com/Azure/azure-event-hubs-python/).

## <a name="next-steps"></a>Další postup
Přečtěte si články v části koncepty, počínaje [přehledu funkcí Event Hubs](event-hubs-features.md).
