---
title: Odesílání událostí do služby Azure Event Hubs pomocí Pythonu | Dokumentace Microsoftu
description: Začínáme s odesíláním událostí do služby Event Hubs pomocí Pythonu
services: event-hubs
author: sethmanheim
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/26/2018
ms.author: sethm
ms.openlocfilehash: 762e21cfc7d16b614eb637c569f8bfc5b6115db1
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43703619"
---
# <a name="send-events-to-event-hubs-using-python"></a>Odesílání událostí do služby Event Hubs pomocí Pythonu

Azure Event Hubs je vysoce škálovatelné událostí systém správy, který dokáže zpracovat miliony událostí za sekundu, které umožňuje aplikacím zpracovávat a analyzovat masivní objemy dat vytvářených připojenými zařízeními a dalšími systémy. Jakmile jsou shromážděna do centra událostí, může přijímat a zpracovávat události pomocí obslužných rutin v procesu nebo předáváním k jiným systémům pro analýzu.

Další informace o službě Event Hubs, najdete v článku [Přehled služby Event Hubs][Event Hubs overview].

Tento kurz popisuje, jak odesílat události do centra událostí z aplikace napsané v Pythonu. Pokud chcete přijímat události, najdete v článku [odpovídající článku Receive](event-hubs-python-get-started-receive.md).

Kód v tomto kurzu je převzatý z [ukázek Githubu](https://github.com/Azure/azure-event-hubs-python/tree/master/examples), které můžete zkontrolovat zobrazíte kompletní funkční aplikaci, včetně příkazy pro import a deklarace proměnné. Další příklady jsou k dispozici ve stejné složce Githubu.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

- Python 3.4 nebo novější.
- Stávající služby Event Hubs oboru názvů a Centrum událostí. Tyto entity můžete vytvořit podle pokynů v [v tomto článku](event-hubs-create.md). 

[!INCLUDE [create-account-note](../../includes/create-account-note.md)]


## <a name="install-python-package"></a>Instalovat balíček Pythonu

Chcete-li nainstalovat balíček Pythonu pro službu Event Hubs, otevřete příkazový řádek, který má Python v cestě a spusťte tento příkaz: 

```bash
pip install azure-eventhub
```

## <a name="create-a-python-script-to-send-events"></a>Vytvořit skript v jazyce Python pro odesílání událostí

V dalším kroku vytvoření aplikace v Pythonu, která zasílá události do centra událostí:

1. Otevřete váš oblíbený editor Pythonu, například [Visual Studio Code][Visual Studio Code].
2. Vytvořte skript volá **send.py**. Tento skript odesílá 100 události do vašeho centra událostí.
3. Vložte následující kód do send.py, zadejte adresu, uživatele a klíč hodnoty hodnotami, které jste získali z portálu Azure portal v předchozí části: 

```python
import sys
import logging
import datetime
import time
import os

from azure.eventhub import EventHubClient, Sender, EventData

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"

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
            sender.send(EventData(str(i)))
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

## <a name="send-events"></a>Odesílání událostí

Pro spuštění skriptu, otevřete příkazový řádek, který má Python v cestě a pak spusťte tento příkaz:

```bash
start python send.py
```
 
## <a name="next-steps"></a>Další postup

Teď, když jste odeslali události do centra událostí pomocí Pythonu, přijímat události naleznete v tématu [odpovídající článku Receive](event-hubs-python-get-started-receive.md).

Získáte další informace o službě Event Hubs na následujících stránkách:

* [Přehled služby Event Hubs][Event Hubs overview]
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
