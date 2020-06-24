---
title: 'Rychlý Start: čtení zachycených dat z aplikace Python – Azure Event Hubs'
description: 'Rychlý Start: skripty, které používají sadu Azure Python SDK k předvedení funkce Event Hubs Capture.'
services: event-hubs
author: spelluru
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.topic: quickstart
ms.date: 06/23/2020
ms.author: spelluru
ms.reviewer: shvija
ms.openlocfilehash: 658bfc95e9d7ef2954d245ac9bd7eafb56ebaaa1
ms.sourcegitcommit: 4042aa8c67afd72823fc412f19c356f2ba0ab554
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/24/2020
ms.locfileid: "85296589"
---
# <a name="quickstart-event-hubs-capture-walkthrough-python-azure-eventhub-version-1"></a>Rychlý Start: návod Event Hubsho zachycení: Python (Azure-eventhub verze 1)

Capture je funkce služby Azure Event Hubs. Pomocí Capture můžete automaticky doručovat streamovaná data v centru událostí do účtu Azure Blob Storage podle vašeho výběru. Tato funkce usnadňuje dávkové zpracování dat streamování v reálném čase. Tento článek popisuje, jak používat Event Hubs zachytávání pomocí Pythonu. Další informace o Event Hubs Capture najdete v tématu [zachycení událostí prostřednictvím Azure Event Hubs][Overview of Event Hubs Capture].

