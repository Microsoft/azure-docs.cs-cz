---
title: Vysvětlení zasílání zpráv služby Azure IoT Hub | Microsoft Docs
description: Příručka pro vývojáře – zasílání zpráv ze zařízení do cloudu a z cloudu do zařízení pomocí IoT Hub. Obsahuje informace o formátech zpráv a podporovaných komunikačních protokolech.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: e25b9d6cbe8446fb2f10150f17f7e2b0ccf85d3c
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147620"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>Posílání zpráv ze zařízení do cloudu a z cloudu na zařízení pomocí IoT Hub

IoT Hub povoluje obousměrnou komunikaci s vašimi zařízeními. Pomocí IoT Hub zasílání zpráv pro komunikaci se zařízeními můžete posílat zprávy ze svých zařízení do back-endu řešení a odesílat příkazy z back-endu vašich řešení IoT do vašich zařízení. Přečtěte si další informace o [formátu IoT Hub zpráv](iot-hub-devguide-messages-construct.md).

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>Posílání zpráv ze zařízení do cloudu pro IoT Hub

IoT Hub má integrovaný koncový bod služby, který můžou používat back-end služby ke čtení zpráv telemetrie z vašich zařízení. Tento koncový bod je kompatibilní s [Event Hubs](../event-hubs/index.yml) a ke [čtení z tohoto integrovaného koncového bodu](iot-hub-devguide-messages-read-builtin.md)můžete použít standardní sady IoT Hub SDK.

IoT Hub také podporuje [vlastní koncové body](iot-hub-devguide-endpoints.md#custom-endpoints) , které mohou definovat uživatelé pro posílání dat telemetrie a událostí zařízení do služeb Azure pomocí [směrování zpráv](iot-hub-devguide-messages-d2c.md).

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>Posílání zpráv z cloudu na zařízení z IoT Hub

Můžete posílat zprávy z [cloudu na zařízení](iot-hub-devguide-messages-c2d.md) z back-endu řešení do zařízení.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Základní vlastnosti funkce zasílání zpráv IoT Hub jsou spolehlivosti a odolnosti zpráv. Tyto vlastnosti umožňují přerušované připojení na straně zařízení a načítají se špičky ve zpracování událostí na straně cloudu. Pro zasílání zpráv ze zařízení do cloudu i z cloudu do zařízení IoT Hub implementuje *alespoň* jednu záruku doručení.

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>Výběr správného typu IoT Hub zasílání zpráv

Zprávy ze zařízení do cloudu slouží k posílání telemetrie a výstrah časových řad z aplikace pro zařízení a zpráv z cloudu na zařízení pro jednosměrná oznámení do vaší aplikace zařízení.

* V tématu [Průvodce komunikací ze zařízení na Cloud](./iot-hub-devguide-d2c-guidance.md) si můžete vybrat mezi zprávami ze zařízení do cloudu, hlášenými vlastnostmi nebo nahráváním souborů.

* V tématu [doprovodné materiály k komunikaci z cloudu na zařízení](./iot-hub-devguide-c2d-guidance.md) si můžete vybrat mezi zprávami z cloudu na zařízení, požadovanými vlastnostmi nebo přímými metodami.

## <a name="next-steps"></a>Další kroky

* Přečtěte si o IoT Hub [směrování zpráv](iot-hub-devguide-messages-d2c.md).

* Přečtěte si o IoT Hub [zasílání zpráv z cloudu na zařízení](iot-hub-devguide-messages-c2d.md).