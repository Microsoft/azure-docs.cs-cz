---
title: Připojení zařízení v Azure IoT centrální | Microsoft Docs
description: Tento článek představuje klíčové koncepty týkající se připojení zařízení v Azure IoT centrální
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 11/30/2017
ms.topic: conceptual
ms.prod: microsoft-iot-central
manager: timlt
ms.openlocfilehash: 00b621a4635ef1ceda26772ac5876fa2599b56f8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="device-connectivity-in-azure-iot-central"></a>Připojení zařízení v Azure IoT centrální

Tento článek představuje klíčové koncepty týkající se připojení zařízení v Microsoft Azure IoT centrální.

Každé zařízení, která se připojuje k vaší aplikaci Azure IoT centrální, připojí k [koncové body](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints) , jsou zveřejněné službou IoT hub, Azure IoT centrální používá. IoT Hub umožňuje škálovatelnou, zabezpečenou a spolehlivou připojení z připojených zařízení.

## <a name="sdk-support"></a>Podpora v sadě SDK

Nabídka SDK služby Azure zařízení nejjednodušší můžete implementovat kód na zařízení, která se připojuje k Azure IoT centrální aplikaci. K dispozici jsou následující sady SDK pro zařízení:

- [Azure IoT SDK pro jazyk C](https://github.com/azure/azure-iot-sdk-c)
- [Azure IoT SDK pro Python](https://github.com/azure/azure-iot-sdk-python)
- [Azure IoT SDK pro Node.js](https://github.com/azure/azure-iot-sdk-node)
- [Azure IoT SDK pro jazyk Java](https://github.com/azure/azure-iot-sdk-java)
- [Azure IoT SDK pro .NET](https://github.com/azure/azure-iot-sdk-csharp)

Každé zařízení připojí pomocí jedinečné připojovacího řetězce, které identifikují zařízení. Zařízení lze připojit pouze ke službě IoT hub, kde je zaregistrován. Když vytvoříte skutečné zařízení v aplikaci Azure IoT centrální, aplikace generuje připojovací řetězec k použití.

## <a name="sdk-features-and-iot-hub-connectivity"></a>Funkce sady SDK a IoT Hub připojení

Veškerá komunikace zařízení s centrem IoT používá následující možnosti připojení služby IoT Hub:

- [Zasílání zpráv typu zařízení cloud](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-d2c)
- [Dvojčata zařízení](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins)

Následující tabulka shrnuje, jak Azure IoT centrální funkcí zařízení mapovat k funkcí služby IoT Hub:

| Azure IoT Central | Azure IoT Hub |
| ----------- | ------- |
| Měření: Telemetrie | Zasílání zpráv typu zařízení cloud |
| Vlastnosti zařízení | Dvojče zařízení hlášené vlastnosti |
| Nastavení | Dvojče zařízení potřeby a který ohlásil, vlastnosti |

Další informace o použití sady SDK zařízení, najdete v jednom z následujících článků například kódu:

- [Obecné Node.js klienta připojit k Azure IoT centrální aplikace](howto-connect-nodejs.md)
- [Připojit k aplikaci Azure IoT centrální malin platformy zařízení](howto-connect-raspberry-pi-python.md)
- [Připojení zařízení kit DevDiv do aplikace Azure IoT centrální](howto-connect-devkit.md).

Následující video obsahuje přehled toho, jak se připojit k Azure IoT centrální skutečné zařízení:

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Connect-real-devices-to-Microsoft-IoT-Central/Player]

## <a name="protocols"></a>Protokoly

Sady SDK zařízení podporují následující síťové protokoly pro připojení do služby IoT hub:

- MQTT
- AMQP
- HTTPS

Informace o těchto rozdíl protokoly a pokyny na zvolili jeden najdete v tématu [zvolte komunikační protokol](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-protocols).

Pokud vaše zařízení nemůžete použít žádnou z podporovaných protokolů, můžete použít Azure IoT Edge protokolu převod. Okraj IoT podporuje scénáře intelligence na the-edge k přesměrování zpracování na hranici z Azure IoT centrální aplikace.

## <a name="security"></a>Zabezpečení

Všechna data, které se vyměňují mezi zařízením a vaší Azure IoT centrální je zašifrovaná. IoT Hub ověřuje každou žádost ze zařízení, která se připojuje k žádné koncové body centra IoT směřujících zařízení. Abyste se vyhnuli výměna přihlašovací údaje prostřednictvím sítě, zařízení k ověřování používá podepsané tokeny. Další informace najdete v tématu, [řízení přístupu ke službě IoT Hub](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-security).

> [!NOTE]
> V současné době používaly zařízení, která se připojují k Azure IoT centrální tokeny SAS. X.509 – certifikáty nejsou podporovány pro zařízení, která se připojují k centrální Azure IoT.

## <a name="next-steps"></a>Další postup

Teď, když jste se naučili o připojení zařízení v Azure IoT centrální, tady jsou navrhované další kroky:

- [Příprava a připojení zařízení DevKit](howto-connect-devkit.md)
- [Příprava a připojte malin platformy](howto-connect-raspberry-pi-python.md)
- [Obecné Node.js klienta připojit k Azure IoT centrální aplikace](howto-connect-nodejs.md)
