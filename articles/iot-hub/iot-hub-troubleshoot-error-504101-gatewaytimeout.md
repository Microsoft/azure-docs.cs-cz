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
ms.openlocfilehash: b74a93e15d533bf9b15797e6371a25230f7a08f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960669"
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