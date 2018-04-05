---
title: Pochopení formát zprávy Azure IoT Hub | Microsoft Docs
description: Příručka vývojáře - popisuje formát a očekávaný obsah zprávy IoT Hub.
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: ''
ms.assetid: 3fc5f1a3-3711-4611-9897-d4db079b4250
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/20/2018
ms.author: dobett
ms.openlocfilehash: 5979c0d79a0abd9897fd3970ccea1d9ba07f9dfd
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="create-and-read-iot-hub-messages"></a>Vytvoření a čtení zpráv služby IoT Hub

Pro podporu bezproblémové vzájemná funkční spolupráce mezi protokoly, definuje IoT Hub běžný formát zprávy pro všechny protokoly zařízení přístupem. Používá se tento formát zprávy pro [zařízení cloud] [ lnk-d2c] a [cloud zařízení] [ lnk-c2d] zprávy. 

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

[IoT Hub zpráva] [ lnk-messaging] se skládá z:

* Sadu *vlastnosti systému*. Vlastnosti, které Centrum IoT interpretuje nebo nastaví. Tato sada je předem určený.
* Sadu *vlastnosti aplikace*. Slovník vlastnosti řetězce, které aplikace můžete definovat a přístup, aniž by museli deserializaci textu zprávy. IoT Hub nikdy upravuje tyto vlastnosti.
* Neprůhledné binární text.

Názvy a hodnoty vlastností mohou obsahovat pouze alfanumerické znaky ASCII, plus ```{'!', '#', '$', '%, '&', "'", '*', '+', '-', '.', '^', '_', '`', '|', '~'}``` při můžete:  

* Odesílání zpráv typu zařízení cloud pomocí protokolu HTTPS.
* Odesílání zpráv typu cloud zařízení.

Další informace o tom, jak kódování a dekódování zprávy odeslané pomocí různých protokolů najdete v tématu [SDK služby Azure IoT][lnk-sdks].

Následující tabulka uvádí sadu vlastností systému ve zprávách služby IoT Hub.

| Vlastnost | Popis |
| --- | --- |
| messageId |Identifikátor uživatele nastavit zprávě použité pro vzory požadavku a odpovědi. Formát: Malá a velká písmena řetězec (až 128 znaků.) z alfanumerických znaků ASCII 7bitového + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Pořadové číslo |Číslo (jedinečný pro každé zařízení fronty) přiřazené službou IoT Hub každou zprávu cloud zařízení. |
| Akce |Cíl zadaný v [Cloud-zařízení] [ lnk-c2d] zprávy. |
| ExpiryTimeUtc |Datum a čas vypršení platnosti zprávy. |
| EnqueuedTime |Datum a čas [Cloud-zařízení] [ lnk-c2d] zpráva byla přijata službou IoT Hub. |
| CorrelationId |Vlastnosti řetězce v zprávu odpovědi, která obvykle obsahuje MessageId žádosti v vzory požadavku a odpovědi. |
| ID uživatele |ID používané k určení původu zprávy. Zprávy generované IoT Hub, je nastavený na `{iot hub name}`. |
| Potvrzení |Generátor zpráva zpětnou vazbu. Tato vlastnost je v zprávy typu cloud zařízení slouží k vyžádání IoT Hub, která generují zprávy zpětné vazby v důsledku spotřeby zprávy zařízení. Možné hodnoty: **žádné** (výchozí): je vygenerována žádná zpráva zpětnou vazbu, **kladné**: zobrazí zpráva zpětné vazby, pokud zpráva byla dokončena, **záporné**: přijímat zpráva zpětné vazby, pokud platnost zprávy (nebo bylo dosaženo maximální doručení počet) bez jeho dokončení zařízení, nebo **úplné**: kladné a záporné. Další informace najdete v tématu [zprávy zpětné vazby][lnk-feedback]. |
| ConnectionDeviceId |ID nastavit službou IoT Hub na zpráv typu zařízení cloud. Obsahuje **deviceId** zařízení, která zprávu odeslala. |
| ConnectionDeviceGenerationId |ID nastavit službou IoT Hub na zpráv typu zařízení cloud. Obsahuje **generationId** (dle [vlastnosti identity zařízení][lnk-device-properties]) zařízení, která zprávu odeslala. |
| ConnectionAuthMethod |Metoda ověřování, nastavit službou IoT Hub na zpráv typu zařízení cloud. Tato vlastnost obsahuje informace o metodu ověřování k ověření zařízení odesílá zprávy. Další informace najdete v tématu [zařízení do cloudu ochranu proti falšování][lnk-antispoofing]. |
| CreationTimeUtc | Datum a čas vytvoření zprávy na zařízení. Zařízení musí explicitně nastavena tato hodnota. |

## <a name="message-size"></a>Velikost zpráv

IoT Hub měří velikost zprávy způsobem, bez ohledu na protokol, vzhledem k tomu jenom skutečné datové části. Velikost v bajtech se počítá jako součet z následujících akcí:

* Velikost textu v bajtech.
* Velikost v bajtech všechny hodnoty vlastností zpráv systému.
* Velikost v bajtech všechny názvy vlastností uživatele a hodnoty.

Názvy a hodnoty vlastností jsou omezeny na znaky ASCII, takže délka řetězce rovná velikost v bajtech.

## <a name="next-steps"></a>Další postup

Informace o omezení velikosti zpráv v centru IoT najdete v tématu [IoT Hub kvót a omezování][lnk-quotas].

Zjistěte, jak vytvořit a čtení zpráv v různých programovacích jazyků IoT Hub, najdete v článku [Začínáme] [ lnk-get-started] kurzy.

[lnk-messaging]: iot-hub-devguide-messaging.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-get-started]: iot-hub-get-started.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
[lnk-feedback]: iot-hub-devguide-messages-c2d.md#message-feedback
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-antispoofing]: iot-hub-devguide-messages-d2c.md#anti-spoofing-properties
