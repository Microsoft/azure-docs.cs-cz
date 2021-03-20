---
title: Azure IoT Hub možnosti cloudu na zařízení | Microsoft Docs
description: Příručka pro vývojáře – pokyny k použití přímých metod, požadovaných vlastností a zpráv z cloudu na zařízení pro komunikaci z cloudu na zařízení.
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
ms.openlocfilehash: ad4f5dcd137a9be6dfc764385802792026c0297d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101093022"
---
# <a name="cloud-to-device-communications-guidance"></a>Pokyny pro komunikaci z cloudu na zařízení

IoT Hub poskytuje pro aplikace zařízení tři možnosti, jak vystavit funkce pro back-endové aplikace:

* [Přímé metody](iot-hub-devguide-direct-methods.md) komunikace, které vyžadují okamžité potvrzení výsledku. Přímé metody se často používají pro interaktivní řízení zařízení, jako je například zapnutí ventilátoru.

* [Požadované vlastnosti](iot-hub-devguide-device-twins.md) pro dlouhotrvající příkazy určené k umístění zařízení do určitého požadovaného stavu. Nastavte například interval odesílání telemetrie na 30 minut.

* [Zprávy z cloudu na zařízení](iot-hub-devguide-messages-c2d.md) pro jednosměrná oznámení do aplikace zařízení.

Informace o tom, jak [Azure IoT technologie Plug and Play](../iot-pnp/overview-iot-plug-and-play.md) používá tyto možnosti k řízení zařízení technologie Plug and Play IoT, najdete v tématu [Příručka pro vývojáře služby IoT technologie Plug and Play](../iot-pnp/concepts-developer-guide-service.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Toto je podrobné porovnání různých možností komunikace typu cloud-zařízení.

| Kategorie | Přímé metody | Požadované vlastnosti vlákna | Zprávy z cloudu na zařízení |
| ---------- | -------------- | ------------------------- | ------------------------ |
| Scenario | Příkazy, které vyžadují okamžité potvrzení, jako je například zapnutí ventilátoru. | Dlouho běžící příkazy určené k umístění zařízení do určitého požadovaného stavu. Nastavte například interval odesílání telemetrie na 30 minut. | Jednosměrná oznámení do aplikace zařízení. |
| Tok dat | Dvoucestné. Aplikace zařízení může přímo reagovat na metodu. Back-end řešení obdrží výsledek v kontextu požadavku. | Jednosměrnou metodou. Aplikace zařízení obdrží oznámení se změnou vlastnosti. | Jednosměrnou metodou. Aplikace zařízení obdrží zprávu.
| Stálost | Odpojená zařízení se nekontaktují. Back-end řešení oznamuje, že zařízení není připojené. | Hodnoty vlastností jsou zachovány v zařízení s dvojitou hodnotou. Zařízení se přečte při příštím opětovném připojení. Hodnoty vlastností lze získat pomocí [dotazovacího jazyka IoT Hub](iot-hub-devguide-query-language.md). | Zprávy mohou být uchovávány IoT Hub až 48 hodin. |
| Targets | Jedno zařízení používající **deviceId** nebo více zařízení, které používá [úlohy](iot-hub-devguide-jobs.md). | Jedno zařízení používající **deviceId** nebo více zařízení, které používá [úlohy](iot-hub-devguide-jobs.md). | Jedno zařízení podle **deviceId**. |
| Velikost | Maximální velikost datové části přímé metody je 128 KB. | Maximální velikost požadovaných vlastností je 32 KB. | Až 64 zpráv KB. |
| Frekvence | Vysoká. Další informace najdete v tématu [omezení IoT Hub](iot-hub-devguide-quotas-throttling.md). | Střední. Další informace najdete v tématu [omezení IoT Hub](iot-hub-devguide-quotas-throttling.md). | Nízká. Další informace najdete v tématu [omezení IoT Hub](iot-hub-devguide-quotas-throttling.md). |
| Protokol | K dispozici pomocí MQTT nebo AMQP. | K dispozici pomocí MQTT nebo AMQP. | K dispozici na všech protokolech. Zařízení se musí dotazovat při použití protokolu HTTPS. |

Naučte se používat přímé metody, požadované vlastnosti a zprávy z cloudu na zařízení v následujících kurzech:

* [Použití přímých metod](quickstart-control-device-node.md)
* [Použití požadovaných vlastností ke konfiguraci zařízení](tutorial-device-twins.md) 
* [Odesílání zpráv z cloudu do zařízení](iot-hub-node-node-c2d.md)
