---
title: Principy formátu zpráv služby Azure IoT Hub | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – popisuje formát a očekávaný obsah zpráv služby IoT Hub.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: asrastog
ms.openlocfilehash: 28537ac2389fbb1ca43ca4014515564bddeba4ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "69872486"
---
# <a name="create-and-read-iot-hub-messages"></a>Vytvoření a čtení zpráv IoT Hubu

Pro podporu bezproblémové interoperability mezi protokoly definuje služba IoT Hub společný formát zpráv pro všechny protokoly orientované na zařízení. Tento formát zprávy se používá pro [směrování mezi zařízeními](iot-hub-devguide-messages-d2c.md) a cloudem a pro zprávy [mezi cloudy.](iot-hub-devguide-messages-c2d.md) 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Služba IoT Hub implementuje zasílání zpráv typu zařízení cloud pomocí vzoru streamování zpráv. Zprávy [služby](/azure/event-hubs/) IoT Hub mezi zařízeními a cloudem se podobu událostí centra událostí *než* *zprávy* [service bus](/azure/service-bus-messaging/) udávají v tom, že prostřednictvím služby prochází velký objem událostí, které mohou číst více čtenářů.

Zpráva služby IoT Hub se skládá z:

* Předem určená sada *vlastností systému,* jak je uvedeno níže.

* Sada *vlastností aplikace*. Slovník vlastností řetězce, které aplikace může definovat a přistupovat, aniž by bylo nutné rekonstruovat text zprávy. IoT Hub nikdy modifikuje tyto vlastnosti.

* Neprůhledné binární tělo.

