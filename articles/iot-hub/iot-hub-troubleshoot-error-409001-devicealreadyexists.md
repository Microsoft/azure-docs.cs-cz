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
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "76960786"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

Tento článek popisuje příčiny a řešení 409001 chyb **DeviceAlreadyExists** .

## <a name="symptoms"></a>Příznaky

Při pokusu o registraci zařízení v IoT Hub se požadavek nezdařil s chybou **409001 DeviceAlreadyExists**.

## <a name="cause"></a>Příčina

Ve službě IoT Hub už existuje zařízení se stejným ID zařízení. 

## <a name="solution"></a>Řešení

Použijte jiné ID zařízení a zkuste to znovu.