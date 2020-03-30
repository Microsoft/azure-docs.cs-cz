---
title: Možnosti služby Azure IoT Hub pro cloud zařízení | Dokumenty společnosti Microsoft
description: Průvodce pro vývojáře – pokyny, kdy použít přímé metody, požadované vlastnosti dvojčete zařízení nebo zprávy mezi cloudy a zařízení pro komunikaci mezi cloudy a zařízeními.
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.openlocfilehash: a2ca9a167d50619ed2963b13515c0a772d712570
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77591264"
---
# <a name="cloud-to-device-communications-guidance"></a>Pokyny pro komunikaci mezi cloudy

Služba IoT Hub nabízí tři možnosti pro aplikace zařízení, které zpřístupní funkce back-endové aplikaci:

* [Přímé metody](iot-hub-devguide-direct-methods.md) komunikace, které vyžadují okamžité potvrzení výsledku. Přímé metody se často používají pro interaktivní ovládání zařízení, jako je zapnutí ventilátoru.

* [Twin požadované vlastnosti](iot-hub-devguide-device-twins.md) pro dlouhotrvající příkazy určené k uvedením zařízení do určitého požadovaného stavu. Například nastavte interval odesílání telemetrie na 30 minut.

* [Zprávy z cloudu na zařízení](iot-hub-devguide-messages-c2d.md) pro jednosměrná oznámení do aplikace zařízení.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Zde je podrobné srovnání různých možností komunikace mezi cloudy a zařízeními.

|  | Přímé metody | Požadované vlastnosti dvojčete | Zprávy z cloudu na zařízení |
| ---- | ------- | ---------- | ---- |
| Scénář | Příkazy, které vyžadují okamžité potvrzení, například zapnutí ventilátoru. | Dlouhotrvající příkazy určené k přepychu zařízení do určitého požadovaného stavu. Například nastavte interval odesílání telemetrie na 30 minut. | Jednosměrná oznámení do aplikace zařízení. |
| Tok dat | Obousměrný. Aplikace zařízení může na metodu reagovat ihned. Back-end řešení obdrží výsledek kontextově k požadavku. | Jednosměrné. Aplikace zařízení obdrží oznámení se změnou vlastnosti. | Jednosměrné. Aplikace zařízení obdrží zprávu
| Stálost | Odpojená zařízení nejsou kontaktována. Back-end řešení je upozorněn, že zařízení není připojeno. | Hodnoty vlastností jsou zachovány v dvojčeti zařízení. Zařízení si jej přečte při příštím opětovném připojení. Hodnoty vlastností lze načíst pomocí [dotazovacího jazyka IoT Hub](iot-hub-devguide-query-language.md). | Zprávy mohou být uchovány v centru IoT Po dobu až 48 hodin. |
| Cíle | Jedno zařízení pomocí **deviceId**nebo více zařízení pomocí [úloh](iot-hub-devguide-jobs.md). | Jedno zařízení pomocí **deviceId**nebo více zařízení pomocí [úloh](iot-hub-devguide-jobs.md). | Jedno zařízení podle **deviceId**. |
| Velikost | Maximální velikost datové části přímé metody je 128 KB. | Maximální požadovaná velikost vlastností je 32 KB. | Až 64 kb zpráv. |
| Frequency | Vysoká. Další informace najdete v tématu [limity ioT hub .](iot-hub-devguide-quotas-throttling.md) | Střední. Další informace najdete v tématu [limity ioT hub .](iot-hub-devguide-quotas-throttling.md) | Nízká. Další informace najdete v tématu [limity ioT hub .](iot-hub-devguide-quotas-throttling.md) |
| Protocol (Protokol) | K dispozici pomocí MQTT nebo AMQP. | K dispozici pomocí MQTT nebo AMQP. | K dispozici na všech protokolech. Zařízení musí při použití protokolu HTTPS dotazování. |

V následujících kurzech se dozvíte, jak používat přímé metody, požadované vlastnosti a zprávy mezi cloudy:

* [Použití přímých metod](quickstart-control-device-node.md)
* [Konfigurace zařízení pomocí požadovaných vlastností](tutorial-device-twins.md) 
* [Odesílání zpráv z cloudu do zařízení](iot-hub-node-node-c2d.md)
