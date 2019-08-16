---
title: Vysvětlení formátu zprávy Azure IoT Hub | Microsoft Docs
description: Příručka pro vývojáře – popisuje formát a očekávaný obsah IoT Hubch zpráv.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: asrastog
ms.openlocfilehash: 9a62001f168e0577ea07ad030923a4d0398e50af
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69534973"
---
# <a name="create-and-read-iot-hub-messages"></a>Vytvoření a čtení zpráv IoT Hubu

Pro zajištění bezproblémové interoperability mezi protokoly IoT Hub definuje společný formát zpráv pro všechny protokoly pro zařízení. Tento formát zprávy se používá pro směrování mezi zařízeními a cloudem i pro [](iot-hub-devguide-messages-c2d.md) zprávy ze [zařízení](iot-hub-devguide-messages-d2c.md) do cloudu. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub implementuje zprávy typu zařízení-Cloud pomocí vzoru zasílání zpráv streamování. Zprávy typu zařízení-Cloud IoT Hub jsou větší, jako [Event Hubs](/azure/event-hubs/) *události* , než [Service Bus](/azure/service-bus-messaging/) *zprávy* v tom, že existuje velký objem událostí, které je možné předávat prostřednictvím služby, kterou může číst více čtenářů.

IoT Hub zpráva se skládá z těchto:

* Předem stanovená sada *vlastností systému* , jak je uvedeno níže.

* Sada *vlastností aplikace* Slovník vlastností řetězce, které může aplikace definovat a přistupovat, aniž by bylo nutné deserializovat tělo zprávy. IoT Hub tyto vlastnosti nikdy nezmění.

* Neprůhledný binární text

Názvy vlastností a hodnoty mohou obsahovat pouze alfanumerické znaky ASCII a také ``{'!', '#', '$', '%, '&', ''', '*', '+', '-', '.', '^', '_', '`', '|', '~'}`` při posílání zpráv ze zařízení do cloudu pomocí protokolu HTTPS nebo posílání zpráv z cloudu na zařízení.

Zasílání zpráv ze zařízení do cloudu s IoT Hub má následující vlastnosti:

* Zprávy ze zařízení do cloudu jsou odolné a uchovávané ve výchozím koncovém bodě **zpráv/událostí** služby IoT Hub po dobu až sedmi dnů.

* Zprávy ze zařízení do cloudu můžou být maximálně 256 KB a můžou se seskupovat v dávkách pro optimalizaci odesílání. Dávky mohou být nejvýše 256 KB.

* IoT Hub nepovoluje libovolné dělení. Zprávy ze zařízení do cloudu jsou rozdělené podle původního **deviceId**.

* Jak je vysvětleno v tématu [řízení přístupu k IoT Hub](iot-hub-devguide-security.md), IoT Hub umožňuje ověřování na základě zařízení a řízení přístupu.

* Zprávy lze porazítkovat informacemi, které jsou součástí vlastností aplikace. Další informace najdete v tématu věnovaném [rozšířením zpráv](iot-hub-message-enrichments-overview.md).

Další informace o tom, jak zakódovat a dekódovat zprávy odeslané pomocí různých protokolů, najdete v tématu sady [SDK služby Azure IoT](iot-hub-devguide-sdks.md).

## <a name="system-properties-of-d2c-iot-hub-messages"></a>Systémové vlastnosti zpráv **D2C** IoT Hub

