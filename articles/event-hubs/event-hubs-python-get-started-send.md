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
ms.openlocfilehash: 22f6b2aba36e560e9bd335baa92925fe9846c670
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162595"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>Rychlý Start: odesílání a příjem událostí pomocí Event Hubs pomocí Pythonu (Azure – eventhub verze 1)
V tomto rychlém startu se dozvíte, jak odesílat události do centra událostí a přijímat z něj události pomocí balíčku **Azure-eventhub verze 1** Python. 

> [!WARNING]
> V tomto rychlém startu se používá starý balíček Azure-eventhub verze 1. Rychlý Start, který používá nejnovější **verzi 5** balíčku, najdete v tématu [posílání a přijímání událostí pomocí Azure-eventhub verze 5](get-started-python-send-v2.md). Postup přesunutí aplikace z použití starého balíčku na nový najdete v [Průvodci migrací z Azure – eventhub verze 1 na verzi 5](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).
 

## <a name="prerequisites"></a>Předpoklady
Pokud s Azure Event Hubs teprve začínáte, přečtěte si téma [přehled Event Hubs](event-hubs-about.md) před provedením tohoto rychlého startu. 

K dokončení tohoto rychlého startu potřebujete následující požadavky:

- **Microsoft Azure předplatné**. Pokud chcete používat služby Azure, včetně Azure Event Hubs, potřebujete předplatné.  Pokud nemáte existující účet Azure, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/) nebo využít výhody pro předplatitele MSDN při [vytváření účtu](https://azure.microsoft.com).
- Python 3,4 nebo novější s `pip` nainstalované a aktualizované.
- Balíček Pythonu pro Event Hubs. Chcete-li nainstalovat balíček, spusťte tento příkaz na příkazovém řádku, který má Python v cestě: 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```
- **Vytvoří obor názvů Event Hubs a centrum událostí**. Prvním krokem je použití webu [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centrum událostí, postupujte podle pokynů v [tomto článku](event-hubs-create.md). Pak Získejte hodnotu přístupového klíče pro centrum událostí podle pokynů uvedených v článku [získání připojovacího řetězce](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Přístupovou klávesu použijete v kódu, který napíšete později v tomto rychlém startu. Výchozí název klíče je: **RootManageSharedAccessKey**. 


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

