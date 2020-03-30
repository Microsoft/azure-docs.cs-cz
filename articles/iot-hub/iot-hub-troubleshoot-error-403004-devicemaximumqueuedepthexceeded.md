---
title: Poradce při potížích s chybou služby Azure IoT Hub 403004 DeviceMaximumQueueDepthExceeded
description: Pochopit, jak opravit chybu 403004 DeviceMaximumQueueDepthExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1edf723aa885ff18d2ce2dda4d71b67700a98a5b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497497"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

Tento článek popisuje příčiny a řešení pro **403004 DeviceMaximumQueueDepthExceeded** chyby.

## <a name="symptoms"></a>Příznaky

Při pokusu o odeslání zprávy cloud zařízení požadavek se nezdaří s chybou **403004** nebo **DeviceMaximumQueueDepthExceeded**.

## <a name="cause"></a>Příčina

Základní příčinou je, že počet zpráv zařazených do fronty pro zařízení překračuje [limit fronty (50)](./iot-hub-devguide-quotas-throttling.md#other-limits).

Nejpravděpodobnější důvod, proč běžíte do tohoto limitu je, že používáte protokol HTTPS `ReceiveAsync`pro příjem zprávy, což vede k nepřetržité dotazování pomocí , výsledkem je omezení služby IoT Hub požadavek.

## <a name="solution"></a>Řešení

Podporovaný vzor pro zprávy typu cloud zařízení s protokolem HTTPS je občas připojená zařízení, která zřídka kontrolují zprávy (méně než každých 25 minut). Chcete-li snížit pravděpodobnost spuštění do limitu fronty, přepněte na AMQP nebo MQTT pro zprávy mezi cloudem a zařízeními.

Případně můžete vylepšit logiku na straně zařízení, abyste rychle dokončili, odmítli nebo opustili zprávy ve frontě, zkrátili dobu života nebo zvážili odeslání menšího počtu zpráv. Viz téma věnované [hodnotě TTL (Time to Live) zpráv ve směru cloud-zařízení](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live).

Nakonec zvažte použití [rozhraní API pro vymazání fronty](https://docs.microsoft.com/rest/api/iothub/service/registrymanager/purgecommandqueue) k pravidelnému čištění čekajících zpráv před dosažením limitu.