Názvy a hodnoty vlastností mohou obsahovat ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` pouze alfanumerické znaky ASCII a navíc při odesílání zpráv typu zařízení cloud pomocí protokolu HTTPS nebo odesílání zpráv typu cloud zařízení.

Zasílání zpráv mezi zařízeními a cloudem pomocí služby IoT Hub má následující charakteristiky:

* Zprávy mezi zařízeními a cloudem jsou trvanlivé a uchovávané ve výchozím koncovém bodě **zpráv/událostí služby** IoT hub po dobu až sedmi dnů.

* Zprávy mezi zařízeními a cloudy mohou být maximálně 256 kB a mohou být seskupeny v dávkách pro optimalizaci odešle. Dávky mohou být maximálně 256 KB.

* IoT Hub neumožňuje libovolné dělení. Zprávy mezi zařízeními a cloudjsou rozděleny na základě jejich původní **deviceId**.

* Jak je vysvětleno v [ovládacím prvku přístup k IoT Hub](iot-hub-devguide-security.md), IoT Hub umožňuje ověřování na zařízení a řízení přístupu.

* Zprávy můžete razítkovat informacemi, které přejdou do vlastností aplikace. Další informace naleznete v tématu [obohacení zpráv](iot-hub-message-enrichments-overview.md).

Další informace o kódování a dekódování zpráv odeslaných pomocí různých protokolů naleznete v [tématu sady Azure IoT SDK](iot-hub-devguide-sdks.md).

## <a name="system-properties-of-d2c-iot-hub-messages"></a>Vlastnosti systému zpráv **služby D2C** IoT Hub

| Vlastnost | Popis  |Uživatel settable?|Klíčové slovo pro </br>směrovací dotaz|
| --- | --- | --- | --- |
| id zprávy |Identifikátor uživatelem settable pro zprávu použitou pro vzory odpovědí na požadavek. Formát: Řetězec rozlišující malá a velká písmena (až 128 znaků) 7bitových alfanumerických znaků ASCII + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`.  | Ano | Messageid |
| iothub-enqueuedtime |Datum a čas, kdy služba IoT Hub přijala zprávu [zařízení ke cloudu.](iot-hub-devguide-d2c-guidance.md) | Ne | enqueuedTime |
| user-id |ID slouží k určení původu zpráv. Když jsou zprávy generovány pomocí ioT `{iot hub name}`hubu, je nastavena na . | Ano | userId |
| iothub-connection-device-id |ID nastavené službou IoT Hub na zprávy zařízení cloud. Obsahuje **deviceId** zařízení, které odeslal zprávu. | Ne | connectionDeviceId |
| iothub-connection-module-id |ID nastavené službou IoT Hub na zprávy zařízení cloud. Obsahuje **modulId** zařízení, které odeslal zprávu. | Ne | connectionModuleId |
| iothub-connection-auth-generation-id |ID nastavené službou IoT Hub na zprávy zařízení cloud. Obsahuje **connectionDeviceGenerationId** (podle [vlastností identity zařízení](iot-hub-devguide-identity-registry.md#device-identity-properties)) zařízení, které zprávu odeslané. | Ne |connectionDeviceGenerationId |
| metoda iothub-connection-auth |Metoda ověřování nastavená službou IoT Hub na zprávách mezi zařízeními a cloudy. Tato vlastnost obsahuje informace o metodě ověřování použité k ověření zařízení odesílajícího zprávu.| Ne | connectionAuthMethod |

## <a name="system-properties-of-c2d-iot-hub-messages"></a>Vlastnosti systému zpráv **služby C2D** IoT Hub

| Vlastnost | Popis  |Uživatel settable?|
| --- | --- | --- |
| id zprávy |Identifikátor uživatelem settable pro zprávu použitou pro vzory odpovědí na požadavek. Formát: Řetězec rozlišující malá a velká písmena (až 128 znaků) 7bitových alfanumerických znaků ASCII + `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`.  |Ano|
| pořadové číslo |Číslo (jedinečné pro každou frontu zařízení) přiřazené službou IoT Hub ke každé zprávě o cloudu zařízení. |Ne|
| na |Cíl určený ve [zprávách cloud-zařízení.](iot-hub-devguide-c2d-guidance.md) |Ne|
| absolutní doba expirace |Datum a čas vypršení platnosti zprávy. |Ne|   |
| correlation-id |Vlastnost řetězce ve zprávě odpovědi, která obvykle obsahuje MessageId požadavku, ve vzorcích požadavku a odpovědi. |Ano|
| user-id |ID slouží k určení původu zpráv. Když jsou zprávy generovány pomocí ioT `{iot hub name}`hubu, je nastavena na . |Ano|
| iothub-ack |Generátor zpráv zpětné vazby. Tato vlastnost se používá ve zprávách mezi cloudy a zařízení mno žadovky, které požadují, aby služba IoT Hub vygenerovala zprávy o zpětné vazbě v důsledku spotřeby zprávy zařízením. Možné hodnoty: **žádné** (výchozí): není generována žádná zpráva o zpětné vazbě, **kladná**: obdržíte zprávu o zpětné vazbě, pokud byla zpráva dokončena, **záporná**: obdržíte zprávu o zpětné vazbě, pokud vypršela platnost zprávy (nebo bylo dosaženo maximálního počtu doručení), aniž by byla dokončena zařízením, nebo **úplná**: kladná i záporná. |Ano|

## <a name="message-size"></a>Velikost zpráv

IoT Hub měří velikost zprávy způsobem, který nestanoví protokol, s ohledem pouze na skutečnou datovou část. Velikost v bajtů se vypočítá jako součet následujících hodnot:

* Velikost těla v bajtů.
* Velikost v bajtů všech hodnot vlastností systému zpráv.
* Velikost v bajtů všech názvů uživatelských vlastností a hodnot.

Názvy vlastností a hodnoty jsou omezeny na znaky ASCII, takže délka řetězců se rovná velikosti v bajtů.

## <a name="anti-spoofing-properties"></a>Vlastnosti ochrany proti falšování

Aby se zabránilo falšování zařízení ve zprávách mezi zařízeními a cloudem, služba IoT Hub označí všechny zprávy následujícími vlastnostmi:

* **iothub-connection-device-id**
* **iothub-connection-auth-generation-id**
* **metoda iothub-connection-auth**

První dva obsahují **deviceId** a **generationId** původního zařízení podle [vlastností identity zařízení](iot-hub-devguide-identity-registry.md#device-identity-properties).

Vlastnost **iothub-connection-auth-method** obsahuje serializovaný objekt JSON s následujícími vlastnostmi:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Další kroky

* Informace o omezení velikosti zpráv v centru IoT Hub najdete v [tématu kvóty služby IoT Hub a omezení](iot-hub-devguide-quotas-throttling.md).

* Informace o vytváření a čtení zpráv centra IoT Hub v různých programovacích jazycích najdete v [tématu Rychlé starty](quickstart-send-telemetry-node.md).
