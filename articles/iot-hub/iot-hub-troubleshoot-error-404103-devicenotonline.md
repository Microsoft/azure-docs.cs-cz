---
title: Řešení potíží s chybami Azure IoT Hub 404103 DeviceNotOnline
description: Vysvětlení, jak opravit chybu 404103 DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: ed4341c1c8df588bf735bdc9c531c2165514d610
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061260"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

Tento článek popisuje příčiny a řešení 404103 chyb **DeviceNotOnline** .

## <a name="symptoms"></a>Příznaky

Přímá metoda pro zařízení se nezdařila s chybou **404103 DeviceNotOnline** i v případě, že je zařízení online. 

## <a name="cause"></a>Příčina

Pokud víte, že je zařízení online, a pořád se zobrazí chyba, je pravděpodobnou příčinou to, že zpětné volání přímé metody není v zařízení zaregistrované.

## <a name="solution"></a>Řešení

Chcete-li správně nakonfigurovat vaše zařízení pro zpětná volání metod, přečtěte si téma [zpracování přímé metody v zařízení](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device).