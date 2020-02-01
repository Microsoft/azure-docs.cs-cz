---
title: Posílání a přijímání událostí z Azure Event Hubs pomocí Pythonu (staré)
description: V tomto návodu se dozvíte, jak vytvářet a spouštět skripty Pythonu, které odesílají události do služby Azure Event Hubs nebo z nich přijímat události pomocí starého balíčku Azure-eventhub verze 1.
services: event-hubs
author: spelluru
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 654ccd6352dc0b671cc3becdafd2f1e1102dd39e
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902934"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>Rychlý Start: odesílání a příjem událostí pomocí Event Hubs pomocí Pythonu (Azure – eventhub verze 1)

Azure Event Hubs je platforma pro zpracování velkých objemů dat a služba pro příjem událostí, která může přijímat a zpracovávat miliony událostí za sekundu. Event Hubs může zpracovávat a ukládat události, data nebo telemetrie z distribuovaného softwaru a zařízení. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Další informace o Event Hubs najdete v tématu [azure Event Hubs](event-hubs-about.md) a [funkce a terminologie v Azure Event Hubs](event-hubs-features.md).

V tomto rychlém startu se dozvíte, jak vytvářet aplikace v Pythonu, které odesílají události do a přijímat události z centra událostí. 

> [!WARNING]
> Tento rychlý Start je pro verzi 1 sady Azure Event Hubs Python SDK. Doporučujeme [migrovat](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md) kód na [verzi 5 sady Python SDK](get-started-python-send-v2.md).

 
 

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto rychlého startu potřebujete následující požadavky:

- Předplatné Azure. Pokud ho nemáte, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- Obor názvů Active Event Hubs a centrum událostí vytvořené podle pokynů v tématu [rychlý Start: vytvoření centra událostí pomocí Azure Portal](event-hubs-create.md). Poznamenejte si název oboru názvů a centra událostí, které použijete později v tomto návodu. 
- Hodnota názvu sdíleného přístupového klíče a primárního klíče pro obor názvů Event Hubs. Pomocí pokynů uvedených v části [získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)Získejte název a hodnotu přístupového klíče. Název výchozího přístupového klíče je **RootManageSharedAccessKey**. Zkopírujte název klíče a hodnotu primárního klíče pro pozdější použití v tomto návodu. 
- Python 3,4 nebo novější s `pip` nainstalované a aktualizované.
- Balíček Pythonu pro Event Hubs. Chcete-li nainstalovat balíček, spusťte tento příkaz na příkazovém řádku, který má Python v cestě: 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```


## <a name="send-events"></a>Odesílání událostí

Vytvoření aplikace v Pythonu, která odesílá události do centra událostí:

> [!NOTE]
> Místo práce v rychlém startu si můžete stáhnout a spustit [ukázkové aplikace](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) z GitHubu. Nahraďte `EventHubConnectionString` a `EventHubName` řetězce hodnotami centra událostí.

1. Otevřete oblíbený editor Pythonu, například [Visual Studio Code](https://code.visualstudio.com/)
2. Vytvořte nový soubor s názvem *Send.py*. Tento skript odesílá 100 události do vašeho centra událostí.
3. Do *Send.py*vložte následující kód a nahraďte Event Hubs obor názvů \<>, \<eventhub >, \<AccessKeyName > a \<hodnoty primárního klíče > s hodnotami: 
   
   ```python
   import sys
   import logging
   import datetime
   import time
   import os
   
   from azure.eventhub import EventHubClient, Sender, EventData
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<namespace>.servicebus.windows.net/eventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   try:
       if not ADDRESS:
           raise ValueError("No EventHubs URL supplied.")
   
       # Create Event Hubs client
       client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
       sender = client.add_sender(partition="0")
       client.run()
       try:
           start_time = time.time()
           for i in range(100):
               print("Sending message: {}".format(i))
               message = "Message {}".format(i)
               sender.send(EventData(message))
       except:
           raise
       finally:
           end_time = time.time()
           client.stop()
           run_time = end_time - start_time
           logger.info("Runtime: {} seconds".format(run_time))
   
   except KeyboardInterrupt:
       pass
   ```
   
4. Uložte soubor. 

Pokud chcete skript spustit, z adresáře, kam jste uložili *Send.py*, spusťte tento příkaz:

```cmd
start python send.py
```

Blahopřejeme! Nyní jste odeslali zprávy do centra událostí.

## <a name="receive-events"></a>Příjem událostí

Vytvoření aplikace v Pythonu, která přijímá události z centra událostí:

1. V editoru Pythonu vytvořte soubor s názvem *recv.py*.
2. Do *recv.py*vložte následující kód a nahraďte Event Hubs obor názvů \<>, \<eventhub >, \<AccessKeyName > a \<hodnoty primárního klíče > s hodnotami: 
   
   ```python
   import os
   import sys
   import logging
   import time
   from azure.eventhub import EventHubClient, Receiver, Offset
   
   logger = logging.getLogger("azure")
   
   # Address can be in either of these formats:
   # "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
   # "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   # SAS policy and key are not required if they are encoded in the URL
   
   ADDRESS = "amqps://<namespace>.servicebus.windows.net/<eventhub>"
   USER = "<AccessKeyName>"
   KEY = "<primary key value>"
   
   
   CONSUMER_GROUP = "$default"
   OFFSET = Offset("-1")
   PARTITION = "0"
   
   total = 0
   last_sn = -1
   last_offset = "-1"
   client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
   try:
       receiver = client.add_receiver(
           CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
       client.run()
       start_time = time.time()
       for event_data in receiver.receive(timeout=100):
           print("Received: {}".format(event_data.body_as_str(encoding='UTF-8')))
           total += 1
   
       end_time = time.time()
       client.stop()
       run_time = end_time - start_time
       print("Received {} messages in {} seconds".format(total, run_time))
   
   except KeyboardInterrupt:
       pass
   finally:
       client.stop()
   ```
   
4. Uložte soubor.

Pokud chcete skript spustit, z adresáře, kam jste uložili *recv.py*, spusťte tento příkaz:

```cmd
start python recv.py
```

## <a name="next-steps"></a>Další kroky
Další informace o Event Hubs najdete v následujících článcích:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkce a terminologie ve službě Azure Event Hubs](event-hubs-features.md)
- [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

