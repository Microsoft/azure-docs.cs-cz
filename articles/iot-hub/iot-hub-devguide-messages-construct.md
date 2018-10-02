---
title: Vysvětlení formát zprávy služby Azure IoT Hub | Dokumentace Microsoftu
description: Příručka pro vývojáře – popisuje formátu a očekávaný obsah zpráv ve službě IoT Hub.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: 168f78ed2236d70a37a111c628e45e7bf0bbfa4b
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585403"
---
# <a name="create-and-read-iot-hub-messages"></a>Vytvoření a čtení zpráv IoT Hubu

IoT Hub pro podporu bezproblémovou spolupráci mezi protokoly, definuje společný formát zprávy pro všechny protokoly určených pro zařízení. Zprávy ve formátu se používá pro obě [směrování typu zařízení cloud](iot-hub-devguide-messages-d2c.md) a [typu cloud zařízení](iot-hub-devguide-messages-c2d.md) zprávy. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub implementuje zasílání zpráv typu zařízení cloud, pomocí vzoru streamování přenosu zpráv. Zprávy typu zařízení cloud služby IoT Hub se totiž podobá spíš [Event Hubs](/azure/event-hubs/) *události* než [služby Service Bus](/azure/service-bus-messaging/) *zprávy* v tom, že existuje velký počet předávání přes službu, kterou si můžete přečíst více čtenářům událostí.

Zpráva IoT Hub se skládá ze:

* Sada předem *vlastnosti systému* jak je uvedeno níže.

* Sada *vlastnosti aplikace*. Slovník vlastnosti řetězce, které můžete definovat aplikaci a přístup, aniž byste museli deserializaci textu zprávy. IoT Hub nikdy upravuje tyto vlastnosti.

* Neprůhledný binární tělo.

Názvy a hodnoty vlastností můžou obsahovat jenom alfanumerické znaky ASCII, plus ```{'!', '#', '$', '%, '&', "'", '*', '+', '-', '.', '^', '_', '`', '|', '~'}``` při odesílání zpráv typu zařízení cloud pomocí HTTPS protokolu nebo odesílání zpráv typu cloud zařízení.

Zařízení cloud zasílání zpráv pomocí služby IoT Hub má následující vlastnosti:

* Zprávy typu zařízení cloud jsou trvalé a zachované ve výchozím nastavení služby IoT hub **zpráv/události** koncový bod po dobu až sedmi dnů.

* Zprávy typu zařízení cloud může obsahovat nejvýše 256 KB a mohou být seskupeny do dávek pro optimalizaci odešle. Dávky může obsahovat nejvýše 256 KB.

* IoT Hub neumožňuje libovolného dělení. Zprávy typu zařízení cloud jsou rozdělené na základě jejich zdrojového **deviceId**.

* Jak je vysvětleno v [řízení přístupu ke službě IoT Hub](iot-hub-devguide-security.md), IoT Hub umožňuje pomocí řízení přístupu a ověřování podle zařízení.

Další informace o tom, jak kódování a dekódování zprávy odesílané pomocí různých protokolů najdete v tématu [sad SDK Azure IoT](iot-hub-devguide-sdks.md).

V následující tabulce jsou uvedeny sadu vlastností systému v zpráv ve službě IoT Hub.

