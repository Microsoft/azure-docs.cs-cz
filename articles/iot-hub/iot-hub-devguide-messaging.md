---
title: Vysvětlení, zasílání zpráv služby Azure IoT Hub | Dokumentace Microsoftu
description: Příručka pro vývojáře – typu zařízení cloud a cloud zařízení pomocí služby IoT Hub pro zasílání zpráv. Obsahuje informace o formáty zpráv a podporovaných komunikační protokoly.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: d3a7284555fb592956d4e1dc3f56137c88d108e1
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47584385"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>Odesílání zpráv typu zařízení cloud a cloud zařízení pomocí služby IoT Hub

IoT Hub umožňuje obousměrnou komunikaci ve vašich zařízeních. IoT Hub pomocí zasílání zpráv pro komunikaci ve vašich zařízeních odesíláním zpráv ze zařízení do řešení zpět ukončit a odesílání příkazů z vašeho IoT řešení back-endu pro vaše zařízení. Další informace o [formát zprávy služby IoT Hub](iot-hub-devguide-messages-construct.md).

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>Odesílání zpráv typu zařízení cloud do služby IoT Hub

IoT Hub má integrované služby koncového bodu, který je možné ke čtení telemetrických zpráv ze zařízení back endovými službami. Tento koncový bod je kompatibilní s [Event Hubs](https://docs.microsoft.com/azure/event-hubs/) a můžete použít standardní sady SDK IoT Hub do [čtení z koncového bodu této integrované](iot-hub-devguide-messages-read-builtin.md).

IoT Hub podporuje také [vlastní koncové body](iot-hub-devguide-endpoints.md#custom-endpoints) uživatelům odesílat telemetrická data zařízení a událostí pomocí služby Azure, který může definovat [směrování zpráv](iot-hub-devguide-messages-d2c.md).

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>Odesílání zpráv typu cloud zařízení ze služby IoT Hub

Můžete odeslat [typu cloud zařízení](iot-hub-devguide-messages-c2d.md) zprávy z řešení back-endu pro vaše zařízení.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Základní vlastnosti funkce zasílání zpráv služby IoT Hub se spolehlivost a odolnost zpráv. Tyto vlastnosti zajištěna odolnost k přerušovaným připojením na straně zařízení a načíst poraďte se špičkami na straně cloudu pro zpracování událostí. IoT Hub implementuje *alespoň jednou* záruky doručení pro zasílání zpráv typu zařízení cloud a cloud zařízení.

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>Výběr správného typu zasílání zpráv služby IoT Hub

Jednosměrná oznámení do aplikace pro zařízení pomocí zpráv typu zařízení cloud pro odesílání čas řady telemetrii a upozornění z aplikace pro zařízení a zprávy typu cloud zařízení.

* Odkazovat na [pokyny komunikace typu zařízení cloud](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-d2c-guidance) na výběr mezi zprávy typu zařízení cloud, ohlášené vlastnosti nebo odeslat soubor.

* Odkazovat na [pokyny komunikaci typu Cloud zařízení](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-c2d-guidance) si vybrat mezi zprávy typu cloud zařízení, požadovaných vlastností nebo přímých metod.

## <a name="next-steps"></a>Další postup

* Další informace o službě IoT Hub [směrování zpráv](iot-hub-devguide-messages-d2c.md).

* Další informace o službě IoT Hub [zasílání zpráv typu cloud zařízení](iot-hub-devguide-messages-c2d.md).