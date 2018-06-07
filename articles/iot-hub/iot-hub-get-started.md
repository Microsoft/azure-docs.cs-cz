---
title: Azure IoT Hub - začít pracovat s připojením zařízení IoT ke cloudu | Microsoft Docs
description: Zjistěte, jak se připojit ke službě Azure IoT Hub panely IoT a Startovní sady. Zařízení může odesílat telemetrická data do služby IoT Hub a IoT Hub můžete sledovat a spravovat vaše zařízení.
author: dominicbetts
manager: timlt
keywords: Azure iot hub kurzu
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 76f427204f0ad31196ce8b995b9e4ed9676209ca
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634698"
---
# <a name="azure-iot-hub-get-started-with-real-devices"></a>Azure IoT Hub začít pracovat s skutečné zařízení

Azure IoT Hub a sady SDK zařízení Azure IoT můžete použít k vytvoření řešení Internetu věcí (IoT):

* Azure IoT Hub je plně spravovaná služba v cloudu, které bezpečně připojit, monitoruje a spravuje zařízení IoT. Implementace zařízení IoT pomocí sady SDK zařízení IoT v Azure.
* Používejte bránu IoT v složitější scénáře IoT. Například, kdy potřebujete vezměte v úvahu faktory, jako jsou zařízení se starší verzí, náklady na šířku pásma, zásady zabezpečení a ochrana osobních údajů nebo zpracování dat okraj. V těchto scénářích použití [Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) implementovat bránu, která se připojuje zařízení do služby IoT hub.

## <a name="what-the-how-to-articles-cover"></a>Co zahrnují články s návody

Tyto články vám představí Azure IoT Hub a sady SDK pro zařízení. Články zahrnují běžné scénáře IoT k předvedení funkcí služby IoT Hub. Články také znázorňují, jak kombinovat s jinými službami Azure a nástroje pro vytváření výkonnější IoT řešení IoT Hub. V článcích použijte skutečné zařízení IoT.

## <a name="set-up-your-device"></a>Nastavení zařízení

Připojení zařízení IoT nebo brány Azure IoT Hub:

| Zařízení IoT                       | Programovací jazyk |
|----------------------------------|----------------------|
| Raspberry Pi                     | [Python][Pi_Py], [Node.js][Pi_Nd], [C][Pi_C]  |
| IoT DevKit                       | [Arduino v VSCode][DevKit]     |
| Intel Edison                     | [Node.js][Ed_Nd], [C][Ed_C]    |
| Prolnutí Adafruit HUZZAH ESP8266  | [Arduino][Hu_Ard]              |
| Sparkfun ESP8266 věc vývojářů       | [Arduino][Th_Ard]              |
| Prolnutí Adafruit M0              | [Arduino][M0_Ard]              |
| Simulátor online zařízení         | [Malinová platformy (Node.js)][Ol_Sim] |

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