| Vlastnost | Popis | Je uživatel nastavit? |
| --- | --- | --- |
| ID zprávy |Nastavit uživatele identifikátor pro zprávu použitou pro požadavek odpověď vzory. Formát: Malá a velká písmena řetězec (až 128 znaků) alfanumerických znaků ASCII 7 bitů + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. | Ano |
| Pořadové číslo |Číslo (jedinečný za každou frontu zařízení) ve službě IoT Hub přiřazená každé zprávy typu cloud zařízení. | Ne pro C2D zprávy. v opačném případě Ano. |
| Akce |Určení v [typu Cloud-zařízení](iot-hub-devguide-c2d-guidance.md) zprávy. | Ne pro C2D zprávy. v opačném případě Ano. |
| ExpiryTimeUtc |Datum a čas vypršení platnosti zpráv. | Ano |
| EnqueuedTime |Datum a čas [typu Cloud-zařízení](iot-hub-devguide-c2d-guidance.md) byla přijata zpráva ve službě IoT Hub. | Ne pro C2D zprávy. v opačném případě Ano. |
| CorrelationId |Vlastnost řetězce v odpovědi, který obvykle obsahuje ID zprávy požadavku ve vzorech pro požadavek odpověď. | Ano |
| UserId |ID používané k určení počátku zprávy. Při generování zpráv ve službě IoT Hub, je nastavena na `{iot hub name}`. | Ne |
| Potvrzení |Generátor zprávy zpětné vazby. Tato vlastnost se používá v zprávy typu cloud zařízení do služby IoT Hub ke generování zpráv se zpětnou vazbou v důsledku spotřeby zprávy požadavku zařízení. Možné hodnoty: **žádný** (výchozí): je vygenerována žádná zpráva zpětnou vazbu, **kladné**: Pokud zpráva byla dokončena, zobrazí se zpráva zpětnou vazbu **negativní**: přijímat zpráva zpětnou vazbu, pokud vypršela platnost zprávy (nebo bylo dosaženo maximální počet doručení) bez dokončení zařízení, nebo **úplné**: kladné a záporné. 
<!-- robinsh For more information, see [Message feedback][lnk-feedback].--> | Ano |
| ConnectionDeviceId |ID nastavit na zprávy typu zařízení cloud ve službě IoT Hub. Obsahuje **deviceId** zařízení, který zprávu odeslal. | Ne pro D2C zprávy. v opačném případě Ano. |
| ConnectionDeviceGenerationId |ID nastavit na zprávy typu zařízení cloud ve službě IoT Hub. Obsahuje **generationId** (jak je uvedeno [vlastnosti identity zařízení](iot-hub-devguide-identity-registry.md#device-identity-properties)) zařízení, který zprávu odeslal. | Ne pro D2C zprávy. v opačném případě Ano. |
| ConnectionAuthMethod |Metoda ověřování nastavena na zprávy typu zařízení cloud ve službě IoT Hub. Tato vlastnost obsahuje informace o metodu ověřování používanou k ověření zařízení posílání zprávy. <!-- ROBINSH For more information, see [Device to cloud anti-spoofing][lnk-antispoofing].--> | Ne pro D2C zprávy. v opačném případě Ano. |
| CreationTimeUtc | Datum a čas byla zpráva vytvořena na zařízení. Zařízení musí tuto hodnotu nastavit explicitně. | Ano |

## <a name="message-size"></a>Velikost zpráv

IoT Hub měří velikosti zprávy v protokolu bez ohledu na způsob, vzhledem k tomu jenom skutečné datové části. Velikost v bajtech se počítá jako součet z následujících akcí:

* Velikost textu v bajtech.
* Velikost v bajtech všechny hodnoty vlastností zprávy systému.
* Velikost v bajtech všechny uživatele názvy vlastností a hodnot.

Názvy a hodnoty vlastností jsou omezené na znaky ASCII, takže délky řetězce se rovná velikost v bajtech.

## <a name="anti-spoofing-properties"></a>Vlastnosti ochranu proti falšování identity

Chcete-li vyhnout falšování identity v zpráv typu zařízení cloud, IoT Hub zařízení razítka všechny zprávy s následujícími vlastnostmi:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

První dva obsahují **deviceId** a **generationId** původní zařízení, jak je uvedeno [vlastnosti identity zařízení](iot-hub-devguide-identity-registry.md#device-identity-properties).

**ConnectionAuthMethod** vlastnost obsahuje objekt serializován do formátu JSON, s následujícími vlastnostmi:

```json
{
  "scope": "{ hub | device }",
  "type": "{ symkey | sas | x509 }",
  "issuer": "iothub"
}
```

## <a name="next-steps"></a>Další postup

* Informace o omezení velikosti zpráv ve službě IoT Hub najdete v tématu [služby IoT Hub kvóty a omezování](iot-hub-devguide-quotas-throttling.md).

* Zjistěte, jak vytvořit a čtení zpráv v různých programovacích jazycích služby IoT Hub, najdete v článku [rychlých startů](quickstart-send-telemetry-node.md).