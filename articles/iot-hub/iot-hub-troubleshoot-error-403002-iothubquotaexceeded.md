---
title: Řešení potíží s chybou služby Azure IoT Hub 403002 IoTHubQuotaExceeded
description: Pochopit, jak opravit chybu 403002 IoTHubQuotaExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 8312c3267e826088f34b3bffe1520703eec00bdc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76961111"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

Tento článek popisuje příčiny a řešení pro **403002 IoTHubQuotaExceeded** chyby.

## <a name="symptoms"></a>Příznaky

Všechny požadavky na službu IoT Hub se nezdaří s chybou **403002 IoTHubQuotaExceeded**. Na webu Azure Portal se seznam zařízení služby IoT hub nenačte.

## <a name="cause"></a>Příčina

Je překročena kvóta denních zpráv pro službu IoT hub. 

## <a name="solution"></a>Řešení

[Upgradujte nebo zvyšte počet jednotek v centru IoT](iot-hub-upgrade.md) hub nebo počkejte na další den utc pro aktualizaci denní kvóty.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak se operace započítávají do kvóty, jako jsou dotazy dvojčat a přímé metody, najdete [v tématu Principy cen služby IoT Hub](iot-hub-devguide-pricing.md#charges-per-operation)
* Chcete-li nastavit monitorování denního využití kvót, nastavte výstrahu s metrikou *Celkový počet použitých zpráv*. Podrobné pokyny najdete v tématu [Nastavení metrik a výstrah pomocí centra IoT Hub.](tutorial-use-metrics-and-diags.md#set-up-metrics)