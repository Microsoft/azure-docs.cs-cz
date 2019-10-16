---
title: Posílání a přijímání událostí pomocí Pythonu – Azure Event Hubs
description: Tento návod ukazuje, jak vytvořit a spustit skripty Pythonu, které odesílají události do nebo přijímají události z Azure Event Hubs.
services: event-hubs
author: ShubhaVijayasarathy
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 10/11/2019
ms.author: shvija
ms.openlocfilehash: 330a7f5dc325c707b5be7ce9f9b3242a1d4c9547
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72428899"
---
# <a name="send-and-receive-events-with-event-hubs-using-python"></a>Odesílání a příjem událostí pomocí Event Hubs pomocí Pythonu

Azure Event Hubs je platforma pro zpracování velkých objemů dat a služba pro příjem událostí, která může přijímat a zpracovávat miliony událostí za sekundu. Event Hubs může zpracovávat a ukládat události, data nebo telemetrie z distribuovaného softwaru a zařízení. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Další informace o Event Hubs najdete v tématu [azure Event Hubs](event-hubs-about.md) a [funkce a terminologie v Azure Event Hubs](event-hubs-features.md).

V tomto rychlém startu se dozvíte, jak vytvářet aplikace v Pythonu, které odesílají události do a přijímat události z centra událostí. 

> [!NOTE]
> Místo práce v rychlém startu si můžete stáhnout a spustit [ukázkové aplikace](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) z GitHubu. Nahraďte řetězce `EventHubConnectionString` a `EventHubName` hodnotami vašeho centra událostí. 

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto rychlého startu potřebujete následující požadavky:

- Předplatné Azure. Pokud ho nemáte, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
- Obor názvů Active Event Hubs a centrum událostí vytvořené podle pokynů v tématu [rychlý Start: vytvoření centra událostí pomocí Azure Portal](event-hubs-create.md). Poznamenejte si název oboru názvů a centra událostí, které použijete později v tomto návodu. 
- Hodnota názvu sdíleného přístupového klíče a primárního klíče pro obor názvů Event Hubs. Pomocí pokynů uvedených v části [získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal)Získejte název a hodnotu přístupového klíče. Název výchozího přístupového klíče je **RootManageSharedAccessKey**. Zkopírujte název klíče a hodnotu primárního klíče pro pozdější použití v tomto návodu. 
- Python 3,4 nebo novější s nainstalovanou a aktualizovanou `pip`.
- Balíček Pythonu pro Event Hubs. Chcete-li nainstalovat balíček, spusťte tento příkaz na příkazovém řádku, který má Python v cestě: 
  
  ```cmd
  pip install azure-eventhub
  ```
  
  > [!NOTE]
  > Kód v tomto rychlém startu používá aktuální stabilní verzi 1.3.1 sady Event Hubs SDK. Vzorový kód, který používá verzi Preview sady SDK, najdete v části [https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/eventhub/azure-eventhubs/examples).

## <a name="send-events"></a>Odesílání událostí

Vytvoření aplikace v Pythonu, která odesílá události do centra událostí:

1. Otevřete oblíbený editor Pythonu, například [Visual Studio Code](https://code.visualstudio.com/)
2. Vytvořte nový soubor s názvem *Send.py*. Tento skript pošle do centra událostí 100 událostí.
3. Do *Send.py*vložte následující kód a nahraďte Event Hubs \<namespace >, \<eventhub >, \<AccessKeyName > a \<primary hodnoty klíče > s hodnotami: 
   
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
2. Do *recv.py*vložte následující kód a nahraďte Event Hubs \<namespace >, \<eventhub >, \<AccessKeyName > a \<primary hodnoty klíče > s hodnotami: 
   
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

