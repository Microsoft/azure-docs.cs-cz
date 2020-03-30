---
title: Řešení potíží s chybou služby Azure IoT Hub 409001 DeviceAlreadyExists
description: Pochopit, jak opravit chybu 409001 DeviceAlreadyExists
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.openlocfilehash: 93ab2ecc8e820c461a7c79082ac1d50c24f0ba8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76960786"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

Tento článek popisuje příčiny a řešení pro **409001 DeviceAlreadyExists chyby.**

## <a name="symptoms"></a>Příznaky

Při pokusu o registraci zařízení v centru IoT Hub, požadavek se nezdaří s chybou **409001 DeviceAlreadyExists**.

## <a name="cause"></a>Příčina

V centru IoT hubu už existuje zařízení se stejným ID zařízení. 

## <a name="solution"></a>Řešení

Použijte jiné ID zařízení a akci opakujte.