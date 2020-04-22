---
title: Poradce při potížích s chybou služby Azure IoT Hub 504101 GatewayTimeout
description: Pochopit, jak opravit chybu 504101 GatewayTimeout
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 373acc30ed652a7f540e840dfad5eeeda65ca179
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759554"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

Tento článek popisuje příčiny a řešení pro **chyby 504101 GatewayTimeout.**

## <a name="symptoms"></a>Příznaky

Při pokusu o vyvolání přímé metody z ioT hubu do zařízení se požadavek nezdaří s chybou **504101 GatewayTimeout**.

## <a name="cause"></a>Příčina

### <a name="cause-1"></a>Příčina 1

IoT Hub zjistil chybu a nemohl potvrdit, pokud byla před vypršením časového limitu dokončena přímá metoda.

### <a name="cause-2"></a>Příčina 2

Při použití starší verze sady Azure IoT C# SDK (<1.19.0) lze z důvodu chyby bezobslužně vynechat propojení AMQP mezi zařízením a službou IoT Hub.

## <a name="solution"></a>Řešení

### <a name="solution-1"></a>Řešení 1

Vystaví opakování.

### <a name="solution-2"></a>Řešení 2

Upgradujte na nejnovější verzi sady Azure IOT C# SDK.