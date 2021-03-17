---
title: Možnosti zařízení-Cloud pro Azure IoT Hub | Microsoft Docs
description: Příručka pro vývojáře – pokyny k použití zpráv ze zařízení do cloudu, hlášených vlastností nebo nahrání souborů pro komunikaci z cloudu na zařízení.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.custom:
- amqp
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
ms.openlocfilehash: dd4f4ad7f84ebc1f5e254843e7afa4aa0f3f224f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87319129"
---
# <a name="device-to-cloud-communications-guidance"></a>Doprovodné materiály k komunikace zařízení-Cloud

Při odesílání informací z aplikace zařízení do back-endu řešení IoT Hub zpřístupňuje tři možnosti:

* [Zprávy ze zařízení do cloudu](iot-hub-devguide-messages-d2c.md) pro telemetrii a upozornění časových řad.

* [Ohlášené vlastnosti](iot-hub-devguide-device-twins.md) , které jsou v zařízení hlášeny pro vytváření sestav informací o stavu zařízení, jako jsou dostupné možnosti, podmínky nebo stav dlouhotrvajících pracovních postupů. Například konfigurace a aktualizace softwaru.

* [Nahrávání](iot-hub-devguide-file-upload.md) souborů pro mediální soubory a velké dávky telemetrie nahrané občas připojenými zařízeními nebo komprimované za účelem úspory šířky pásma.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Tady je podrobné porovnání různých možností komunikace mezi zařízeními a cloudem.

| Faktor | Zprávy typu zařízení-cloud | Vlastnosti hlášené v zařízení | Nahrání souborů |
| ---- | ------- | ---------- | ---- |
| Scénář | Časová řada telemetrie a výstrahy. Například 256 – datové balíky snímač KB jsou odesílány každých 5 minut. | Dostupné možnosti a podmínky. Například aktuální režim připojení zařízení, jako je mobilní síť nebo Wi-Fi. Synchronizace dlouhodobě spuštěných pracovních postupů, jako jsou konfigurace a aktualizace softwaru. | Mediální soubory. Velké (obvykle komprimované) dávky telemetrie. |
| Ukládání a načítání | Dočasně uloženo IoT Hub až 7 dní. Pouze sekvenční čtení. | Uloženo IoT Hub v zařízení je v nevlákna. Získatelné pomocí [dotazovacího jazyka IoT Hub](iot-hub-devguide-query-language.md). | Uloženo v uživatelsky zadaném Azure Storage účtu. |
| Velikost | Až 256 – zprávy KB. | Maximální velikost hlášených vlastností je 32 KB. | Maximální velikost souboru podporovaná službou Azure Blob Storage. |
| Frequency | Vysoká. Další informace najdete v tématu [omezení IoT Hub](iot-hub-devguide-quotas-throttling.md). | Střední. Další informace najdete v tématu [omezení IoT Hub](iot-hub-devguide-quotas-throttling.md). | Nízká. Další informace najdete v tématu [omezení IoT Hub](iot-hub-devguide-quotas-throttling.md). |
| Protokol | K dispozici na všech protokolech. | K dispozici pomocí MQTT nebo AMQP. | K dispozici při použití libovolného protokolu, ale na zařízení vyžaduje HTTPS. |

Aplikace může potřebovat odesílat informace jak jako časovou řadu telemetrie, tak pro upozornění a zpřístupňuje je v zařízení. V tomto scénáři můžete vybrat jednu z následujících možností:

* Aplikace zařízení pošle zprávu typu zařízení-Cloud a ohlásí změnu vlastnosti.
* Back-end řešení může ukládat informace ve značkách vlákna zařízení, když obdrží zprávu.

Vzhledem k tomu, že zprávy typu zařízení-Cloud umožňují mnohem vyšší propustnost než aktualizace nenáročné na zařízení, někdy je žádoucí zabránit tomu, aby se u každé zprávy typu zařízení-Cloud neaktualizovala dvojitá velikost zařízení.
