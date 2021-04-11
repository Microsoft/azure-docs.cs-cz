---
title: Řešení potíží s chybami Azure IoT Hub 504101 GatewayTimeout
description: Vysvětlení, jak opravit chybu 504101 GatewayTimeout
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 5d5962c43a8a3cd97b31ad3fa50bce774e2626cb
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060937"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

Tento článek popisuje příčiny a řešení 504101 chyb **GatewayTimeout** .

## <a name="symptoms"></a>Příznaky

Při pokusu o vyvolání přímé metody z IoT Hub do zařízení, požadavek se nezdařil s chybou **504101 GatewayTimeout**.

## <a name="cause"></a>Příčina

### <a name="cause-1"></a>Příčina 1

IoT Hub zjistila chybu a nemohla potvrdit, zda byla přímá metoda dokončena před vypršením časového limitu.

### <a name="cause-2"></a>Příčina 2

Pokud používáte starší verzi sady Azure IoT C# SDK (<1.19.0), propojení AMQP mezi zařízením a IoT Hub se dá v tichém režimu vyřadit z důvodu chyby.

## <a name="solution"></a>Řešení

### <a name="solution-1"></a>Řešení 1

Vydejte problém znovu.

### <a name="solution-2"></a>Řešení 2

Upgradujte na nejnovější verzi sady Azure IOT C# SDK.