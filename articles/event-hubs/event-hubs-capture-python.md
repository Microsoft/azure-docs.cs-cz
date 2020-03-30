---
title: 'Úvodní příručka: Čtení zachycených dat z aplikace Python – Azure Event Hubs'
description: 'Úvodní příručka: Skripty, které používají Azure Python SDK k předvedení funkce zachycení centra událostí.'
services: event-hubs
documentationcenter: ''
author: ShubhaVijayasarathy
editor: ''
ms.assetid: bdff820c-5b38-4054-a06a-d1de207f01f6
ms.service: event-hubs
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: seodec18
ms.date: 01/15/2020
ms.author: shvija
ms.openlocfilehash: 6c830cf871c2ae650bb61e8b3712a664e9e405d4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77187292"
---
# <a name="quickstart-event-hubs-capture-walkthrough-python-azure-eventhub-version-1"></a>Úvodní příručka: Průvodce zachycením centra událostí: Python (azure-eventhub verze 1)

Digitalizace je funkce Azure Event Hubs. Pomocí funkce Capture můžete automaticky doručovat streamovaná data ve vašem centru událostí do účtu úložiště objektů Blob Azure podle vašeho výběru. Tato funkce usnadňuje dávkové zpracování dat datových proudů v reálném čase. Tento článek popisuje, jak používat zachycení centra událostí v Pythonu. Další informace o zachycení centra událostí najdete v [tématu Zachycení událostí prostřednictvím centra událostí Azure][Overview of Event Hubs Capture].

