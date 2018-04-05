---
title: Možnosti zařízení cloud Azure IoT Hub | Microsoft Docs
description: Příručka vývojáře – pokyny k použití zpráv typu zařízení cloud, hlášen vlastnostech nebo nahrávání souborů pro komunikaci typu cloud zařízení.
services: iot-hub
documentationcenter: .net
author: fsautomata
manager: timlt
editor: ''
ms.assetid: 979136db-c92d-4288-870c-f305e8777bdd
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/29/2018
ms.author: elioda
ms.openlocfilehash: a870852d937732bb38f08420a07bfe696860e08b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="device-to-cloud-communications-guidance"></a>Komunikace zařízení cloud pokyny
Při odesílání informací z aplikace zařízení k back-end řešení, IoT Hub zpřístupní tři možnosti:

* [Zprávy typu zařízení cloud] [ lnk-d2c] pro časové řady telemetrie a výstrahy.
* [Dvojče zařízení je hlášené vlastnosti] [ lnk-twins] pro vytváření sestav informace o stavu zařízení například dostupné možnosti, podmínky nebo stav dlouho běžící pracovních postupů. Například konfigurace a aktualizace softwaru.
* [Nahrávání souborů] [ lnk-fileupload] pro média soubory a velké telemetrie dávek odeslaný občas připojených zařízení a Komprimovat pro snížení šířky pásma.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Zde je podrobné porovnání různých možností komunikace zařízení cloud.

|  | Zprávy typu zařízení-cloud | Vlastnosti hlášené dvojče zařízení | Nahrávání souborů |
| ---- | ------- | ---------- | ---- |
| Scénář | Výstrahy a telemetrie časové řady. Například 256 KB senzor datových balíků odesílá každých 5 minut. | Podmínky a dostupné možnosti. Například aktuální zařízení režim připojení třeba mobilní síť nebo Wi-Fi. Synchronizace dlouho běžící pracovních postupů, jako je například konfigurace a aktualizací softwaru. | Mediálních souborů. Velké (obvykle komprimované) telemetrie dávek. |
| Ukládání a načítání | Dočasně uložit službou IoT Hub až do 7 dnů. Pouze sekvenční čtení. | Ukládá v dvojče zařízení IoT Hub. Dá načíst pomocí [IoT Hub dotazovací jazyk][lnk-query]. | Uložený v účtu Azure Storage zadaný uživatelem. |
| Velikost | Až 256 KB zprávy. | Maximální hlášené vlastnosti velikost je 8 KB. | Maximální velikost podporovaná technologií úložiště objektů Blob Azure. |
| Frekvence | Vysoká. Další informace najdete v tématu [IoT Hub omezuje][lnk-quotas]. | Střední. Další informace najdete v tématu [IoT Hub omezuje][lnk-quotas]. | Nízká. Další informace najdete v tématu [IoT Hub omezuje][lnk-quotas]. |
| Protocol (Protokol) | K dispozici ve všech protokolů. | K dispozici pomocí MQTT nebo AMQP. | K dispozici při použití libovolný protokol, ale vyžaduje protokol HTTPS na zařízení. |

Aplikace pravděpodobně nutné odeslat informace, jak jako telemetrie časové řady nebo výstrahu a zpřístupnit jej v dvojče zařízení. V tomto scénáři můžete zvolit jeden z následujících možností:

* Odešle zprávu typu zařízení cloud a sestavy změnu vlastnosti aplikace zařízení.
* Back-end řešení může ukládat informace ve značkách dvojče zařízení při přijetí zprávy.

Vzhledem k tomu, že zprávy typu zařízení cloud povolit mnohem vyšší propustnost než aktualizace twin zařízení, je někdy žádoucí, aby se zabránilo aktualizace dvojče zařízení pro každou zprávu typu zařízení cloud.


[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-fileupload]: iot-hub-devguide-file-upload.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messages-d2c.md
