---
title: Příjem událostí ze služby Azure Event Hubs pomocí Pythonu | Dokumentace Microsoftu
description: Začínáme s příjmem události ze služby Event Hubs pomocí Pythonu
services: event-hubs
author: sethmanheim
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 07/26/2018
ms.author: sethm
ms.openlocfilehash: f5388f2de599d94f68a1d24a7d701a2cb4795915
ms.sourcegitcommit: cb61439cf0ae2a3f4b07a98da4df258bfb479845
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43703600"
---
# <a name="receive-events-from-event-hubs-using-python"></a>Příjem událostí ze služby Event Hubs pomocí Pythonu

Azure Event Hubs je vysoce škálovatelné událostí systém správy, který dokáže zpracovat miliony událostí za sekundu, které umožňuje aplikacím zpracovávat a analyzovat masivní objemy dat vytvářených připojenými zařízeními a dalšími systémy. Jakmile jsou shromážděna do centra událostí, může přijímat a zpracovávat události pomocí obslužných rutin v procesu nebo předáváním k jiným systémům pro analýzu.

Další informace o službě Event Hubs, najdete v článku [Přehled služby Event Hubs][Event Hubs overview].

Tento kurz popisuje, jak chcete přijímat události z centra událostí z aplikace napsané v Pythonu. K odesílání událostí, naleznete v tématu [odpovídající článku odeslat](event-hubs-python-get-started-send.md).

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

## <a name="create-a-python-script-to-receive-events"></a>Vytvořit skript Pythonu pro příjem událostí

V dalším kroku vytvoření aplikace v Pythonu, která bude přijímat události z centra událostí:

1. Otevřete váš oblíbený editor Pythonu, například [Visual Studio Code][Visual Studio Code].
2. Vytvořte skript volá **recv.py**.
3. Vložte následující kód do recv.py, zadejte adresu, uživatele a klíč hodnoty hodnotami, které jste získali z portálu Azure portal v předchozí části: 

```python
import os
import sys
import logging
import time
from azure.eventhub import EventHubClient, Receiver, Offset

logger = logging.getLogger("azure")

# Address can be in either of these formats:
# "amqps://<URL-encoded-SAS-policy>:<URL-encoded-SAS-key>@<mynamespace>.servicebus.windows.net/myeventhub"
# "amqps://<mynamespace>.servicebus.windows.net/myeventhub"
# For example:
ADDRESS = "amqps://mynamespace.servicebus.windows.net/myeventhub"

# SAS policy and key are not required if they are encoded in the URL
USER = "RootManageSharedAccessKey"
KEY = "namespaceSASKey"
CONSUMER_GROUP = "$default"
OFFSET = Offset("-1")
PARTITION = "0"

total = 0
last_sn = -1
last_offset = "-1"
client = EventHubClient(ADDRESS, debug=False, username=USER, password=KEY)
try:
    receiver = client.add_receiver(CONSUMER_GROUP, PARTITION, prefetch=5000, offset=OFFSET)
    client.run()
    start_time = time.time()
    for event_data in receiver.receive(timeout=100):
        last_offset = event_data.offset
        last_sn = event_data.sequence_number
        print("Received: {}, {}".format(last_offset, last_sn))
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

## <a name="receive-events"></a>Příjem událostí

Pro spuštění skriptu, otevřete příkazový řádek, který má Python v cestě a pak spusťte tento příkaz:

```bash
start python recv.py
```
 
## <a name="next-steps"></a>Další postup

K odesílání událostí, naleznete v tématu [odpovídající článku odeslat](event-hubs-python-get-started-send.md).

Získáte další informace o službě Event Hubs na následujících stránkách:

* [Přehled služby Event Hubs][Event Hubs overview]
* [Vytvoření centra událostí](event-hubs-create.md)
* [Nejčastější dotazy k Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
