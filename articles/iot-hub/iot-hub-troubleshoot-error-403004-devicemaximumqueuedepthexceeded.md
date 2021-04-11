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
ms.openlocfilehash: 3a8a63837617bc03ced475ae514f748e85d9b237
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061328"
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