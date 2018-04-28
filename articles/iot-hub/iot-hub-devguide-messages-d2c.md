---
title: Pochopení zasílání zpráv typu zařízení cloud Azure IoT Hub | Microsoft Docs
description: Příručka vývojáře - použití službou IoT Hub zasílání zpráv typu zařízení cloud. Obsahuje informace o odeslání telemetrie a bez telemtry dat a doručování zpráv pomocí směrování.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: dobett
ms.openlocfilehash: 54b2961713f529438f5508bb0615824ec6bec214
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="send-device-to-cloud-messages-to-iot-hub"></a>Odesílání zpráv typu zařízení cloud do služby IoT Hub

Odesílání telemetrie časové řady a výstrahy z vašich zařízení pro vaše řešení back-end, odesílání zpráv typu zařízení cloud ze zařízení do služby IoT hub. Informace o dalších zařízení cloud možnosti podporované službou IoT Hub, najdete v části [pokyny komunikace zařízení cloud][lnk-d2c-guidance].

Odesílání zpráv typu zařízení cloud prostřednictvím koncového bodu směřujících zařízení (**/devices/ {deviceId} / zprávy/události**). Pravidla směrování a směrování zpráv do jednoho z koncových bodů služby přístupem ve službě IoT hub. Pravidla směrování použijte k určení, kam je směrovat hlavičky a tělo zprávy typu zařízení cloud. Ve výchozím nastavení, zprávy jsou směrovány na předdefinovaný koncový bod služby směřujících (**zprávy nebo události**), který je kompatibilní s [Event Hubs][lnk-event-hubs]. Proto můžete použít standardní [integrace služby Event Hubs a sady SDK] [ lnk-compatible-endpoint] pro příjem zpráv typu zařízení cloud ve vašem back-end řešení.

IoT Hub implementuje zařízení cloud zasílání zpráv pomocí streamování vzorcem zasílání zpráv. Zprávy typu zařízení cloud IoT Hub se více podobají [Event Hubs] [ lnk-event-hubs] *události* než [Service Bus] [ lnk-servicebus] *zprávy* v, aby nedocházelo k velkému počtu událostí předávání prostřednictvím služby, který může číst několik čtečky.

Zařízení cloud zasílání zpráv službou IoT Hub má následující vlastnosti:

* Zprávy typu zařízení cloud jsou odolné a udržení ve výchozím nastavení služby IoT hub **zprávy nebo události** koncový bod pro až sedm dní.
* Zprávy typu zařízení cloud může být maximálně 256 KB a mohou být seskupeny do dávky k optimalizaci odešle. Dávky může být maximálně 256 KB.
* Jak je popsáno v [řízení přístupu ke službě IoT Hub] [ lnk-devguide-security] oddíl, IoT Hub umožňuje na zařízení ověřování a řízení přístupu.
* IoT Hub můžete vytvořit až 10 vlastní koncové body. Doručování zpráv do koncových bodů podle trasy nakonfigurované ve službě IoT hub. Další informace najdete v tématu [pravidla směrování](iot-hub-devguide-query-language.md#device-to-cloud-message-routes-query-expressions).
* IoT Hub umožňuje miliony současně připojených zařízení (viz [kvóty a omezení][lnk-quotas]).
* IoT Hub neumožňuje libovolný dělení. Zprávy typu zařízení cloud jsou rozdělena na oddíly na základě jejich zdrojového **deviceId**.

Další informace o rozdílech mezi IoT Hub a Event Hubs najdete v tématu [porovnání služeb Azure IoT Hub a Azure Event Hubs][lnk-comparison].

## <a name="send-non-telemetry-traffic"></a>Odesílat provoz telemetrická data

Kromě telemetrie, často zařízení odesílat zprávy a požadavky, které vyžadují samostatné spuštění a zpracování v back-end řešení. Například kritické výstrahy, které musí spustit určité akci v back-end. Můžete napsat [pravidlo směrování] [ lnk-devguide-custom] k odeslání tyto typy zprávy do koncového bodu vyhrazený pro jejich zpracování na základě buď hlavičku na zprávu nebo hodnotu v textu zprávy.

Další informace o nejlepší způsob, jak zpracovat tento druh zpráv najdete v tématu [kurz: postupy zpracování zpráv typu zařízení cloud IoT Hub] [ lnk-d2c-tutorial] kurzu.

## <a name="route-device-to-cloud-messages"></a>Směrování zprávy typu zařízení cloud

Máte dvě možnosti pro směrování zpráv typu zařízení cloud pro vaše serverové aplikace:

* Použijte předdefinovaný [koncový bod kompatibilní s centrem událostí] [ lnk-compatible-endpoint] k back-end aplikace číst zprávy typu zařízení cloud přijaté službou. Další informace o předdefinovaných koncový bod kompatibilní s centrem událostí najdete v tématu [číst zprávy typu zařízení cloud z předdefinovaných koncového bodu][lnk-devguide-builtin].
* Pravidla směrování se použijte k odesílání zpráv do vlastní koncové body ve službě IoT hub. Vlastní koncové body povolit aplikace back-end umožní číst zprávy typu zařízení cloud pomocí Event Hubs, fronty sběrnice nebo témata Service Bus. Další informace o směrování a vlastní koncové body najdete v tématu [použít vlastní koncové body a pravidla směrování pro zprávy typu zařízení cloud][lnk-devguide-custom].

## <a name="anti-spoofing-properties"></a>Ochranu proti falšování vlastnosti

Chcete-li vyhnout falšování identity ve zpráv typu zařízení cloud, IoT Hub zařízení razítka všechny zprávy s následujícími vlastnostmi:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

První dvě obsahovat **deviceId** a **generationId** původní zařízení dle [vlastnosti identity zařízení][lnk-device-properties].

**ConnectionAuthMethod** vlastnost obsahuje objekt serializován do formátu JSON, s následujícími vlastnostmi:

```json
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Další postup

Informace o sadách SDK, můžete použít k odesílání zpráv typu zařízení cloud najdete v tématu [SDK služby Azure IoT][lnk-sdks].

[Začínáme] [ lnk-get-started] kurzy ukazují, jak k odesílání zpráv typu zařízení cloud ze simulovaného a fyzické zařízení. Další podrobnosti najdete [zpráv typu zařízení cloud proces IoT Hub pomocí trasy] [ lnk-d2c-tutorial] kurzu.

[lnk-devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[lnk-devguide-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-comparison]: iot-hub-compare-event-hubs.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-get-started]: iot-hub-get-started.md

[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-compatible-endpoint]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
