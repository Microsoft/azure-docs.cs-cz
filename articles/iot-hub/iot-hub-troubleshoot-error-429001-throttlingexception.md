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
ms.openlocfilehash: 3095e398d7e5cfe59085144d5bb4e8dc33618064
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76960695"
---
# <a name="429001-throttlingexception"></a>429001 ThrottlingException

Tento článek popisuje příčiny a řešení 429001 chyb **ThrottlingException** .

## <a name="symptoms"></a>Příznaky

Vaše požadavky na IoT Hub selžou s chybou **429001 ThrottlingException**.

## <a name="cause"></a>Příčina

Pro požadovanou operaci byly překročeny [limity omezení](./iot-hub-devguide-quotas-throttling.md) IoT Hub.

## <a name="solution"></a>Řešení

Podívejte se, jestli jste nedosáhli limitu omezování, porovnáním metriky *odesílání pokusů o odeslání zpráv* na základě výše uvedených omezení. Můžete také kontrolovat metriky *chyb při omezování* . Další informace o těchto a dalších metrikách, které jsou k dispozici pro IoT Hub, najdete v tématu [IoT Hub metriky a jejich použití](./iot-hub-metrics.md#iot-hub-metrics-and-how-to-use-them).

IoT Hub vrátí 429 ThrottlingException až po porušení limitu příliš dlouhého období. To se provádí tak, že vaše zprávy nejsou vyřazené, pokud vaše centrum IoT získá nárůst provozu. IoT Hub mezitím zpracovává zprávy rychlostí odpovídající omezení šířky pásma, která může být nízká, pokud je v backlogu příliš velký provoz. Další informace najdete v tématu věnovaném [formování provozu služby IoT Hub](./iot-hub-devguide-quotas-throttling.md#traffic-shaping).

## <a name="next-steps"></a>Další kroky

Pokud používáte omezení kvóty nebo omezování, zvažte možnost horizontálního [navýšení kapacity IoT Hub](./iot-hub-scaling.md) .