---
title: Odesílání nebo přijímání událostí z Azure Event Hubs pomocí Pythonu (starého)
description: Tento návod ukazuje, jak vytvořit a spustit skripty Pythonu, které odesílají události do nebo přijímají události z Azure Event Hubs pomocí starého balíčku azure-eventhub verze 1.
services: event-hubs
author: spelluru
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: quickstart
ms.date: 01/15/2020
ms.author: spelluru
ms.openlocfilehash: 22f6b2aba36e560e9bd335baa92925fe9846c670
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77162595"
---
# <a name="quickstart-send-and-receive-events-with-event-hubs-using-python-azure-eventhub-version-1"></a>Úvodní příručka: Odesílání a přijímání událostí pomocí centra událostí pomocí Pythonu (azure-eventhub verze 1)
Tento rychlý start ukazuje, jak odesílat události do centra událostí a přijímat je pomocí balíčku Pythonu **azure-eventhub verze 1.** 

> [!WARNING]
> Tento rychlý start používá starý balíček azure-eventhub verze 1. Pro rychlý start, který používá nejnovější **verzi 5** balíčku, najdete v tématu odesílání a přijímání událostí [pomocí azure-eventhub verze 5](get-started-python-send-v2.md). Pokud chcete přesunout aplikaci z používání starého balíčku na nový, [přečtěte si příručku pro migraci z azure-eventhub verze 1 na verzi 5](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub/migration_guide.md).
 

## <a name="prerequisites"></a>Požadavky
Pokud s Azure Event Hubs tečujete, přečtěte si [téma Přehled centra událostí,](event-hubs-about.md) než začnete tento rychlý start. 

Chcete-li tento rychlý start dokončit, potřebujete následující požadavky:

- **Předplatné Microsoft Azure**. Pokud chcete používat služby Azure, včetně Azure Event Hubs, potřebujete předplatné.  Pokud nemáte existující účet Azure, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free/) nebo použít výhody předplatitele MSDN při vytváření [účtu](https://azure.microsoft.com).
- Python 3.4 nebo `pip` novější, s nainstalovaným a aktualizovaným.
- Balíček Pythonu pro centra událostí. Chcete-li balíček nainstalovat, spusťte tento příkaz v příkazovém řádku, který má v cestě Python: 
  
  ```cmd
  pip install azure-eventhub==1.3.*
  ```
- **Vytvořte obor názvů Event Hubs a centrum událostí**. Prvním krokem je použití [portálu Azure](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Chcete-li vytvořit obor názvů a centrum událostí, postupujte podle postupu v [tomto článku](event-hubs-create.md). Potom získat hodnotu přístupového klíče pro centrum událostí podle pokynů z článku: [Získat připojovací řetězec](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Přístupový klíč v kódu, který napíšete později v tomto rychlém startu. Výchozí název klíče je: **RootManageSharedAccessKey**. 


## <a name="send-events"></a>Odesílání událostí

Vytvoření aplikace Pythonu, která odesílá události do centra událostí:

> [!NOTE]
> Místo práce přes rychlý start, můžete stáhnout a spustit [ukázkové aplikace](https://github.com/Azure/azure-event-hubs-python/tree/master/examples) z GitHubu. Nahraďte řetězce `EventHubConnectionString` a `EventHubName` hodnotami centra událostí.

1. Otevření oblíbeného editoru Pythonu, například [Visual Studio Code](https://code.visualstudio.com/)
2. Vytvořte nový soubor s názvem *send.py*. Tento skript odešle 100 událostí do centra událostí.
3. Do *send.py*vložte následující kód , \<který nahradí \<> oboru \<názvů Event Hubs,> eventhubu,> AccessKeyName a \<hodnoty primárního klíče> hodnotami: 
   
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

Chcete-li spustit skript, spusťte z adresáře, do kterého jste *uložili send.py*, spusťte tento příkaz:

```cmd
start python send.py
```

Blahopřejeme! Nyní jste odeslali zprávy do centra událostí.

## <a name="receive-events"></a>Příjem událostí

Vytvoření aplikace Pythonu, která přijímá události z centra událostí:

1. V editoru Pythonu vytvořte soubor s názvem *recv.py*.
2. Do *recv.py*vložte následující kód , \<který nahradí \<> oboru \<názvů Event Hubs,> eventhubu,> AccessKeyName a \<> hodnoty primárního klíče s hodnotami: 
   
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

Chcete-li spustit skript, spusťte z adresáře, do kterého jste *uložili recv.py*, spusťte tento příkaz:

```cmd
start python recv.py
```

## <a name="next-steps"></a>Další kroky
Další informace o centru událostí najdete v následujících článcích:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funkce a terminologie ve službě Azure Event Hubs](event-hubs-features.md)
- [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

