---
title: Vysvětlení, zasílání zpráv typu zařízení cloud Azure IoT Hub | Dokumentace Microsoftu
description: Průvodce pro vývojáře – jak používat zasílání zpráv typu zařízení cloud pomocí služby IoT Hub. Obsahuje informace o odesílání telemetrických dat a jiných telemtry dat a doručování s využitím směrování zpráv.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: be87b00f27f0d0b25cd77a0634ab1c653a85e5ac
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126438"
---
# <a name="send-device-to-cloud-messages-to-iot-hub"></a>Odesílání zpráv typu zařízení cloud do služby IoT Hub

K odesílání telemetrických dat časových řad a výstrahy z vašich zařízení k back-endem řešení, odesílání zpráv typu zařízení cloud ze zařízení do služby IoT hub. Informace o dalších typu zařízení cloud možností podporovaných službou IoT Hub, najdete v článku [doprovodných materiálech ke komunikaci typu zařízení cloud][lnk-d2c-guidance].

Odesílání zpráv typu zařízení cloud prostřednictvím koncového bodu připojeného k zařízení (**/devices/ {deviceId} / zpráv/události**). Pravidla směrování a směrování zpráv do jeden z koncových bodů určených pro služby ve službě IoT hub. Pravidla směrování použijte k určení, kam chcete směrovat je hlavičky a tělo zprávy typu zařízení cloud. Ve výchozím nastavení, zprávy jsou směrovány na integrovaný koncový bod služby přístupem (**zpráv/události**), která je kompatibilní s [Event Hubs][lnk-event-hubs]. Proto můžete použít standardní [integraci služby Event Hubs a sady SDK] [ lnk-compatible-endpoint] pro příjem zpráv typu zařízení cloud v back-endem řešení.

IoT Hub implementuje zasílání zpráv typu zařízení cloud, pomocí vzoru streamování přenosu zpráv. Zprávy typu zařízení cloud služby IoT Hub se totiž podobá spíš [Event Hubs] [ lnk-event-hubs] *události* než [služby Service Bus] [ lnk-servicebus] *zprávy* v tom, že je k velkému počtu událostí předávání přes službu, kterou si můžete přečíst více čtenářům.

Zařízení cloud zasílání zpráv pomocí služby IoT Hub má následující vlastnosti:

* Zprávy typu zařízení cloud jsou trvalé a zachované ve výchozím nastavení služby IoT hub **zpráv/události** koncový bod po dobu až sedmi dnů.
* Zprávy typu zařízení cloud může obsahovat nejvýše 256 KB a mohou být seskupeny do dávek pro optimalizaci odešle. Dávky může obsahovat nejvýše 256 KB.
* Jak je vysvětleno v [řízení přístupu ke službě IoT Hub] [ lnk-devguide-security] oddíl služby IoT Hub umožňuje řízení přístupu a ověřování podle zařízení.
* IoT Hub umožňuje vytvořit až 10 vlastních koncových bodů. Zprávy se doručí do koncových bodů podle trasy nakonfigurované ve službě IoT hub. Další informace najdete v tématu [pravidla směrování](iot-hub-devguide-query-language.md#device-to-cloud-message-routes-query-expressions).
* IoT Hub umožňuje pomocí miliony současně připojených zařízení (viz [kvóty a omezování][lnk-quotas]).
* IoT Hub neumožňuje libovolného dělení. Zprávy typu zařízení cloud jsou rozdělené na základě jejich zdrojového **deviceId**.

Další informace o rozdílech mezi službou IoT Hub a Event Hubs najdete v tématu [porovnání služeb Azure IoT Hub a Azure Event Hubs][lnk-comparison].

## <a name="send-non-telemetry-traffic"></a>Posílání provozu bez telemetrie

Často kromě telemetrická data, zařízení odesílají zprávy a požadavky, které vyžadují samostatné spuštění a zpracování v back-endu řešení. Například kritické výstrahy, které musí spustit konkrétní akci v back-endu. Můžete napsat [pravidlo směrování] [ lnk-devguide-custom] odesílat tyto typy zpráv pro koncový bod vyhrazený pro jejich zpracování na základě záhlaví ve zprávě nebo hodnotu v textu zprávy.

Další informace o nejlepší způsob, jak zpracovat tento druh zpráv, najdete v článku [kurz: postupy zpracování zpráv typu zařízení cloud služby IoT Hub] [ lnk-d2c-tutorial] kurzu.

## <a name="route-device-to-cloud-messages"></a>Směrování zpráv typu zařízení cloud

Máte dvě možnosti pro směrování zpráv typu zařízení cloud do back endové aplikace:

* Použít integrovaný [koncový bod kompatibilní s centrem událostí] [ lnk-compatible-endpoint] umožňující back endové aplikace umožní číst zprávy typu zařízení cloud přijaté službou. Další informace o integrovaný koncový bod kompatibilní s centrem událostí, naleznete v tématu [čtení zpráv typu zařízení cloud z integrovaného koncového bodu][lnk-devguide-builtin].
* Použijte pravidla směrování pro odesílání zpráv do vlastní koncové body služby IoT hub. Vlastní koncové body umožňují vaší back endové aplikace umožní číst zprávy typu zařízení cloud pomocí služby Event Hubs, fronty Service Bus nebo témat sběrnice Service Bus. Další informace o směrování a vlastní koncové body, naleznete v tématu [použití vlastních koncových bodů a pravidel směrování pro zprávy typu zařízení cloud][lnk-devguide-custom].

## <a name="anti-spoofing-properties"></a>Vlastnosti ochranu proti falšování identity

Chcete-li vyhnout falšování identity v zpráv typu zařízení cloud, IoT Hub zařízení razítka všechny zprávy s následujícími vlastnostmi:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

První dva obsahují **deviceId** a **generationId** původní zařízení, jak je uvedeno [vlastnosti identity zařízení][lnk-device-properties].

**ConnectionAuthMethod** vlastnost obsahuje objekt serializován do formátu JSON, s následujícími vlastnostmi:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Další postup

Informace o sadách SDK můžete použít k odesílání zpráv typu zařízení cloud, najdete v části [sad SDK Azure IoT][lnk-sdks].

[Rychlých startů] [ lnk-get-started] ukazují, jak odesílat zprávy typu zařízení cloud ze simulovaných zařízení. Další podrobnosti najdete [zpráv typu zařízení cloud procesu IoT Hubu pomocí tras] [ lnk-d2c-tutorial] kurzu.

[lnk-devguide-builtin]: iot-hub-devguide-messages-read-builtin.md
[lnk-devguide-custom]: iot-hub-devguide-messages-read-custom.md
[lnk-comparison]: iot-hub-compare-event-hubs.md
[lnk-d2c-guidance]: iot-hub-devguide-d2c-guidance.md
[lnk-get-started]: quickstart-send-telemetry-node.md

[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-compatible-endpoint]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-d2c-tutorial]: tutorial-routing.md
