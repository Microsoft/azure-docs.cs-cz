---
title: Poradce při potížích s chybou služby Azure IoT Hub 403006 DeviceMaximumActiveFileUploadLimitExceeded
description: Pochopit, jak opravit chybu 403006 DeviceMaximumActiveFileUploadLimitPřekročeno
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 1e3c05e4cc3ccf34573b55d3729aded16e26d66e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960838"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

Tento článek popisuje příčiny a řešení pro **403006 DeviceMaximumActiveFileUploadLimitExceeded** chyby.

## <a name="symptoms"></a>Příznaky

Váš požadavek na nahrání souboru se nezdaří s kódem chyby **403006** a zprávou "Počet aktivních požadavků na odeslání souboru nesmí překročit 10".

## <a name="cause"></a>Příčina

Každý klient zařízení je omezen na [10 souběžných odeslání souborů](./iot-hub-devguide-quotas-throttling.md#other-limits). 

Pokud vaše zařízení neupozorní IoT Hub na dokončení nahrávání souborů, můžete tento limit snadno překročit. Tento problém je obvykle způsoben nespolehlivé sítě na straně zařízení.

## <a name="solution"></a>Řešení

Ujistěte se, že zařízení může okamžitě [upozornit dokončení odesílání souborů v centru IoT](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload). Potom zkuste [snížit token SAS TTL pro konfiguraci nahrávání souborů](iot-hub-configure-file-upload.md).

## <a name="next-steps"></a>Další kroky

Další informace o nahrávání souborů najdete v tématu Nahrávání souborů pomocí služby [IoT Hub](./iot-hub-devguide-file-upload.md) a [Konfigurace nahrávání souborů služby IoT Hub pomocí portálu Azure .](./iot-hub-configure-file-upload.md)