| Vlastnost | Popis  |Nastavit uživatele?|Klíčové slovo pro </br>dotaz směrování|
| --- | --- | --- | --- |
| ID zprávy |Uživatelsky nastavitelný identifikátor zprávy, která se používá ke vzorům požadavků a odpovědí. Formát: Řetězec s rozlišováním velkých a malých písmen (od až 128 znaků dlouhý) znaků ASCII 7 bitů `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`.  | Ano | messageId |
| iothub-enqueuedtime |Datum a čas přijetí zprávy ze [zařízení do cloudu](iot-hub-devguide-d2c-guidance.md) IoT Hub. | Ne | enqueuedTime |
| ID uživatele |ID, které slouží k určení původu zpráv. Když jsou zprávy generovány IoT Hub, je nastavena na `{iot hub name}`. | Ano | userId |
| iothub-ID zařízení-připojení |ID nastavené IoT Hub u zpráv ze zařízení do cloudu. Obsahuje **deviceId** zařízení, které zprávu odeslalo. | Ne | connectionDeviceId |
| iothub-Connection-Module-ID |ID nastavené IoT Hub u zpráv ze zařízení do cloudu. Obsahuje **moduleId** zařízení, které zprávu odeslalo. | Ne | connectionModuleId |
| iothub-connection-auth-generation-id |ID nastavené IoT Hub u zpráv ze zařízení do cloudu. Obsahuje **connectionDeviceGenerationId** (podle [vlastností identity jednotlivých zařízení](iot-hub-devguide-identity-registry.md#device-identity-properties)) zařízení, které zprávu odeslalo. | Ne |connectionDeviceGenerationId |
| iothub připojení-auth-Method |Metoda ověřování nastavená IoT Hub na zprávy ze zařízení do cloudu. Tato vlastnost obsahuje informace o metodě ověřování, která se používá k ověření zařízení odesílajícího zprávu.| Ne | connectionAuthMethod |

## <a name="system-properties-of-c2d-iot-hub-messages"></a>Systémové vlastnosti zpráv **C2D** IoT Hub

| Vlastnost | Popis  |Nastavit uživatele?|
| --- | --- | --- |
| ID zprávy |Uživatelsky nastavitelný identifikátor zprávy, která se používá ke vzorům požadavků a odpovědí. Formát: Řetězec s rozlišováním velkých a malých písmen (od až 128 znaků dlouhý) znaků ASCII 7 bitů `{'-', ':', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`.  |Ano|
| pořadové číslo |Číslo (jedinečné pro každou frontu zařízení) přiřazené IoT Hub ke každé zprávě typu cloud-zařízení. |Ne|
| na |Cíl určený ve zprávách typu [Cloud-zařízení](iot-hub-devguide-c2d-guidance.md) . |Ne|
| absolutní – doba vypršení platnosti |Datum a čas vypršení platnosti zprávy |Ne|   |
| ID korelace |Řetězcová vlastnost ve zprávě odpovědi, která obvykle obsahuje parametr MessageId žádosti ve vzorech požadavek-odpověď. |Ano|
| ID uživatele |ID, které slouží k určení původu zpráv. Když jsou zprávy generovány IoT Hub, je nastavena na `{iot hub name}`. |Ano|
| iothub – ACK |Generátor zpráv zpětné vazby. Tato vlastnost se používá ve zprávách typu cloud-zařízení k vyžádání IoT Hub k vygenerování zpráv zpětné vazby v důsledku spotřeby zprávy ze zařízení. Možné hodnoty: **none** (výchozí): není generována žádná zpráva ozpětné vazbě, kladná zpráva o zpětné vazbě v případě, že zpráva byla dokončena, **záporná**: doručení zprávy zpětné vazby, pokud vypršela platnost zprávy (nebo byl maximální počet doručení dosaženo) bez dokončení zařízení nebo **úplné**: kladné i záporné. |Ano|

## <a name="message-size"></a>Velikost zpráv

IoT Hub měří velikost zpráv v nezávislá způsobech, přičemž zvažuje pouze skutečnou datovou část. Velikost v bajtech se počítá jako součet z následujících hodnot:

* Velikost těla v bajtech
* Velikost v bajtech všech hodnot vlastností systému zprávy.
* Velikost všech názvů a hodnot vlastností uživatele v bajtech.

Názvy vlastností a hodnoty jsou omezeny na znaky ASCII, takže délka řetězce se rovná velikosti v bajtech.

## <a name="anti-spoofing-properties"></a>Vlastnosti ochrany proti falšování identity

Aby se zabránilo falšování zařízení v rámci zpráv ze zařízení do cloudu, IoT Hub razítka všech zpráv s následujícími vlastnostmi:

* **iothub-ID zařízení-připojení**
* **iothub-connection-auth-generation-id**
* **iothub připojení-auth-Method**

První dva obsahují **deviceId** a **generationId** původního zařízení, jako jsou [Vlastnosti identity jednotlivých zařízení](iot-hub-devguide-identity-registry.md#device-identity-properties).

Vlastnost **iothub-Connection-auth-Method** obsahuje serializovaný objekt JSON s následujícími vlastnostmi:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Další postup

* Informace o omezení velikosti zpráv v IoT Hub najdete v tématu [IoT Hub kvóty a omezování](iot-hub-devguide-quotas-throttling.md).

* Informace o tom, jak vytvářet a číst IoT Hub zprávy v různých programovacích jazycích, najdete v tématu [rychlé starty](quickstart-send-telemetry-node.md).
