---
title: Vývoj bez sady Azure IoT SDK | Microsoft Docs
description: Příručka pro vývojáře – informace o a odkazy na témata, která můžete použít k vytváření aplikací pro zařízení a back-endové aplikace bez použití sady Azure IoT SDK.
author: robinsh
manager: philmea
ms.author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/12/2020
ms.custom:
- mqtt
- amqp
- 'Role: IoT Device'
- 'Role: Cloud Development'
ms.openlocfilehash: ae8b01522a248b8b1dbdd255a9fcd55f16cf2369
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96461708"
---
# <a name="develop-without-using-an-azure-iot-hub-sdk"></a>Vývoj bez použití sady Azure IoT Hub SDK

V tomto tématu najdete užitečné informace a odkazy pro vývojáře, kteří chtějí vyvíjet zařízení a back-endové aplikace bez použití sad SDK služby Azure IoT.

Společnost Microsoft důrazně doporučuje použití sady Azure IoT SDK. Sady SDK pro zařízení a služby Azure IoT jsou publikované na mnoha oblíbených platformách. Sady SDK poskytují pohodlnou vrstvu, která zpracovává většinu složitosti základního komunikačního protokolu, včetně připojení zařízení a opětovného připojení, a zásady opakování. Sady SDK se pravidelně aktualizují, aby poskytovaly nejnovější funkce, které jsou vystavené IoT Hub a také aktualizace zabezpečení. Použití sad SDK vám může pomoci zkrátit dobu vývoje a čas, který je věnován na údržbu kódu. Další informace o sadách Azure IoT SDK najdete v tématu sady [SDK pro zařízení a služby Azure IoT](iot-hub-devguide-sdks.md). Další informace o výhodách používání sady Azure IoT SDK najdete v tématu [výhody používání sad SDK Azure IoT a nástrah, abyste se vyhnuli tomu, že nebudete](https://azure.microsoft.com/en-us/blog/benefits-of-using-the-azure-iot-sdks-in-your-azure-iot-solution/) publikovat příspěvek na blogu.

I když IoT Hub podporuje AMQP, AMQP přes WebSockets, HTTPS, MQTT a MQTT přes WebSockets pro komunikaci se zařízeními, doporučujeme používat MQTT, pokud ho vaše zařízení podporuje.

## <a name="development-prerequisites"></a>Požadavky na vývoj

Než začnete s vývojem, měli byste mít důkladné znalosti o IoT Hub a funkcích, které má vaše zařízení nebo back-end aplikace implementovat. Tady je velmi zkrácený seznam témat, se kterými byste měli být obeznámeni:

* Ujistěte se, že rozumíte koncovým bodům vystaveným IoT Hub a protokoly podporovanými na jednotlivých koncových bodech. Další informace najdete v tématu [IoT Hub koncových bodů](iot-hub-devguide-endpoints.md).

* Pokud je pro aplikace zařízení zapojená volba protokolu, důrazně doporučujeme, abyste používali MQTT. Než začnete s volbou protokolu, ujistěte se, že rozumíte omezením, která jsou mezi nimi uložená. Další informace najdete v tématu [Volba komunikačního protokolu](iot-hub-devguide-protocols.md).

* Pokud chcete pochopit ověřování pomocí IoT Hub, přečtěte si téma [řízení přístupu k IoT Hub](iot-hub-devguide-security.md).

[!INCLUDE [iot-hub-include-x509-ca-signed-support-note](../../includes/iot-hub-include-x509-ca-signed-support-note.md)]

## <a name="help-on-different-protocols"></a>Pomáhat s různými protokoly

Nápovědu k používání následujících protokolů bez sady Azure IoT SDK:

* Zařízení nebo back-endové aplikace v **AMQP** najdete v tématu [Podpora AMQP](iot-hub-amqp-support.md).

* Aplikace pro zařízení v **MQTT** najdete v tématu [Podpora MQTT](iot-hub-mqtt-support.md). Většina tohoto tématu pracuje přímo s použitím protokolu MQTT. Obsahuje také informace o použití [úložiště ukázek IoT MQTT](https://github.com/Azure-Samples/IoTMQTTSample). Toto úložiště obsahuje ukázky jazyka C, které používají knihovnu Mosquitto zatmění k posílání zpráv do IoT Hub.

* Zařízení nebo back-endové aplikace na **https** najdete v [rozhraních REST api pro Azure IoT Hub](/rest/api/iothub/). Mějte na paměti, jak je uvedeno v článku [požadavky pro vývoj](#development-prerequisites), takže nemůžete použít ověřování certifikační autority (CA) X. 509 s protokolem HTTPS.

U zařízení se důrazně doporučuje používat MQTT, pokud ho vaše zařízení podporuje.

## <a name="next-steps"></a>Další kroky

* [Podpora MQTT](iot-hub-mqtt-support.md)