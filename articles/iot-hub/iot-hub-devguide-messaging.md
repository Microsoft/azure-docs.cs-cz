---
title: Principy zasílání zpráv služby Azure IoT Hub | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – zasílání zpráv mezi zařízeními a cloud-to-zařízení pomocí služby IoT Hub. Obsahuje informace o formátech zpráv a podporovaných komunikačních protokolech.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: f56332fa7f53c729ffaa28ea375f043d1b4a3678
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60626243"
---
# <a name="send-device-to-cloud-and-cloud-to-device-messages-with-iot-hub"></a>Odesílání zpráv mezi zařízeními a cloudem a zařízeními pomocí služby IoT Hub

IoT Hub umožňuje obousměrnou komunikaci s vašimi zařízeními. Pomocí zasílání zpráv služby IoT Hub komunikujte se svými zařízeními odesíláním zpráv ze zařízení do back-endu řešení a odesíláním příkazů z back-endu řešení IoT do vašich zařízení. Přečtěte si další informace o [formátu zpráv služby IoT Hub](iot-hub-devguide-messages-construct.md).

## <a name="sending-device-to-cloud-messages-to-iot-hub"></a>Odesílání zpráv mezi zařízeními a cloudy do služby IoT Hub

Služba IoT Hub má integrovaný koncový bod služby, který můžou používat back-endové služby ke čtení telemetrických zpráv z vašich zařízení. Tento koncový bod je kompatibilní s [event huby](https://docs.microsoft.com/azure/event-hubs/) a můžete použít standardní sady SDK služby IoT Hub ke [čtení z tohoto předdefinovaného koncového bodu](iot-hub-devguide-messages-read-builtin.md).

IoT Hub také podporuje [vlastní koncové body,](iot-hub-devguide-endpoints.md#custom-endpoints) které mohou uživatelé definovat k odesílání telemetrických dat zařízení a událostí do služeb Azure pomocí [směrování zpráv](iot-hub-devguide-messages-d2c.md).

## <a name="sending-cloud-to-device-messages-from-iot-hub"></a>Odesílání zpráv z cloudu na zařízení ze služby IoT Hub

Do zařízení můžete odesílat zprávy [z cloudu na zařízení](iot-hub-devguide-messages-c2d.md) z back-endu řešení.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Základní vlastnosti funkcí zasílání zpráv služby IoT Hub jsou spolehlivost a odolnost zpráv. Tyto vlastnosti umožňují odolnost vůči přerušovanému připojení na straně zařízení a načítají špičky při zpracování událostí na straně cloudu. Služba IoT Hub implementuje *alespoň jednou* záruky doručení pro zasílání zpráv mezi zařízeními a cloudem.

## <a name="choosing-the-right-type-of-iot-hub-messaging"></a>Výběr správného typu zasílání zpráv služby IoT Hub

Zprávy mezi zařízeními a cloudem slouží k odesílání telemetrických časových řad a upozornění z aplikace zařízení a zpráv z cloudu na zařízení pro jednosměrná oznámení do aplikace zařízení.

* Informace o tom, že si můžete vybrat mezi zprávami mezi zařízeními a cloudy, ohlášenými vlastnostmi nebo nahráváním souborů, najdete v [pokynech pro komunikaci](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-d2c-guidance) mezi zařízeními a cloudy.

* V pokynech [ke komunikaci mezi cloudovými zařízeními](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-c2d-guidance) si můžete vybrat mezi zprávami mezi cloudy, požadovanými vlastnostmi nebo přímými metodami.

## <a name="next-steps"></a>Další kroky

* Informace o směrování [zpráv](iot-hub-devguide-messages-d2c.md)služby IoT Hub .

* Přečtěte si o [zasílání zpráv mezi cloudy a zařízeními služby](iot-hub-devguide-messages-c2d.md)IoT Hub .