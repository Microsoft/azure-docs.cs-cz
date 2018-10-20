---
title: Odesílání událostí do služby Azure Event Hubs pomocí Pythonu | Dokumentace Microsoftu
description: Začínáme s odesíláním událostí do služby Event Hubs pomocí Pythonu
services: event-hubs
author: sethmanheim
manager: femila
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 10/16/2018
ms.author: sethm
ms.openlocfilehash: bb77ed69ae8f2229cbd62afa545cac9f048689e8
ms.sourcegitcommit: 668b486f3d07562b614de91451e50296be3c2e1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49457999"
---
# <a name="send-events-to-event-hubs-using-python"></a>Odesílání událostí do služby Event Hubs pomocí Pythonu

Azure Event Hubs je platforma pro streamování velkých objemů dat a služba pro ingestování událostí, která je schopná přijmout a zpracovat miliony událostí za sekundu. Služba Event Hubs dokáže zpracovávat a ukládat události, data nebo telemetrické údaje produkované distribuovaným softwarem a zařízeními. Data odeslaná do centra událostí je možné transformovat a uložit pomocí libovolného poskytovatele analýz v reálném čase nebo adaptérů pro dávkové zpracování a ukládání. Podrobnější přehled služby Event Hubs naleznete v tématu [Přehled služby Event Hubs](event-hubs-about.md) a [funkcích služby Event Hubs](event-hubs-features.md).

Tento kurz popisuje, jak odesílat události do centra událostí z aplikace napsané v Pythonu. 

> [!NOTE]
> V tomto rychlém startu si můžete stáhnout jako ukázku z [Githubu](https://github.com/Azure/azure-event-hubs-python/tree/master/examples), nahraďte `EventHubConnectionString` a `EventHubName` řetězce hodnotami vašeho event hub, a potom ho spusťte. Alternativně můžete provedením kroků v tomto kurzu vytvoříte vlastní.

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu musí být splněné následující požadavky:

- Python 3.4 nebo novější.


## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Vytvoření oboru názvů Event Hubs a centra událostí
Prvním krokem je použití webu [Azure Portal](https://portal.azure.com) k vytvoření oboru názvů typu Event Hubs a získání přihlašovacích údajů pro správu, které vaše aplikace potřebuje ke komunikaci s centrem událostí. Pokud chcete vytvořit obor názvů a centra událostí, postupujte podle pokynů v [v tomto článku](event-hubs-create.md), potom proveďte následující kroky v tomto kurzu.

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

## <a name="run-application-to-send-events"></a>Spuštění aplikace pro odesílání událostí

Pro spuštění skriptu, otevřete příkazový řádek, který má Python v cestě a pak spusťte tento příkaz:

```bash
start python send.py
```

Blahopřejeme! Nyní jste odeslali zprávy do centra událostí.
 
## <a name="next-steps"></a>Další postup
V tomto rychlém startu jste odeslali zprávy do centra událostí pomocí Pythonu. Další způsob příjmu událostí z centra událostí pomocí Pythonu najdete v tématu [přijímat události z centra událostí - Python](event-hubs-python-get-started-receive.md).

<!-- Links -->
[Event Hubs overview]: event-hubs-about.md
[Visual Studio Code]: https://code.visualstudio.com/
[free account]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
