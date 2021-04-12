---
title: Aktualizace zařízení pro IoT Hub požadavky na síť | Microsoft Docs
description: Aktualizace zařízení pro IoT Hub používá různé síťové porty pro různé účely.
author: philmea
ms.author: philmea
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0512308fbaa0a725c6ecca573c70c90d8c04e247
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105558376"
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

[Přečtěte si další informace](../iot-hub/iot-hub-devguide-protocols.md) o aktuálním seznamu podporovaných protokolů.
