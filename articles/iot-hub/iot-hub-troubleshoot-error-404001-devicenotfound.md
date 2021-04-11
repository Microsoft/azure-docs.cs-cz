---
title: Řešení potíží s chybami Azure IoT Hub 404001 DeviceNotFound
description: Vysvětlení, jak opravit chybu 404001 DeviceNotFound
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 09f3c00dadc6e95aae01fb8082fb746e155d4688
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061277"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

Tento článek popisuje příčiny a řešení 404001 chyb **DeviceNotFound** .

## <a name="symptoms"></a>Příznaky

Během komunikace typu cloud-zařízení (C2D), jako je například zpráva C2D, dvojitá aktualizace nebo přímá metoda, operace selže s chybou **404001 DeviceNotFound**.

## <a name="cause"></a>Příčina

Operace se nezdařila, protože zařízení nelze najít pomocí IoT Hub. Zařízení buď není zaregistrované, nebo je zakázané.

## <a name="solution"></a>Řešení

Zaregistrujte ID zařízení, které jste použili, a pak to zkuste znovu.