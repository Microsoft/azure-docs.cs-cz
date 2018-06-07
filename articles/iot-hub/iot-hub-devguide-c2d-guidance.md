---
title: Možnosti cloud zařízení Azure IoT Hub | Microsoft Docs
description: Příručka vývojáře – pokyny k použití metody přímé, dvojče zařízení požadované vlastnosti nebo zprávy typu cloud zařízení pro komunikaci typu cloud zařízení.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: elioda
ms.openlocfilehash: ff81be4bbf6d297c623c5d98b5dc22a540112fcc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634433"
---
# <a name="cloud-to-device-communications-guidance"></a>Pokyny komunikace cloud zařízení
Služba IoT Hub zajišťuje tři možnosti aplikací pro zařízení vystavit funkcionalitu do back-end aplikace:

* [Přímé metody] [ lnk-methods] pro komunikaci, které vyžadují okamžitou potvrzení výsledku. Přímé metody se často používají pro interaktivní kontroly zařízení, například zapnutí ventilátoru.
* [Twin je potřeby vlastnosti] [ lnk-twins] pro dlouhodobé příkazy určené uvést do určitého zařízení požadovaného stavu. Například nastavte interval odesílání telemetrie na 30 minut.
* [Zprávy typu cloud zařízení] [ lnk-c2d] jednosměrný oznámení do aplikace zařízení.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Zde je podrobné porovnání různých možností komunikace cloud zařízení.

|  | Přímé metody | Požadované vlastnosti pro dvojici | Zprávy typu cloud zařízení |
| ---- | ------- | ---------- | ---- |
| Scénář | Příkazy, které vyžadují okamžitou potvrzení, například zapnutí ventilátoru. | Dlouho běžící příkazy se má převést zařízení do určité požadovaný stav. Například nastavte interval odesílání telemetrie na 30 minut. | Jednosměrné oznámení do aplikace zařízení. |
| Tok dat | Obousměrné. Aplikace zařízení může reagovat na metodu hned. Back-end řešení obdrží výsledek kontextově na požadavek. | Jednosměrná. Aplikace zařízení obdrží oznámení s změnu vlastnosti. | Jednosměrná. Aplikace zařízení obdrží zprávu
| Stálost | Odpojené zařízení nejsou kontaktovat. Back-end řešení je oznámeno, že zařízení není připojené. | Hodnoty vlastností v dvojče zařízení zůstanou zachovány. Zařízení se budou číst na další opětovné připojení. Hodnoty vlastností se dá načíst pomocí [IoT Hub dotazovací jazyk][lnk-query]. | Zprávy můžete uchovávat službou IoT Hub až 48 hodin. |
| Cíle | Pomocí jednoho zařízení **deviceId**, nebo více zařízení prostřednictvím [úlohy][lnk-jobs]. | Pomocí jednoho zařízení **deviceId**, nebo více zařízení prostřednictvím [úlohy][lnk-jobs]. | Jedno zařízení podle **deviceId**. |
| Velikost | Přímá metoda maximální velikost datové části je 128 KB. | Maximální požadované vlastnosti velikost je 8 KB. | Až 64 KB zprávy. |
| Frekvence | Vysoká. Další informace najdete v tématu [IoT Hub omezuje][lnk-quotas]. | Střední. Další informace najdete v tématu [IoT Hub omezuje][lnk-quotas]. | Nízká. Další informace najdete v tématu [IoT Hub omezuje][lnk-quotas]. |
| Protocol (Protokol) | K dispozici pomocí MQTT nebo AMQP. | K dispozici pomocí MQTT nebo AMQP. | K dispozici ve všech protokolů. Zařízení se musí dotazovat při použití protokolu HTTPS. |

Další informace o použití přímé metody, požadované vlastnosti a zprávy typu cloud zařízení v následujících kurzech:

* [Používat přímé metody][lnk-methods-tutorial], pro direct metody;
* [Použijte požadované vlastnosti pro konfiguraci zařízení][lnk-twin-properties]pro dvojče zařízení je potřeby vlastnosti; 
* [Odesílání zpráv typu cloud zařízení][lnk-c2d-tutorial], pro zprávy typu cloud zařízení.

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-methods-tutorial]: iot-hub-node-node-direct-methods.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-tutorial]: iot-hub-node-node-c2d.md
