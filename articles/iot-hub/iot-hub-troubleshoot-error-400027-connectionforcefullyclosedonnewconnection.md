---
title: Řešení potíží s chybami Azure IoT Hub 400027 ConnectionForcefullyClosedOnNewConnection
description: Vysvětlení, jak opravit chybu 400027 ConnectionForcefullyClosedOnNewConnection
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: cd531abe1a10abead26055c6b0d4dd328cd3e836
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061362"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

Tento článek popisuje příčiny a řešení 400027 chyb **ConnectionForcefullyClosedOnNewConnection** .

## <a name="symptoms"></a>Příznaky

Vaše zařízení se odpojí s **Communication_Error** jako **ConnectionStatusChangeReason** pomocí sady .NET SDK a typu přenosu MQTT.

Operace s dvojitým cloudem (jako jsou vlastnosti hlášené při čtení nebo opravě) nebo volání přímé metody se nezdařila s kódem chyby **400027**.

## <a name="cause"></a>Příčina

Jiný klient vytvořil nové připojení k IoT Hub pomocí stejných přihlašovacích údajů, takže IoT Hub zavřel předchozí připojení. IoT Hub neumožňuje připojení více než jednoho klienta pomocí stejné sady přihlašovacích údajů.

## <a name="solution"></a>Řešení

Ujistěte se, že se každý klient připojuje k IoT Hub pomocí vlastní identity.