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
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "76960812"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

Tento článek popisuje příčiny a řešení 404103 chyb **DeviceNotOnline** .

## <a name="symptoms"></a>Příznaky

Přímá metoda pro zařízení se nezdařila s chybou **404103 DeviceNotOnline** i v případě, že je zařízení online. 

## <a name="cause"></a>Příčina

Pokud víte, že je zařízení online, a pořád se zobrazí chyba, je pravděpodobnou příčinou to, že zpětné volání přímé metody není v zařízení zaregistrované.

## <a name="solution"></a>Řešení

Chcete-li správně nakonfigurovat vaše zařízení pro zpětná volání metod, přečtěte si téma [zpracování přímé metody v zařízení](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device).