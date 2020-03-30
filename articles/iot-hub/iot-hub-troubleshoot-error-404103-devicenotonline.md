---
title: Poradce při potížích s chybou služby Azure IoT Hub 404103 DeviceNotOnline
description: Pochopit, jak opravit chybu 404103 DeviceNotOnline
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: e648428f924cfc33421c8591c41f7ac85b05a033
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960812"
---
# <a name="404103-devicenotonline"></a>404103 DeviceNotOnline

Tento článek popisuje příčiny a řešení pro **404103 DeviceNotOnline** chyby.

## <a name="symptoms"></a>Příznaky

Přímá metoda zařízení selže s chybou **404103 DeviceNotOnline** i v případě, že je zařízení online. 

## <a name="cause"></a>Příčina

Pokud víte, že zařízení je online a stále se zobrazí chyba, je to pravděpodobně proto, že přímé volání metody není v zařízení registrováno.

## <a name="solution"></a>Řešení

Chcete-li zařízení správně nakonfigurovat pro zpětná volání přímých metod, [přečtěte si](iot-hub-devguide-direct-methods.md#handle-a-direct-method-on-a-device)informace o zpracování přímé metody na zařízení .