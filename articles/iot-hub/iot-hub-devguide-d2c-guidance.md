---
title: Možnosti zařízení cloudu služby Azure IoT Hub | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – pokyny, kdy použít zprávy mezi zařízeními a cloudy, ohlášené vlastnosti nebo nahrávání souborů pro komunikaci mezi cloudy.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: 02dc1b55d85b7137a5c1f57999cc3b7e9b1efe29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77591332"
---
# <a name="device-to-cloud-communications-guidance"></a>Pokyny pro komunikaci mezi zařízeními a cloudy

Při odesílání informací z aplikace zařízení do back-endu řešení, IoT Hub zveřejňuje tři možnosti:

* [Zprávy mezi zařízeními a cloudy](iot-hub-devguide-messages-d2c.md) pro telemetrii a výstrahy časových řad.

* [Ohlášené vlastnosti dvojčete zařízení](iot-hub-devguide-device-twins.md) pro hlášení informací o stavu zařízení, jako jsou dostupné možnosti, podmínky nebo stav dlouhotrvajících pracovních postupů. Například aktualizace konfigurace a softwaru.

* [Nahrávání souborů](iot-hub-devguide-file-upload.md) pro mediální soubory a velké telemetrické dávky nahrané přerušovaně připojenými zařízeními nebo komprimované pro úsporu šířky pásma.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Zde je podrobné srovnání různých možností komunikace mezi zařízeními a cloudem.

|  | Zprávy typu zařízení-cloud | Hlášené vlastnosti dvojčete zařízení | Nahrání souborů |
| ---- | ------- | ---------- | ---- |
| Scénář | Časové řady telemetrie a výstrahy. Například 256-KB datové dávky senzoru odeslané každých 5 minut. | Dostupné možnosti a podmínky. Například aktuální režim připojení zařízení, jako je mobilní nebo WiFi. Synchronizace dlouhotrvajících pracovních postupů, jako je konfigurace a aktualizace softwaru. | Mediální soubory. Velké (obvykle komprimované) dávky telemetrie. |
| Ukládání a načítání | Dočasně uloženy v centru IoT, až 7 dní. Pouze sekvenční čtení. | Uloženo pomocí ioT hubu v dvojčeti zařízení. Lze ji načíst pomocí [dotazovacího jazyka služby IoT Hub](iot-hub-devguide-query-language.md). | Uložené v účtu Azure Storage poskytovaném uživateli. |
| Velikost | Až 256 kB zpráv. | Maximální velikost vykazošovacích vlastností je 32 KB. | Maximální velikost souboru podporovaná úložištěm objektů blob Azure. |
| Frequency | Vysoká. Další informace najdete v tématu [limity ioT hub .](iot-hub-devguide-quotas-throttling.md) | Střední. Další informace najdete v tématu [limity ioT hub .](iot-hub-devguide-quotas-throttling.md) | Nízká. Další informace najdete v tématu [limity ioT hub .](iot-hub-devguide-quotas-throttling.md) |
| Protocol (Protokol) | K dispozici na všech protokolech. | K dispozici pomocí MQTT nebo AMQP. | K dispozici při použití libovolného protokolu, ale vyžaduje protokol HTTPS v zařízení. |

Aplikace může být nutné odeslat informace jako časové řady telemetrie nebo výstrahy a zpřístupnit je v dvojčeti zařízení. V tomto scénáři můžete zvolit jednu z následujících možností:

* Aplikace zařízení odešle zprávu zařízení cloud a hlásí změnu vlastnosti.
* Back-end řešení můžete uložit informace ve značkách dvojčete zařízení, když obdrží zprávu.

Vzhledem k tomu, že zprávy mezi zařízeními a cloudem umožňují mnohem vyšší propustnost než aktualizace dvojčete zařízení, je někdy žádoucí vyhnout se aktualizaci dvojčete zařízení pro každou zprávu zařízení cloud.
