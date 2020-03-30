---
title: Poradce při potížích s chybou služby Azure IoT Hub 404001 DeviceNotFound
description: Pochopit, jak opravit chybu 404001 DeviceNotFound
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 15aa21c2ec2c11bb251f7208fd22c92ceb859d6d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960825"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

Tento článek popisuje příčiny a řešení pro **404001 DeviceNotFound** chyby.

## <a name="symptoms"></a>Příznaky

Během komunikace mezi cloud-zařízení (C2D), jako je například zpráva C2D, aktualizace dvojčete nebo přímá metoda, operace se nezdaří s chybou **404001 DeviceNotFound**.

## <a name="cause"></a>Příčina

Operace se nezdařila, protože zařízení nelze najít pomocí aplikace IoT Hub. Zařízení není registrované nebo zakázané.

## <a name="solution"></a>Řešení

Zaregistrujte ID zařízení, které jste použili, a akci opakujte.