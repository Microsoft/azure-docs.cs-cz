---
title: Azure Event Hubs Capture návod | Dokumentace Microsoftu
description: Ukázka demonstruje použití funkce Event Hubs Capture využívá sady Azure Python SDK.
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
manager: timlt
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2017
ms.author: shvija
ms.openlocfilehash: b38e64891ce1065290d46f0fae2d22e151e03e4d
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005610"
---
# <a name="event-hubs-capture-walkthrough-python"></a>Event Hubs Capture návod: Python

Sběr dat je funkce služby Azure Event Hubs. Můžete ho automaticky doručovat streamovaná data ve službě event hub k účtu úložiště objektů Blob v Azure podle vašeho výběru. Tato funkce usnadňuje provádění dávkovému zpracování na streamovaných datech v reálném čase. Tento článek popisuje, jak používat Event Hubs Capture pomocí Pythonu. Další informace o Event Hubs Capture najdete v tématu [přehledovém článku](event-hubs-capture-overview.md).

Tento příklad používá [sady Azure Python SDK](https://azure.microsoft.com/develop/python/) k předvedení funkci Capture. Sender.py program odesílá Simulovaná telemetrická dat prostředí do služby Event Hubs ve formátu JSON. Centrum událostí je nakonfigurované pro funkci Capture použít k zápisu těchto dat do úložiště objektů Blob v dávkách. Capturereader.py aplikace čte tyto objekty BLOB a vytváří soubor připojení na zařízení. Aplikace pak zapíše data do souborů CSV.

## <a name="what-youll-accomplish"></a>Co budete provádět

1. Vytvoření účtu služby Azure Blob storage a v něm, kontejner objektů blob pomocí webu Azure portal.
2. Vytvořte obor názvů služby Event Hubs pomocí webu Azure portal.
3. Vytvoření centra událostí pomocí funkci Capture povolit pomocí webu Azure portal.
4. Odesílat data do centra událostí pomocí skriptu v jazyce Python.
5. Číst soubory ze sběru a jejich zpracování pomocí jiného skriptu Pythonu.

## <a name="prerequisites"></a>Požadavky

- Python 2.7.x
- Předplatné Azure
- Aktivní [Event Hubs oboru názvů a Centrum událostí](event-hubs-create.md)

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-blob-storage-account"></a>Vytvoření účtu úložiště objektů Blob v Azure
1. Přihlaste se na web [Azure Portal][Azure portal].
2. V levém podokně portálu vyberte **nový** > **úložiště** > **účtu úložiště**.
3. Dokončete výběr v **vytvořit účet úložiště** podokně a pak vyberte **vytvořit**.
   
   ![Podokno "Vytvoření účtu úložiště."][1]
4. Po zobrazení **nasazení proběhla úspěšně** zprávy, vyberte název nového účtu úložiště a v **Essentials** podokně a pak vyberte **objekty BLOB**. Když **službu Blob service** se otevře podokno, vyberte **+ kontejner** v horní části. Název kontejneru **zachycení**a pak zavřete **službu Blob service** podokně.
5. Vyberte **přístupové klíče** v levém podokně a zkopírujte název účtu úložiště a hodnotu **key1**. Uložte tyto hodnoty do Poznámkového bloku nebo jiného dočasného umístění.

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Vytvořit skript v jazyce Python pro odesílání událostí do vašeho centra událostí
1. Otevřete váš oblíbený editor Pythonu, například [Visual Studio Code][Visual Studio Code].
2. Vytvořte skript volá **sender.py**. Tento skript odesílá 200 události do vašeho centra událostí. Jsou jednoduché prostředí odečty odeslán ve formátu JSON.
3. Vložte následující kód do sender.py:
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('INSERT YOUR EVENT HUB NAME', s)
       print y
   ```
4. Aktualizace předchozí kód, který použije název oboru názvů, klíč-hodnota a název centra událostí, který jste získali při vytváření oboru názvů Event Hubs.

## <a name="create-a-python-script-to-read-your-capture-files"></a>Vytvoříte skript Pythonu ke čtení souborů zachytávání

1. Vyplňte podokno a vyberte **vytvořit**.
2. Vytvořte skript volá **capturereader.py**. Tento skript načte zaznamenané soubory a vytvoří soubor na zařízení k zápisu dat pouze pro toto zařízení.
3. Vložte následující kód do capturereader.py:
   
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
4. Vložte odpovídající hodnoty pro název účtu úložiště a klíč ve volání `startProcessing`.

## <a name="run-the-scripts"></a>Spouštění skriptů
1. Otevřete příkazový řádek, který má Python v cestě a potom spuštěním těchto příkazů nainstalujte požadované balíčky Pythonu:
   
   ```
   pip install azure-storage
   pip install azure-servicebus
   pip install avro
   ```
   
   Pokud máte starší verzi buď **služby azure storage** nebo **azure**, možná budete muset použít **--upgrade** možnost.
   
   Může být také potřeba spuštěním následujícího příkazu (není potřeba ve většině systémů):
   
   ```
   pip install cryptography
   ```
2. Změňte adresář na všude, kde jste uložili sender.py a capturereader.py a spusťte tento příkaz:
   
   ```
   start python sender.py
   ```
   
   Tento příkaz spustí nový proces Pythonu ke spuštění odesílatele.
3. Počkejte několik minut, než zachytávání ke spuštění. Do původní příkazovém okně zadejte následující příkaz:
   
   ```
   python capturereader.py
   ```

   Tento snímek procesor používá místní adresář můžete stáhnout všechny objekty BLOB z kontejneru nebo účtu úložiště. Zpracuje všechny, které nejsou prázdné a zapisuje výsledky jako soubory .csv do místního adresáře.

## <a name="next-steps"></a>Další postup

Další informace o službě Event Hubs můžete pomocí následujících odkazů:

* [Přehled služby Event Hubs Capture][Overview of Event Hubs Capture]
* [Ukázkové aplikace, které používají službu Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Přehled služby Event Hubs][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
