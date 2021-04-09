---
title: Vývoj zařízení pro Azure IoT Central | Microsoft Docs
description: Azure IoT Central je aplikační platforma IoT, která zjednodušuje vytváření řešení IoT. Tento článek poskytuje přehled vývoje zařízení pro připojení k aplikaci IoT Central. Zařízení využívají telemetrii k posílání streamovaná data a vlastností k hlášení stavu zařízení. IoT Central může nastavit stav zařízení pomocí zapisovatelných vlastností a příkazů volání na zařízení.
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: 4423eea6c35ad44d55ad17f5703a95c6273422b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104585855"
---
# <a name="iot-central-device-development-guide"></a>Průvodce vývojem zařízení IoT Central

*Tento článek se týká vývojářů zařízení.*

IoT Central aplikace umožňuje monitorovat a spravovat miliony zařízení během jejich životního cyklu. Tato příručka je určená pro vývojáře zařízení, kteří implementují kód pro spouštění na zařízeních, která se připojují k IoT Central.

Zařízení komunikují s aplikací IoT Central pomocí následujících primitivních elementů:

- _Telemetrie_ jsou data, která zařízení odesílá IoT Central. Například datový proud hodnot teploty ze senzoru připojení.
- _Vlastnosti_ jsou hodnoty stavu, které zařízení hlásí IoT Central. Například aktuální verze firmwaru zařízení. Můžete taky mít zapisovatelné vlastnosti, které IoT Central můžou aktualizovat na zařízení, jako je třeba cílová teplota.
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

Další informace najdete v tématu věnovaném [připojení k Azure IoT Central](./concepts-get-connected.md) a [osvědčeným postupům](concepts-best-practices.md).

### <a name="security"></a>Zabezpečení

