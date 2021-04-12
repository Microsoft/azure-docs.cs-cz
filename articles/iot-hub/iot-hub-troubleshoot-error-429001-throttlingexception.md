---
title: Řešení potíží s chybami Azure IoT Hub 429001 ThrottlingException
description: Vysvětlení, jak opravit chybu 429001 ThrottlingException
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 9619a3d2ba24e806f6c684a5576bce03d7e6b793
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060971"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

Tento článek popisuje příčiny a řešení 429001 chyb **ThrottlingException** .

## <a name="symptoms"></a>Příznaky

Vaše požadavky na IoT Hub selžou s chybou **429001 ThrottlingException**.

## <a name="cause"></a>Příčina

Pro požadovanou operaci byly překročeny [limity omezení](./iot-hub-devguide-quotas-throttling.md) IoT Hub.

## <a name="solution"></a>Řešení

Podívejte se, jestli jste nedosáhli limitu omezování, porovnáním metriky *odesílání pokusů o odeslání zpráv* na základě výše uvedených omezení. Můžete také kontrolovat metriky *chyb při omezování* . Informace o těchto metrikách najdete v tématu [metriky telemetrie zařízení](monitor-iot-hub-reference.md#device-telemetry-metrics). Informace o tom, jak používat metriky, které vám pomůžou s monitorováním služby IoT Hub, najdete v tématu [monitorování IoT Hub](monitor-iot-hub.md).

IoT Hub vrátí 429 ThrottlingException až po porušení limitu příliš dlouhého období. To se provádí tak, že vaše zprávy nejsou vyřazené, pokud vaše centrum IoT získá nárůst provozu. IoT Hub mezitím zpracovává zprávy rychlostí odpovídající omezení šířky pásma, která může být nízká, pokud je v backlogu příliš velký provoz. Další informace najdete v tématu věnovaném [formování provozu služby IoT Hub](./iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="next-steps"></a>Další kroky

Pokud používáte omezení kvóty nebo omezování, zvažte možnost horizontálního [navýšení kapacity IoT Hub](./iot-hub-scaling.md) .