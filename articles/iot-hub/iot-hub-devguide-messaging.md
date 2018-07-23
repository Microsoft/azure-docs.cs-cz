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
ms.openlocfilehash: b0667f820145f16c75a07ebe1849e20d2de36cc7
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39185505"
---
# <a name="device-to-cloud-and-cloud-to-device-messaging-with-iot-hub"></a>Typu zařízení cloud a cloud zařízení pomocí služby IoT Hub pro zasílání zpráv

Pomocí zasílání zpráv pro komunikaci ve vašich zařízeních pomocí služby IoT Hub:

* Odesílání [typu zařízení cloud] [ lnk-d2c] zpráv ze zařízení do řešení back-endu.
* Odesílání [typu cloud zařízení] [ lnk-c2d] zprávy z řešení back-endu pro vaše zařízení.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Základní vlastnosti funkce zasílání zpráv služby IoT Hub se spolehlivost a odolnost zpráv. Tyto vlastnosti zajištěna odolnost k přerušovaným připojením na straně zařízení a načíst poraďte se špičkami na straně cloudu pro zpracování událostí. IoT Hub implementuje *alespoň jednou* záruky doručení pro zasílání zpráv typu zařízení cloud a cloud zařízení.

Úvod k možnostem služby IoT Hub najdete v článku [Přehled služby Azure IoT Hub][lnk-iot-hub-overview].

## <a name="when-to-use-iot-hub-messaging"></a>Kdy použít zasílání zpráv služby IoT Hub

Jednosměrná oznámení do aplikace pro zařízení pomocí zpráv typu zařízení cloud pro odesílání čas řady telemetrii a upozornění z aplikace pro zařízení a zprávy typu cloud zařízení.

* Odkazovat na [pokyny komunikace typu zařízení cloud] [ lnk-d2c-guidance] Pokud máte pochybnosti mezi pomocí zpráv typu zařízení cloud, ohlášených vlastností nebo odeslání souboru.
* Odkazovat na [pokyny komunikaci typu Cloud zařízení] [ lnk-c2d-guidance] Pokud máte pochybnosti mezi použitím zprávy typu cloud zařízení, požadovaných vlastností nebo přímých metod.

## <a name="next-steps"></a>Další postup

* Další informace o službě IoT Hub [zasílání zpráv typu zařízení cloud][lnk-d2c].
* Další informace o službě IoT Hub [zasílání zpráv typu cloud zařízení][lnk-c2d].

[lnk-azure-iot]: ../iot-fundamentals/index.yml
[lnk-iot-hub-overview]: about-iot-hub.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-c2d-guidance]: iot-hub-devguide-c2d-guidance.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md