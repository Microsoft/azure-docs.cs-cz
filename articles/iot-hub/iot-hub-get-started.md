---
title: Azure IoT Hub – Začínáme s propojováním zařízení IoT do cloudu | Dokumentace Microsoftu
description: Zjistěte, jak připojit IoT tabulí a úvodních sad do služby Azure IoT Hub. Vaše zařízení můžete odesílání telemetrických dat do služby IoT Hub a IoT Hub můžete monitorovat a spravovat vaše zařízení.
author: dominicbetts
manager: timlt
keywords: kurz Azure iot hub
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: 77abe7e2187a3cb28b326ffa833a856625d6c33d
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125188"
---
# <a name="azure-iot-hub-get-started-with-real-devices"></a>Azure IoT Hub vám umožní začít skutečných zařízení

Tyto články s postupy vám představí službu Azure IoT Hub a spouštění sad SDK pro zařízení na skutečných zařízeních.

## <a name="set-up-your-device"></a>Nastavení zařízení

Připojte se k zařízení IoT nebo brány pro službu Azure IoT Hub:

| Zařízení IoT                       | Programovací jazyk |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| IoT DevKit                       | [Arduino ve VSCode][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd], [C][Ed_C]    |
| Sada Adafruit Feather HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 Thing Dev       | [Arduino][Th_Ard]              |
| Sada Adafruit Feather M0              | [Arduino][M0_Ard]              |
| Online simulátoru zařízení         | [Raspberry Pi (Node.js)][Ol_Sim] |

[!INCLUDE [iot-hub-get-started-extended](../../includes/iot-hub-get-started-extended.md)]

[Pi_Nd]: iot-hub-raspberry-pi-kit-node-get-started.md
[Pi_C]: iot-hub-raspberry-pi-kit-c-get-started.md
[Pi_Py]: iot-hub-raspberry-pi-kit-python-get-started.md
[DevKit]: iot-hub-arduino-iot-devkit-az3166-get-started.md
[Ed_Nd]: iot-hub-intel-edison-kit-node-get-started.md
[Ed_C]: iot-hub-intel-edison-kit-c-get-started.md
[Hu_Ard]: iot-hub-arduino-huzzah-esp8266-get-started.md
[Th_Ard]: iot-hub-sparkfun-esp8266-thing-dev-get-started.md
[M0_Ard]: iot-hub-adafruit-feather-m0-wifi-kit-arduino-get-started.md
[Ol_Sim]: iot-hub-raspberry-pi-web-simulator-get-started.md
