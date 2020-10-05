---
title: Vývoj zařízení pro Azure IoT Central | Microsoft Docs
description: Azure IoT Central je aplikační platforma IoT, která zjednodušuje vytváření řešení IoT. Tento článek poskytuje přehled vývoje zařízení pro připojení k aplikaci IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: 6fabd7d8cf5c19f05bd31c2d0b12863fd6e25382
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90017519"
---
# <a name="iot-central-device-development-overview"></a>Přehled vývoje zařízení pro IoT Central

*Tento článek se týká vývojářů zařízení.*

IoT Central aplikace umožňuje monitorovat a spravovat miliony zařízení během jejich životního cyklu. Tento přehled je určený pro vývojáře zařízení, kteří implementují kód pro spouštění na zařízeních, která se připojují k IoT Central.

Zařízení komunikují s aplikací IoT Central pomocí následujících primitivních elementů:

- _Telemetrie_ jsou data, která zařízení odesílá IoT Central. Například datový proud hodnot teploty ze senzoru připojení.
- _Vlastnosti_ jsou hodnoty stavu, které zařízení hlásí IoT Central. Například aktuální verze firmwaru zařízení. Můžete taky mít zapisovatelné vlastnosti, které IoT Central můžou aktualizovat na zařízení.
- _Příkazy_ jsou volány z IoT Central pro řízení chování zařízení. Například vaše aplikace IoT Central může zavolat příkaz k restartování zařízení.

Tvůrce řešení zodpovídá za konfiguraci řídicích panelů a zobrazení ve webovém uživatelském rozhraní IoT Central k vizualizaci telemetrie, správě vlastností a příkazů volání.

## <a name="types-of-device"></a>Typy zařízení

V následujících částech jsou popsány hlavní typy zařízení, které se můžete připojit k aplikaci IoT Central:

### <a name="standalone-device"></a>Samostatné zařízení

Samostatné zařízení se připojuje přímo k IoT Central. Samostatné zařízení obvykle posílá telemetrii od svého zapojení do vaší aplikace IoT Central. Samostatná zařízení také mohou nahlásit hodnoty vlastností, přijímat hodnoty vlastností s možností zápisu a reagovat na příkazy.

### <a name="gateway-device"></a>Zařízení brány

Zařízení brány spravuje jedno nebo více podřízených zařízení, která se připojují k vaší IoT Central aplikaci. Pomocí IoT Central můžete nakonfigurovat vztahy mezi zařízeními pro příjem dat a zařízením brány. Další informace najdete v tématu [definování nového typu zařízení brány IoT v aplikaci Azure IoT Central](./tutorial-define-gateway-device-type.md).

### <a name="edge-device"></a>Hraniční zařízení

Hraniční zařízení se připojuje přímo k IoT Central, ale funguje jako prostředník pro další zařízení, která se nazývají _listová zařízení_. Hraniční zařízení je obvykle umístěno blízko na listových zařízeních, pro které funguje jako prostředník. Mezi scénáře použití hraničních zařízení patří:

- Povolte zařízení, která se nemůžou připojit přímo k IoT Central pro připojení přes hraniční zařízení. Například na listovém zařízení se může pomocí Bluetooth připojit k hraničnímu zařízení, které se pak připojí přes Internet k IoT Central.
- Agregovaná telemetrie před odesláním do IoT Central. Tento přístup může přispět ke snížení nákladů na odesílání dat IoT Central.
- Řídicí zařízení můžete ovládat místně, abyste se vyhnuli latenci související s připojením k IoT Central přes Internet.

Hraniční zařízení může také odesílat svou vlastní telemetrii, nahlásit jeho vlastnosti a reagovat na aktualizace vlastností a příkazy s možností zápisu.

IoT Central vidí jenom hraniční zařízení, ne koncová zařízení připojená k hraničnímu zařízení.

Další informace najdete v tématu [Přidání zařízení Azure IoT Edge do aplikace Azure IoT Central](./tutorial-add-edge-as-leaf-device.md).

## <a name="connect-a-device"></a>Připojení zařízení

Azure IoT Central používá ke správě všech registrací a připojení zařízení [službu azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) .

Použití DPS umožňuje:

- IoT Central podporovat připojování zařízení a jejich škálování.
- Vygenerujete přihlašovací údaje zařízení a nakonfigurujete zařízení offline bez registrace zařízení prostřednictvím uživatelského rozhraní IoT Central.
- K registraci zařízení v IoT Central můžete použít vlastní ID zařízení. Použití vlastních ID zařízení zjednodušuje integraci se stávajícími systémy Back-Office.
- Jediný konzistentní způsob připojení zařízení k IoT Central.

Další informace najdete v tématu věnovaném [připojení k Azure IoT Central](./concepts-get-connected.md).

### <a name="security"></a>Zabezpečení

Připojení mezi zařízením a vaší IoT Central aplikací je zabezpečené pomocí [sdílených přístupových podpisů](./concepts-get-connected.md#connect-devices-at-scale-using-sas) nebo standardních [certifikátů X. 509](./concepts-get-connected.md#connect-devices-using-x509-certificates).

### <a name="communication-protocols"></a>Komunikační protokoly

Komunikační protokoly, které zařízení může použít pro připojení k IoT Central zahrnuje MQTT, AMQP a HTTPS. Interně IoT Central používá službu IoT Hub k povolení připojení zařízení. Další informace o komunikačních protokolech, které IoT Hub podporuje pro připojení zařízení, najdete v tématu [Volba komunikačního protokolu](../../iot-hub/iot-hub-devguide-protocols.md).

## <a name="implement-the-device"></a>Implementace zařízení

K implementaci chování zařízení použijte jednu ze [sad SDK pro zařízení Azure IoT](#languages-and-sdks) . Kód by měl:

- Zaregistrujte zařízení pomocí DPS a pomocí informací z DPS se připojte k internímu centru IoT ve vaší aplikaci IoT Central.
- Odešlete telemetrii ve formátu, který určuje šablona zařízení v IoT Central. IoT Central používá šablonu zařízení k určení způsobu použití telemetrie pro vizualizace a analýzu.
- Synchronizuje hodnoty vlastností mezi zařízením a IoT Central. Šablona zařízení určuje názvy vlastností a datové typy tak, aby IoT Central mohl zobrazit informace.
- Implementujte obslužné rutiny příkazů pro příkazy, které určuje šablona zařízení. Šablona zařízení určuje názvy příkazů a parametry, které má zařízení použít.

Další informace o roli šablon zařízení najdete v tématu [co jsou to šablony zařízení?](./concepts-device-templates.md).

Ukázkový kód najdete v tématu [Vytvoření a připojení klientské aplikace Node.js](./tutorial-connect-device-nodejs.md) nebo [Vytvoření a připojení klientské aplikace v Pythonu](./tutorial-connect-device-python.md).

### <a name="languages-and-sdks"></a>Jazyky a sady SDK

Další informace o podporovaných jazycích a sadách SDK najdete v tématu [pochopení a používání sad SDK pro zařízení Azure IoT Hub](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks).

## <a name="next-steps"></a>Další kroky

Pokud jste vývojářem zařízení a chcete podrobně do nějakého kódu, je navržený další krok [Vytvoření a připojení klientské aplikace k aplikaci Azure IoT Central](./tutorial-connect-device-nodejs.md).

Pokud chcete získat další informace o použití IoT Central, je doporučeným dalším postupem vyzkoušet rychlé starty, počínaje [vytvořením aplikace Azure IoT Central](./quick-deploy-iot-central.md).
