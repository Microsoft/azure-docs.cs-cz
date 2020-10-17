---
title: Řešení potíží s chybami Azure IoT Hub 403004 DeviceMaximumQueueDepthExceeded
description: Vysvětlení, jak opravit chybu 403004 DeviceMaximumQueueDepthExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 421066ef30e23a79b26f97939cdfffb5be83afb5
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92148242"
---
# <a name="403004-devicemaximumqueuedepthexceeded"></a>403004 DeviceMaximumQueueDepthExceeded

Tento článek popisuje příčiny a řešení 403004 chyb **DeviceMaximumQueueDepthExceeded** .

## <a name="symptoms"></a>Příznaky

Když se pokusíte odeslat zprávu typu cloud-zařízení, požadavek se nezdařil s chybou **403004** nebo **DeviceMaximumQueueDepthExceeded**.

## <a name="cause"></a>Příčina

Základní příčinou je, že počet zpráv zařazených do fronty pro zařízení překračuje [limit fronty (50)](./iot-hub-devguide-quotas-throttling.md#other-limits).

Nejpravděpodobnějším důvodem, že toto omezení spouštíte, je to, že k přijetí zprávy používáte protokol HTTPS, který vede k průběžnému cyklickému dotazování s využitím `ReceiveAsync` . Výsledkem je IoT Hub omezení žádosti.

## <a name="solution"></a>Řešení

Podporovaným vzorům pro zprávy typu cloud-zařízení pomocí protokolu HTTPS jsou občas připojená zařízení, která kontrolují zprávy zřídka (méně než každých 25 minut). Pokud chcete snížit pravděpodobnost běhu do limitu fronty, přepněte na zprávy typu cloud-zařízení na AMQP nebo MQTT.

Alternativně můžete vylepšit logiku na straně zařízení, aby se rychle dokončily, odmítaly nebo vybraly zprávy ve frontě, zkrátila se doba do živého přenosu, nebo zvažte odeslání méně zpráv Viz téma věnované [hodnotě TTL (Time to Live) zpráv ve směru cloud-zařízení](./iot-hub-devguide-messages-c2d.md#message-expiration-time-to-live).

Nakonec zvažte použití [rozhraní API fronty vyprázdnění](/azure/iot-hub/iot-c-sdk-ref/iothub-registrymanager-h/iothubregistrymanager-deletedevice) k pravidelnému vyčištění čekajících zpráv před dosažením limitu.