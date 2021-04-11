---
title: Řešení potíží s chybami Azure IoT Hub 409001 DeviceAlreadyExists
description: Vysvětlení, jak opravit chybu 409001 DeviceAlreadyExists
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: b075519fff2c7c328778d770ef68e9751922807b
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061124"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

Tento článek popisuje příčiny a řešení 409001 chyb **DeviceAlreadyExists** .

## <a name="symptoms"></a>Příznaky

Při pokusu o registraci zařízení v IoT Hub se požadavek nezdařil s chybou **409001 DeviceAlreadyExists**.

## <a name="cause"></a>Příčina

Ve službě IoT Hub už existuje zařízení se stejným ID zařízení. 

## <a name="solution"></a>Řešení

Použijte jiné ID zařízení a zkuste to znovu.