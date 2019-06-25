---
title: Možnosti typu zařízení cloud Azure IoT Hub | Dokumentace Microsoftu
description: Příručka pro vývojáře – pokyny k použití zpráv typu zařízení cloud, ohlášených vlastností nebo odeslání souboru pro komunikaci typu cloud zařízení.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: fffa064b912a96b05feb901d1d2d44533c4681b7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60885512"
---
# <a name="device-to-cloud-communications-guidance"></a>Doprovodných materiálech ke komunikaci typu zařízení cloud

Při odeslání informací z aplikace pro zařízení k back-endu řešení, služby IoT Hub zveřejňuje tři možnosti:

* [Zprávy typu zařízení cloud](iot-hub-devguide-messages-d2c.md) pro čas řady telemetrii a upozornění.

* [Uživatele ohlášené vlastnosti dvojčete zařízení](iot-hub-devguide-device-twins.md) za ohlašování informací o stavu zařízení jako jsou k dispozici možnosti, podmínky nebo stavu dlouhotrvající pracovní postupy. Například konfigurační a aktualizace softwaru.

* [Nahrávání souboru](iot-hub-devguide-file-upload.md) médií souborů a velkých telemetrie dávky odeslaný přerušovaně připojeným zařízením nebo Komprimovat pro snížení šířky pásma.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Tady je podrobné porovnání různých možností komunikace typu zařízení cloud.

|  | Zprávy typu zařízení-cloud | Ohlášené vlastnosti dvojčete zařízení | Nahrání souborů |
| ---- | ------- | ---------- | ---- |
| Scénář | Výstrahy a telemetrická data časové řady. Například 256 KB senzor datových balíků odeslaných každých 5 minut. | K dispozici možnosti a stav. Například aktuální zařízení režim připojení třeba mobilní síť nebo Wi-Fi. Probíhá synchronizace dlouhotrvající pracovní postupy, jako je například konfigurace a aktualizací softwaru. | Mediální soubory. Velké (obvykle komprimované) telemetrických dat dávek. |
| Ukládání a načítání | Dočasně ukládají ve službě IoT Hub, až na 7 dní. Pouze sekvenční čtení. | Uložené ve službě IoT Hub ve dvojčeti zařízení. Retrievable pomocí [dotazovací jazyk služby IoT Hub](iot-hub-devguide-query-language.md). | Uložené v uživatelem zadaný účet služby Azure Storage. |
| Velikost | Až 256 KB zpráv. | Ohlášené vlastnosti maximální velikost je 8 KB. | Maximální velikost souboru podporovaná službou Azure Blob Storage. |
| Frekvence | Vysoká. Další informace najdete v tématu [služby IoT Hub omezuje](iot-hub-devguide-quotas-throttling.md). | Střední. Další informace najdete v tématu [služby IoT Hub omezuje](iot-hub-devguide-quotas-throttling.md). | Nízká. Další informace najdete v tématu [služby IoT Hub omezuje](iot-hub-devguide-quotas-throttling.md). |
| Protocol | K dispozici na všechny protokoly. | K dispozici používat protokol MQTT nebo AMQP. | K dispozici při použití libovolného protokolu pro, ale vyžaduje protokol HTTPS na zařízení. |

Aplikace může potřebovat odeslat informace i jako telemetrická data časové řady nebo výstrahu a zpřístupní ji ve dvojčeti zařízení. V tomto scénáři zvolte jednu z následujících možností:

* Aplikace zařízení odesílá zprávy typu zařízení cloud a sestavy změnu vlastnosti.
* Back-end řešení může ukládat informace ve dvojčeti zařízení značky při přijetí zprávy.

Protože zpráv typu zařízení cloud povolit mnohem vyšší výkon než aktualizace dvojčete zařízení, někdy je žádoucí vyhnout, aktualizace dvojčete zařízení pro všechny zprávy typu zařízení cloud.