Tento návod používá [sadu Azure Python SDK](https://azure.microsoft.com/develop/python/) k předvedení funkce Capture. Program *sender.py* odesílá simulovanou telemetrii o životním prostředí do Event Hubs ve formátu JSON. Centrum událostí používá funkci Capture k zápisu těchto dat do úložiště objektů BLOB v dávkách. Aplikace *capturereader.py* tyto objekty blob přečte, vytvoří pro každé zařízení soubor připojení a na každé zařízení zapíše data do souborů *. csv* .

> [!WARNING]
> Tento rychlý Start je pro verzi 1 sady Azure Event Hubs Python SDK. Doporučujeme [migrovat](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md) kód na [verzi 5 sady Python SDK](get-started-capture-python-v2.md).

V tomto návodu: 

> [!div class="checklist"]
> * Vytvořte účet úložiště objektů BLOB v Azure a kontejner ve Azure Portal.
> * Povolte Event Hubs zachytávání a nasměrujte ho do svého účtu úložiště.
> * Odešlete data do centra událostí pomocí skriptu Pythonu.
> * Čtení a zpracování souborů z Event Hubs zachytávání pomocí jiného skriptu Pythonu.

## <a name="prerequisites"></a>Požadavky

- Python 3,4 nebo novější s `pip` nainstalovaným a aktualizovaným.
  
- Předplatné Azure. Pokud ho ještě nemáte, [Vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
  
- Obor názvů Active Event Hubs a centrum událostí vytvořené podle pokynů v tématu [rychlý Start: vytvoření centra událostí pomocí Azure Portal](event-hubs-create.md). Poznamenejte si název oboru názvů a centra událostí, které použijete později v tomto návodu. 
  
  > [!NOTE]
  > Pokud už máte kontejner úložiště k použití, můžete povolit zachytávání a při vytváření centra událostí vybrat kontejner úložiště. 
  > 
  
- Název Event Hubs sdíleného přístupového klíče a hodnotu primárního klíče. Tyto hodnoty najdete nebo vytvořte v části **zásady sdíleného přístupu** na stránce Event Hubs. Název výchozího přístupového klíče je **RootManageSharedAccessKey**. Zkopírujte název přístupového klíče a hodnotu primárního klíče pro pozdější použití v tomto návodu. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Vytvoření účtu služby Azure Blob Storage a kontejneru

Vytvořte účet úložiště a kontejner, který chcete použít pro zachytávání. 

1. Přihlaste se k webu [Azure Portal][Azure portal].
2. V levém navigačním panelu vyberte **účty úložiště**a na obrazovce **účty úložiště** vyberte **Přidat**.
3. Na obrazovce vytvoření účtu úložiště vyberte předplatné a skupinu prostředků a zadejte název účtu úložiště. Ostatní výběry můžete ponechat ve výchozím nastavení. Vyberte **zkontrolovat + vytvořit**, zkontrolujte nastavení a pak vyberte **vytvořit**. 
   
   ![Vytvoření účtu úložiště][1]
   
4. Až se nasazení dokončí, vyberte **Přejít k prostředku**a na obrazovce **Přehled** účtu úložiště vyberte **kontejnery**.
5. Na obrazovce **kontejnery** vyberte **+ kontejner**. 
6. Na obrazovce **Nový kontejner** zadejte název kontejneru a pak vyberte **OK**. Poznamenejte si název kontejneru, který použijete později v tomto návodu. 
7. V levém navigačním panelu na obrazovce **kontejnery** vyberte **přístupové klíče**. Zkopírujte **název účtu úložiště**a hodnotu **klíče** pod **klíč1**, abyste je mohli použít později v tomto návodu.
 
## <a name="enable-event-hubs-capture"></a>Povolit Event Hubs Capture

1. V Azure Portal přejděte do centra událostí tak, že vyberete jeho obor názvů Event Hubs ze **všech prostředků**, vyberete **centra událostí** v levém navigačním panelu a pak vyberete centrum událostí. 
2. Na obrazovce **Přehled** centra událostí vyberte **zachytit události**.
3. Na obrazovce **zachycení** vyberte **zapnuto**. Pak v části **Azure Storage kontejner**vyberte **Vybrat kontejner**. 
4. Na obrazovce **kontejnery** vyberte kontejner úložiště, který chcete použít, a pak vyberte **Vybrat**. 
5. Na obrazovce **zachycení** vyberte **Uložit změny**. 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Vytvoření skriptu v jazyce Python pro odesílání událostí do centra událostí
Tento skript pošle do centra událostí 200 událostí. Události jsou jednoduché čtení z prostředí odesílané ve formátu JSON.

1. Otevřete oblíbený editor Pythonu, například [Visual Studio Code][Visual Studio Code].
2. Vytvořte nový soubor s názvem *sender.py*. 
3. Vložte následující kód do *sender.py*. Nahraďte vlastní hodnoty pro Event Hubs \<namespace> , \<AccessKeyName> , \<primary key value> a \<eventhub> .
   
   ```python
   import uuid
   import datetime
   import random
   import json
   from azure.servicebus.control_client import ServiceBusService
   
   sbs = ServiceBusService(service_namespace='<namespace>', shared_access_key_name='<AccessKeyName>', shared_access_key_value='<primary key value>')
   devices = []
   for x in range(0, 10):
       devices.append(str(uuid.uuid4()))
   
   for y in range(0,20):
       for dev in devices:
           reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
           s = json.dumps(reading)
           sbs.send_event('<eventhub>', s)
       print(y)
   ```
4. Uložte soubor.

## <a name="create-a-python-script-to-read-capture-files"></a>Vytvoření skriptu v jazyce Python pro čtení souborů zachycení

Tento skript přečte zachycené soubory a vytvoří pro každé ze svých zařízení soubor pro zápis dat pouze pro toto zařízení.

1. V editoru Pythonu vytvořte nový soubor s názvem *capturereader.py*. 
2. Vložte následující kód do *capturereader.py*. Své uložené hodnoty nahraďte \<storageaccount> , \<storage account access key> a \<storagecontainer> .
   
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
           if not parsed_json['id'] in dict:
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
       print('Processor started using path: ' + os.getcwd())
       block_blob_service = BlockBlobService(account_name=accountName, account_key=key)
       generator = block_blob_service.list_blobs(container)
       for blob in generator:
           #content_length == 508 is an empty file, so only process content_length > 508 (skip empty files)
           if blob.properties.content_length > 508:
               print('Downloaded a non empty blob: ' + blob.name)
               cleanName = str.replace(blob.name, '/', '_')
               block_blob_service.get_blob_to_path(container, blob.name, cleanName)
               processBlob(cleanName)
               os.remove(cleanName)
           block_blob_service.delete_blob(container, blob.name)
   startProcessing('<storageaccount>', '<storage account access key>', '<storagecontainer>')
   ```

## <a name="run-the-python-scripts"></a>Spouštění skriptů Pythonu

1. Otevřete příkazový řádek s Pythonem v cestě a spuštěním těchto příkazů nainstalujte balíčky požadovaných součástí Pythonu:
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   Pokud máte starší verzi `azure-storage` nebo `azure` , možná budete muset použít `--upgrade` možnost.
   
   Možná budete muset spustit i následující příkaz. Spuštění tohoto příkazu není pro většinu systémů nutné. 
   
   ```cmd
   pip install cryptography
   ```
   
2. Z adresáře, kam jste uložili *sender.py* a *capturereader.py*, spusťte tento příkaz:
   
   ```cmd
   start python sender.py
   ```
   
   Příkaz spustí nový proces Pythonu pro spuštění odesílatele.
   
3. Až se zachycení dokončí, spusťte tento příkaz:
   
   ```cmd
   python capturereader.py
   ```

   Procesor zachycení stáhne všechny neprázdné objekty BLOB z kontejneru účtu úložiště a zapíše výsledky do místního adresáře jako soubory *. csv* . 

## <a name="next-steps"></a>Další kroky

Další informace o Event Hubs najdete v těchto tématech: 

* [Přehled Event Hubsho zachycení][Overview of Event Hubs Capture]
* [Ukázkové aplikace, které používají službu Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Přehled Event Hubs][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