Připojení mezi zařízením a vaší IoT Central aplikací je zabezpečené pomocí [sdílených přístupových podpisů](./concepts-get-connected.md#sas-group-enrollment) nebo standardních [certifikátů X. 509](./concepts-get-connected.md#x509-group-enrollment).

### <a name="communication-protocols"></a>Komunikační protokoly

Komunikační protokoly, které zařízení může použít pro připojení k IoT Central zahrnuje MQTT, AMQP a HTTPS. Interně IoT Central používá službu IoT Hub k povolení připojení zařízení. Další informace o komunikačních protokolech, které IoT Hub podporuje pro připojení zařízení, najdete v tématu [Volba komunikačního protokolu](../../iot-hub/iot-hub-devguide-protocols.md).

## <a name="implement-the-device"></a>Implementace zařízení

Šablona zařízení IoT Central zahrnuje _model_ , který určuje chování, které má zařízení daného typu implementovat. Chování zahrnují telemetrii, vlastnosti a příkazy.

> [!TIP]
> Model můžete exportovat z IoT Central jako soubor JSON typu [Digital DTDLs Definition Language () v2](https://github.com/Azure/opendigitaltwins-dtdl) .

Každý model má jedinečný _identifikátor zdvojeného modelu zařízení_ (DTMI), například `dtmi:com:example:Thermostat;1` . Když se zařízení připojí k IoT Central, pošle DTMI modelu, který implementuje. IoT Central pak může k zařízení přidružit správnou šablonu zařízení.

[IoT technologie Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) definuje sadu konvencí, které by mělo zařízení dodržovat, když implementuje DTDL model.

Sady [SDK pro zařízení Azure IoT](#languages-and-sdks) zahrnují podporu pro konvence technologie Plug and Play IoT.

### <a name="device-model"></a>Model zařízení

Model zařízení je definován pomocí [DTDL](https://github.com/Azure/opendigitaltwins-dtdl). Tento jazyk vám umožní definovat:

- Telemetrii, kterou zařízení odesílá Definice zahrnuje název a datový typ telemetrie. Zařízení například odesílá telemetrii teploty jako dvojitou hodnotu.
- Vlastnosti, na které se zařízení hlásí, IoT Central. Definice vlastnosti zahrnuje název a datový typ. Například zařízení hlásí stav ventilu jako logickou hodnotu.
- Vlastnosti, které zařízení může přijímat z IoT Central. Volitelně můžete označit vlastnost jako zapisovatelnou. Například IoT Central odesílá cílovou teplotu jako dvojitou hodnotu pro zařízení.
- Příkazy, na které zařízení reaguje Definice obsahuje název příkazu a názvy a datové typy všech parametrů. Zařízení například odpoví na příkaz k restartování, který určuje, kolik sekund se má čekat před restartováním.

Model DTDL může být modelem _No-Component_ nebo model _více komponent_ :

- Model bez součásti: jednoduchý model nepoužívá vložené nebo kaskádované komponenty. Všechny telemetrie, vlastnosti a příkazy jsou definovány jednou _výchozí komponentou_. Příklad najdete v modelu [termostatu](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) .
- Model více komponent. Složitější model, který obsahuje dvě nebo více komponent. Tyto komponenty zahrnují jednu výchozí komponentu a jednu nebo více dalších vnořených komponent. Příklad najdete v článku model [řadiče teploty](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) .

Další informace najdete v tématu [Příručka pro modelování IoT technologie Plug and Play](../../iot-pnp/concepts-modeling-guide.md) .

### <a name="conventions"></a>Konvence

Zařízení by se mělo při vyměňujení dat pomocí IoT Central postupovat podle konvencí technologie Plug and Play IoT. Mezi tyto konvence patří:

- Při připojení k IoT Central odeslat DTMI.
- Posílání správně formátovaných datových částí JSON a metadat IoT Central.
- Správně reagují na vlastnosti a příkazy s možností zápisu z IoT Central.
- Použijte zásady vytváření názvů pro příkazy komponent.

> [!NOTE]
> V současné době IoT Central plně nepodporuje **pole** DTDL a **geoprostorové** datové typy.

Další informace o formátu zpráv JSON, které zařízení vyměňuje pomocí IoT Central, najdete v tématu [telemetrie, vlastnosti a datové části příkazů](concepts-telemetry-properties-commands.md).

Další informace o konvencích technologie Plug and Play IoT najdete v tématu věnovaném [konvencím technologie Plug and Play IoT](../../iot-pnp/concepts-convention.md).

### <a name="device-sdks"></a>Sady SDK pro zařízení

K implementaci chování zařízení použijte jednu ze [sad SDK pro zařízení Azure IoT](#languages-and-sdks) . Kód by měl:

- Zaregistrujte zařízení pomocí DPS a pomocí informací z DPS se připojte k internímu centru IoT ve vaší aplikaci IoT Central.
- Oznamuje DTMI modelu, který zařízení implementuje.
- Odeslat telemetrii ve formátu, který určuje model zařízení. IoT Central používá model v šabloně zařízení k určení způsobu použití telemetrie pro vizualizace a analýzu.
- Synchronizuje hodnoty vlastností mezi zařízením a IoT Central. Model určuje názvy vlastností a typy dat tak, aby IoT Central mohl zobrazit informace.
- Implementujte obslužné rutiny příkazů pro příkazy zadané v modelu. Model určuje názvy příkazů a parametry, které má zařízení použít.

Další informace o roli šablon zařízení najdete v tématu [co jsou to šablony zařízení?](./concepts-device-templates.md).

Ukázkový kód najdete v tématu [Vytvoření a připojení klientské aplikace](./tutorial-connect-device.md).

### <a name="languages-and-sdks"></a>Jazyky a sady SDK

Další informace o podporovaných jazycích a sadách SDK najdete v tématu [pochopení a používání sad SDK pro zařízení Azure IoT Hub](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks).

## <a name="next-steps"></a>Další kroky

Pokud jste vývojářem zařízení a chcete podrobně do nějakého kódu, je navržený další krok [Vytvoření a připojení klientské aplikace k aplikaci Azure IoT Central](./tutorial-connect-device.md).

Pokud chcete získat další informace o použití IoT Central, je doporučeným dalším postupem vyzkoušet rychlé starty, počínaje [vytvořením aplikace Azure IoT Central](./quick-deploy-iot-central.md).