Tento návod používá [Azure Python SDK](https://azure.microsoft.com/develop/python/) k předvedení funkce zachycení. Program *sender.py* odesílá simulovanou telemetrii prostředí do centra událostí ve formátu JSON. Centrum událostí používá funkci zachycení k zápisu těchto dat do úložiště objektů Blob v dávkách. Aplikace *capturereader.py* tyto objekty BLOB přečte, vytvoří soubor pro připojení pro každé zařízení a zapíše data do souborů *.csv* na každém zařízení.

> [!WARNING]
> Tento rychlý start je pro verzi 1 Azure Event Hubs Python SDK. Doporučujeme [migrovat](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md) kód do [verze 5 sady Python SDK](get-started-capture-python-v2.md).

V tomto návodu: 

> [!div class="checklist"]
> * Vytvořte účet úložiště objektů blob Azure a kontejner na webu Azure Portal.
> * Povolte digitalizaci centra událostí a přesměrujte ji na svůj účet úložiště.
> * Odesílejte data do centra událostí pomocí skriptu Pythonu.
> * Čtení a zpracování souborů ze služby Event Hubs Capture pomocí jiného skriptu Pythonu.

## <a name="prerequisites"></a>Požadavky

- Python 3.4 nebo `pip` novější, s nainstalovaným a aktualizovaným.
  
- Předplatné Azure. Pokud ho nemáte, [vytvořte si účet zdarma,](https://azure.microsoft.com/free/) než začnete.
  
- Aktivní obor názvů Event Hubs a centrum událostí vytvořené podle pokynů na [úvodním panelu: Vytvoření centra událostí pomocí portálu Azure Portal](event-hubs-create.md). Poznamenejte si názvy oborů názvů a centra událostí, které chcete použít později v tomto návodu. 
  
  > [!NOTE]
  > Pokud už máte kontejner úložiště k použití, můžete povolit capture a vybrat kontejner úložiště při vytváření centra událostí. 
  > 
  
- Název přístupového klíče a hodnota primárního klíče centra Event Hubs sdílely. Nastránce Centra událostí najděte nebo vytvořte tyto hodnoty v části **Zásady sdíleného přístupu.** Výchozí název přístupového klíče je **RootManageSharedAccessKey**. Zkopírujte název přístupového klíče a hodnotu primárního klíče, která se použije později v tomto návodu. 

## <a name="create-an-azure-blob-storage-account-and-container"></a>Vytvoření účtu a kontejneru úložiště azure blob

Vytvořte účet úložiště a kontejner pro sběr. 

1. Přihlaste se k [portálu Azure][Azure portal].
2. V levém navigačním panelu vyberte **Účty úložiště**a na obrazovce **Účty úložiště** vyberte **Přidat**.
3. Na obrazovce vytvoření účtu úložiště vyberte předplatné a skupinu prostředků a pojmenujte účet úložiště. Ostatní výběry můžete ponechat ve výchozím nastavení. Vyberte **Zkontrolovat + vytvořit**, zkontrolujte nastavení a pak vyberte **Vytvořit**. 
   
   ![Vytvoření účtu úložiště][1]
   
4. Po dokončení nasazení vyberte **Přejít na prostředek**a na obrazovce **Přehled** účtu úložiště vyberte **Kontejnery**.
5. Na obrazovce **Kontejnery** vyberte **+ Kontejner**. 
6. Na obrazovce **Nový kontejner** pojmenujte kontejner a pak vyberte **OK**. Poznamenejte si název kontejneru, který chcete použít později v návodu. 
7. V levém navigačním panelu na obrazovce **Kontejnery** vyberte **přístupové klávesy**. Zkopírujte **název účtu úložiště**a hodnotu **Klíč** pod **klávesou key1**, aby se použila později v návodu.
 
## <a name="enable-event-hubs-capture"></a>Povolení zachycení centra událostí

1. Na webu Azure Portal přejděte do centra událostí tak, že vyberete obor názvů Centra událostí ze **všech prostředků**, vyberete **centra událostí** v levé navigaci a pak vyberete centrum událostí. 
2. Na obrazovce **Přehled** centra událostí vyberte **Zachytit události**.
3. Na obrazovce **Capture** vyberte **Zapnuto**. Potom v části **Kontejner úložiště Azure**vyberte Vybrat **kontejner**. 
4. Na obrazovce **Kontejnery** vyberte kontejner úložiště, který chcete použít, a pak vyberte **Vybrat**. 
5. Na obrazovce **Capture** vyberte **Uložit změny**. 

## <a name="create-a-python-script-to-send-events-to-event-hub"></a>Vytvoření skriptu Pythonu pro odesílání událostí do centra událostí
Tento skript odešle do centra událostí 200 událostí. Události jsou jednoduché environmentální hodnoty zaslané v JSON.

1. Otevřete svůj oblíbený editor Pythonu, například [Visual Studio Code][Visual Studio Code].
2. Vytvořte nový soubor s názvem *sender.py*. 
3. Do *sender.py*vložte následující kód . Nahraďte vlastní hodnoty> \<oboru názvů \<Centra událostí, \<> AccessKeyName,> hodnoty primárního klíče a \<> eventhubu.
   
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

## <a name="create-a-python-script-to-read-capture-files"></a>Vytvoření skriptu Pythonu pro čtení sběrných souborů

Tento skript přečte zachycené soubory a vytvoří soubor pro každé z vašich zařízení pro zápis dat pouze pro toto zařízení.

1. V editoru Pythonu vytvořte nový soubor s názvem *capturereader.py*. 
2. Do *capturereader.py*vložte následující kód . Nahraďte uložené \<hodnoty> \<účtu úložiště,> \<klíče přístupu k účtu úložiště a> kontejneru úložiště.
   
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

## <a name="run-the-python-scripts"></a>Spuštění skriptů Pythonu

1. Otevřete příkazový řádek, který má v cestě Python, a spusťte tyto příkazy k instalaci balíčků požadavků Pythonu:
   
   ```cmd
   pip install azure-storage
   pip install azure-servicebus
   pip install avro-python3
   ```
   
   Pokud máte starší verzi `azure-storage` `azure`aplikace nebo , bude `--upgrade` pravděpodobně nutné tuto možnost použít.
   
   Může být také nutné spustit následující příkaz. Spuštění tohoto příkazu není nutné ve většině systémů. 
   
   ```cmd
   pip install cryptography
   ```
   
2. Z adresáře, do kterého jste *uložili sender.py* a *capturereader.py*, spusťte tento příkaz:
   
   ```cmd
   start python sender.py
   ```
   
   Příkaz spustí nový proces Pythonu pro spuštění odesílatele.
   
3. Po dokončení sběru spusťte tento příkaz:
   
   ```cmd
   python capturereader.py
   ```

   Procesor pro digitalizaci stáhne všechny neprázdné objekty BLOB z kontejneru účtu úložiště a zapíše výsledky jako soubory *CSV* do místního adresáře. 

## <a name="next-steps"></a>Další kroky

Další informace o centru událostí najdete v tématu: 

* [Přehled zachycení centra událostí][Overview of Event Hubs Capture]
* [Ukázkové aplikace, které používají službu Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)
* [Přehled centra událostí][Event Hubs overview]

[Azure portal]: https://portal.azure.com/
[Overview of Event Hubs Capture]: event-hubs-capture-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]:../storage/common/storage-create-storage-account.md
[Visual Studio Code]: https://code.visualstudio.com/
[Event Hubs overview]: event-hubs-what-is-event-hubs.md
