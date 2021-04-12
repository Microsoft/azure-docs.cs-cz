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
ms.openlocfilehash: 3521933baa8dc328910fbacd8b1b6768832fa5f1
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061311"
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