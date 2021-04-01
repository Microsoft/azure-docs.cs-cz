---
title: Řešení potíží s chybami Azure IoT Hub 403002 IoTHubQuotaExceeded
description: Vysvětlení, jak opravit chybu 403002 IoTHubQuotaExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 8312c3267e826088f34b3bffe1520703eec00bdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "76961111"
---
# <a name="403002-iothubquotaexceeded"></a>403002 IoTHubQuotaExceeded

Tento článek popisuje příčiny a řešení 403002 chyb **IoTHubQuotaExceeded** .

## <a name="symptoms"></a>Příznaky

Všechny požadavky na IoT Hub selžou s chybou  **403002 IoTHubQuotaExceeded**. V Azure Portal se seznam zařízení centra IoT Hub nenačte.

## <a name="cause"></a>Příčina

Překročila se kvóta denních zpráv pro Centrum IoT. 

## <a name="solution"></a>Řešení

[Upgradujte nebo zvyšte počet jednotek ve službě IoT Hub](iot-hub-upgrade.md) nebo počkejte na další den UTC, kdy se má denní kvóta aktualizovat.

## <a name="next-steps"></a>Další kroky

* Informace o tom, jak se počítají operace s kvótou, jako jsou například zdvojené dotazy a přímé metody, najdete v tématu [vysvětlení IoT Hub ceny](iot-hub-devguide-pricing.md#charges-per-operation) .
* Pokud chcete nastavit monitorování denního využití kvót, nastavte upozornění s *celkovým počtem použitých zpráv*. Podrobné pokyny najdete v tématu [nastavení metrik a výstrah pomocí IoT Hub](tutorial-use-metrics-and-diags.md#set-up-metrics)