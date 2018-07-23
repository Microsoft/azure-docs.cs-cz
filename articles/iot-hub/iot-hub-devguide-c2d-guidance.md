---
title: Možnosti typu cloud zařízení Azure IoT Hub | Dokumentace Microsoftu
description: Příručka pro vývojáře – pokyny k použití přímých metod, dvojče zařízení požadované vlastnosti nebo zprávy typu cloud zařízení ke komunikaci typu cloud zařízení.
author: fsautomata
manager: ''
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 01/29/2018
ms.author: elioda
ms.openlocfilehash: 2cc9bd39371741caaa3ae025df494e225dc754b0
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39187032"
---
# <a name="cloud-to-device-communications-guidance"></a>Doprovodných materiálech ke komunikaci typu cloud zařízení
IoT Hub poskytuje tři možnosti pro aplikace pro zařízení pro funkci do back endové aplikace:

* [Přímé metody] [ lnk-methods] pro komunikaci, které vyžadují okamžitou potvrzení výsledku. Přímé metody jsou často používají za účelem interaktivního ovládání zařízení, například zapnutí ventilátoru.
* [Požadované vlastnosti dvojčete] [ lnk-twins] pro dlouho běžící příkazy určené převést zařízení do určitého požadovaného stavu. Například nastavte interval odesílání telemetrie na 30 minut.
* [Zprávy typu cloud zařízení] [ lnk-c2d] jednosměrné oznámení do aplikace pro zařízení.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Tady je podrobné porovnání různých možností komunikace typu cloud zařízení.

|  | Přímé metody | Požadované vlastnosti dvojčete | Zprávy typu cloud zařízení |
| ---- | ------- | ---------- | ---- |
| Scénář | Příkazy, které vyžadují okamžitou potvrzení, například zapnutí ventilátoru. | Dlouho běžící příkazy se mají převést zařízení do určité požadovaného stavu. Například nastavte interval odesílání telemetrie na 30 minut. | Jednosměrná oznámení do aplikace pro zařízení. |
| Tok dat | Obousměrná. Aplikace pro zařízení může reagovat na metodu okamžitě. Back-end řešení obdrží výsledek kontextově na požadavek. | Jednosměrná. Aplikace pro zařízení obdrží upozornění na změny vlastností. | Jednosměrná. Aplikace pro zařízení obdrží zprávu
| Stálost | Odpojená zařízení se kontaktovat. Back-end řešení se zobrazí oznámení, že zařízení není připojené. | Hodnoty vlastností jsou zachována ve dvojčeti zařízení. Zařízení se budou číst na další obnovení připojení. Hodnoty vlastností jsou retrievable s [dotazovací jazyk služby IoT Hub][lnk-query]. | Zprávy se můžou ukládat ve službě IoT Hub až 48 hodin. |
| Cíle | Pomocí jednoho zařízení **deviceId**, nebo více zařízení pomocí [úlohy][lnk-jobs]. | Pomocí jednoho zařízení **deviceId**, nebo více zařízení pomocí [úlohy][lnk-jobs]. | Jedno zařízení podle **deviceId**. |
| Velikost | Přímé metody maximální velikost datové části je 128 KB. | Maximální požadovaný činí vlastnosti velikosti 8 KB. | Až 64 KB zpráv. |
| Frekvence | Vysoká. Další informace najdete v tématu [služby IoT Hub omezuje][lnk-quotas]. | Střední. Další informace najdete v tématu [služby IoT Hub omezuje][lnk-quotas]. | Nízká. Další informace najdete v tématu [služby IoT Hub omezuje][lnk-quotas]. |
| Protocol (Protokol) | K dispozici používat protokol MQTT nebo AMQP. | K dispozici používat protokol MQTT nebo AMQP. | K dispozici na všechny protokoly. Zařízení se musí dotazovat při použití protokolu HTTPS. |

Další informace o použití přímých metod, požadované vlastnosti a zprávy typu cloud zařízení v následujících kurzech:

* [Použití přímých metod][lnk-methods-tutorial], pro přímé metody;
* [Použijte požadované vlastnosti ke konfiguraci zařízení][lnk-twin-properties]pro požadované vlastnosti; dvojčete zařízení 
* [Odesílat zprávy typu cloud zařízení][lnk-c2d-tutorial], pro zprávy typu cloud zařízení.

[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md
[lnk-c2d]: iot-hub-devguide-messages-c2d.md
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-methods-tutorial]: quickstart-control-device-node.md
[lnk-twin-properties]: tutorial-device-twins.md
[lnk-c2d-tutorial]: iot-hub-node-node-c2d.md
