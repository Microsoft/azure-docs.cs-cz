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
ms.openlocfilehash: b74a93e15d533bf9b15797e6371a25230f7a08f7
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2020
ms.locfileid: "76960669"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

Tento článek popisuje příčiny a řešení 504101 chyb **GatewayTimeout** .

## <a name="symptoms"></a>Příznaky

Při pokusu o vyvolání přímé metody z IoT Hub do zařízení, požadavek se nezdařil s chybou **504101 GatewayTimeout**.

## <a name="cause"></a>Příčina

### <a name="cause-1"></a>Příčiny 1

IoT Hub zjistila chybu a nemohla potvrdit, zda byla přímá metoda dokončena před vypršením časového limitu.

### <a name="cause-2"></a>Příčiny 2

Pokud používáte starší verzi sady Azure IoT C# SDK (< 1.19.0), propojení AMQP mezi zařízením a IoT Hub se dá v tichém režimu vyřadit z důvodu chyby.

## <a name="solution"></a>Řešení

### <a name="solution-1"></a>Řešení 1

Vydejte problém znovu.

### <a name="solution-2"></a>Řešení 2

Upgradujte na nejnovější verzi sady Azure IOT C# SDK.