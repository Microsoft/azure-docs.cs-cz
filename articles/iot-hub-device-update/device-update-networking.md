---
title: Aktualizace zařízení pro IoT Hub požadavky na síť | Microsoft Docs
description: Aktualizace zařízení pro IoT Hub používá různé síťové porty pro různé účely.
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e72ff144a56f44ccaa695b7dab328e42052fce39
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101679280"
---
# <a name="ports-used-with-device-update-for-iot-hub"></a>Porty používané při aktualizaci zařízení pro IoT Hub
ADU používá různé síťové porty pro různé účely.

## <a name="default-ports"></a>Výchozí porty

Účel|Číslo portu |
---|---
Stažení ze sítě/sítě CDN  | 80 (protokol HTTP)
Stažení z MCC/sítě CDN | 80 (protokol HTTP)
Připojení agenta ADU k Azure IoT Hub  | 8883 (protokol MQTT)

## <a name="use-azure-iot-hub-supported-protocols"></a>Použít podporované protokoly Azure IoT Hub
Agent ADU se dá upravit tak, aby používal některý z podporovaných protokolů Azure IoT Hub.

[Přečtěte si další informace](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols#:~:text=Table%202%20%20%20,%201%20more%20rows) o aktuálním seznamu podporovaných protokolů.
