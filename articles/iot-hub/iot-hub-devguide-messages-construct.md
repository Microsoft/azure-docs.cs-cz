---
title: Vysvětlení formát zprávy služby Azure IoT Hub | Dokumentace Microsoftu
description: Příručka pro vývojáře – popisuje formátu a očekávaný obsah zpráv ve službě IoT Hub.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: a1296565384e60117d883a1f1407362482ba1a3e
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125009"
---
# <a name="create-and-read-iot-hub-messages"></a>Vytvoření a čtení zpráv IoT Hubu

IoT Hub pro podporu bezproblémovou spolupráci mezi protokoly, definuje společný formát zprávy pro všechny protokoly určených pro zařízení. Zprávy ve formátu se používá pro obě [typu zařízení cloud] [ lnk-d2c] a [typu cloud zařízení] [ lnk-c2d] zprávy. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

[Zpráv služby IoT Hub] [ lnk-messaging] se skládá ze:

* Sada *vlastnosti systému*. Vlastnosti, které služby IoT Hub interpretuje nebo nastaví. Tato sada je předem.
* Sada *vlastnosti aplikace*. Slovník vlastnosti řetězce, které můžete definovat aplikaci a přístup, aniž byste museli deserializaci textu zprávy. IoT Hub nikdy upravuje tyto vlastnosti.
* Neprůhledný binární tělo.

Názvy a hodnoty vlastností můžou obsahovat jenom alfanumerické znaky ASCII, plus ```{'!', '#', '$', '%, '&', "'", '*', '+', '-', '.', '^', '_', '`', '|', '~'}``` při můžete:  

* Odesílání zpráv typu zařízení cloud pomocí protokolu HTTPS.
* Odesílat zprávy typu cloud zařízení.

Další informace o tom, jak kódování a dekódování zprávy odesílané pomocí různých protokolů najdete v tématu [sad SDK Azure IoT][lnk-sdks].

V následující tabulce jsou uvedeny sadu vlastností systému v zpráv ve službě IoT Hub.

| Vlastnost | Popis |
| --- | --- |
| ID zprávy |Nastavit uživatele identifikátor pro zprávu použitou pro požadavek odpověď vzory. Formát: Malá a velká písmena řetězec (až 128 znaků) alfanumerických znaků ASCII 7 bitů + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Pořadové číslo |Číslo (jedinečný za každou frontu zařízení) ve službě IoT Hub přiřazená každé zprávy typu cloud zařízení. |
| Akce |Určení v [typu Cloud-zařízení] [ lnk-c2d] zprávy. |
| ExpiryTimeUtc |Datum a čas vypršení platnosti zpráv. |
| EnqueuedTime |Datum a čas [typu Cloud-zařízení] [ lnk-c2d] byla přijata zpráva ve službě IoT Hub. |
| CorrelationId |Vlastnost řetězce v odpovědi, který obvykle obsahuje ID zprávy požadavku ve vzorech pro požadavek odpověď. |
| UserId |ID používané k určení počátku zprávy. Při generování zpráv ve službě IoT Hub, je nastavena na `{iot hub name}`. |
| Potvrzení |Generátor zprávy zpětné vazby. Tato vlastnost se používá v zprávy typu cloud zařízení do služby IoT Hub ke generování zpráv se zpětnou vazbou v důsledku spotřeby zprávy požadavku zařízení. Možné hodnoty: **žádný** (výchozí): je vygenerována žádná zpráva zpětnou vazbu, **kladné**: Pokud zpráva byla dokončena, zobrazí se zpráva zpětnou vazbu **negativní**: přijímat zpráva zpětnou vazbu, pokud vypršela platnost zprávy (nebo bylo dosaženo maximální počet doručení) bez dokončení zařízení, nebo **úplné**: kladné a záporné. Další informace najdete v tématu [zprávy zpětné vazby][lnk-feedback]. |
| ConnectionDeviceId |ID nastavit na zprávy typu zařízení cloud ve službě IoT Hub. Obsahuje **deviceId** zařízení, který zprávu odeslal. |
| ConnectionDeviceGenerationId |ID nastavit na zprávy typu zařízení cloud ve službě IoT Hub. Obsahuje **generationId** (jak je uvedeno [vlastnosti identity zařízení][lnk-device-properties]) zařízení, který zprávu odeslal. |
| ConnectionAuthMethod |Metoda ověřování nastavena na zprávy typu zařízení cloud ve službě IoT Hub. Tato vlastnost obsahuje informace o metodu ověřování používanou k ověření zařízení posílání zprávy. Další informace najdete v tématu [zařízení do cloudu, ochranu proti falšování identity][lnk-antispoofing]. |
| CreationTimeUtc | Datum a čas byla zpráva vytvořena na zařízení. Zařízení musí tuto hodnotu nastavit explicitně. |

## <a name="message-size"></a>Velikost zpráv

IoT Hub měří velikosti zprávy v protokolu bez ohledu na způsob, vzhledem k tomu jenom skutečné datové části. Velikost v bajtech se počítá jako součet z následujících akcí:

* Velikost textu v bajtech.
* Velikost v bajtech všechny hodnoty vlastností zprávy systému.
* Velikost v bajtech všechny uživatele názvy vlastností a hodnot.

Názvy a hodnoty vlastností jsou omezené na znaky ASCII, takže délky řetězce se rovná velikost v bajtech.

## <a name="next-steps"></a>Další postup

Informace o omezení velikosti zpráv ve službě IoT Hub najdete v tématu [služby IoT Hub kvóty a omezování][lnk-quotas].

Zjistěte, jak vytvořit a čtení zpráv v různých programovacích jazycích služby IoT Hub, najdete v článku [rychlých startů][lnk-get-started].

[lnk-messaging]: iot-hub-devguide-messaging.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-get-started]: quickstart-send-telemetry-node.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-feedback]: iot-hub-devguide-messages-c2d.md#message-feedback
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-antispoofing]: iot-hub-devguide-messages-d2c.md#anti-spoofing-properties
