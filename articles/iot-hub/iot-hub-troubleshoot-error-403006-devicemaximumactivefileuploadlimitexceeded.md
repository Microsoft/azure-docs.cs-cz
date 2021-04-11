---
title: Řešení potíží s chybami Azure IoT Hub 403006 DeviceMaximumActiveFileUploadLimitExceeded
description: Vysvětlení, jak opravit chybu 403006 DeviceMaximumActiveFileUploadLimitExceeded
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 3a70c11fd4f2a0549370933c300f431a03ffcf1d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061294"
---
# <a name="403006-devicemaximumactivefileuploadlimitexceeded"></a>403006 DeviceMaximumActiveFileUploadLimitExceeded

Tento článek popisuje příčiny a řešení 403006 chyb **DeviceMaximumActiveFileUploadLimitExceeded** .

## <a name="symptoms"></a>Příznaky

Vaše žádost o nahrání souboru se nezdařila s kódem chyby **403006** a zpráva "počet aktivních požadavků na nahrání souboru nesmí překročit 10".

## <a name="cause"></a>Příčina

Každý klient zařízení je omezený na [10 souběžných nahrávání souborů](./iot-hub-devguide-quotas-throttling.md#other-limits). 

Limit můžete snadno překročit, pokud zařízení neupozorní IoT Hub, když se dokončí nahrávání souborů. Tento problém je obvykle způsoben nespolehlivou sítí na straně zařízení.

## <a name="solution"></a>Řešení

Ujistěte se, že zařízení může zobrazit výzvu [IoT Hub dokončení nahrávání souboru](./iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload). Pak zkuste [snížit hodnotu TTL tokenu SAS pro konfiguraci nahrávání souborů](iot-hub-configure-file-upload.md).

## <a name="next-steps"></a>Další kroky

Další informace o nahrávání souborů najdete v tématu [nahrání souborů pomocí IoT Hub](./iot-hub-devguide-file-upload.md) a [Konfigurace IoT Hub souborů pro nahrávání pomocí Azure Portal](./iot-hub-configure-file-upload.md).