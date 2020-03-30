---
title: Úvod do náhledu ioT plug and play | Dokumenty společnosti Microsoft
description: Přečtěte si o náhledu IoT Plug and Play. IoT Plug and Play je založen na otevřeném jazyce modelování, který umožňuje zařízením IoT deklarovat své schopnosti. Zařízení IoT představují tuto deklaraci, nazývanou model schopností zařízení, když se připojují ke cloudovým řešením, jako jsou Azure IoT Central nebo partnerské aplikace. Cloudové řešení pak může automaticky pochopit zařízení a začít s ním pracovat – to vše bez psaní kódu.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/23/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 0399e1659fb7cc6a650c6b3c1d0189c8802d4904
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80064317"
---
# <a name="what-is-iot-plug-and-play-preview"></a>Co je náhled ioT plug and play?

IoT Plug and Play Preview umožňuje vývojářům řešení integrovat zařízení s jejich řešeními bez psaní vloženého kódu. Jádrem IoT Plug and Play je _schéma modelu schopností zařízení,_ které popisuje možnosti zařízení. Toto schéma je dokument JSON, který je strukturován jako sada rozhraní, která obsahují definice:

- _Vlastnosti,_ které představují stav zařízení nebo jiné entity jen pro čtení a čtení a zápis. Sériové číslo zařízení může být například vlastností jen pro čtení a cílová teplota na termostatu může být vlastnost pro čtení a zápis.
- _Telemetrie,_ která je data vyzařovaná zařízením, zda jsou data pravidelným proudem údajů senzorů, příležitostnou chybou nebo informační zprávou.
- _Příkazy,_ které popisují funkci nebo operaci, kterou lze provést na zařízení. Příkaz může například restartovat bránu nebo pořit snímek pomocí vzdálené kamery.

Můžete znovu použít rozhraní napříč modely funkcí zařízení, abyste usnadnili spolupráci a urychlili vývoj.

Chcete-li, aby IoT Plug and Play bezproblémově fungovals [digitálními dvojčaty Azure](../digital-twins/about-digital-twins.md), je schéma IoT Plug and Play definováno pomocí [jazyka DTDL (Digital Twin Definition Language).](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL) IoT Plug and Play a DTDL jsou otevřené pro komunitu a Microsoft vítá spolupráci se zákazníky, partnery a odvětvím. Obě jsou založeny na otevřených standardech W3C, jako jsou JSON-LD a RDF, které umožňují snadnější přijetí napříč službami a nástroji. Navíc neexistuje žádné další náklady na používání IoT Plug and Play a DTDL. Standardní sazby pro [Azure IoT Hub](../iot-hub/about-iot-hub.md), [Azure IoT Central](../iot-central/core/overview-iot-central.md)a další služby Azure zůstávají stejné.

Řešení postavená na IoT Hubu nebo IoT Central můžou využívat IoT Plug and Play.

Tento článek popisuje:

- Typické role přidružené k projektu, který používá IoT Plug and Play.
- Jak používat zařízení IoT Plug and Play ve vaší aplikaci.
- Jak vyvinout aplikaci zařízení IoT, která podporuje IoT Plug and Play.
- Jak certifikovat zařízení IoT Plug and Play a publikovat v [katalogu zařízení Certified for IoT](https://catalog.azureiotsolutions.com/).

## <a name="user-roles"></a>Role uživatelů

IoT Plug and Play je užitečný pro dva typy vývojářů:

- _Vývojář řešení_ je zodpovědný za vývoj řešení IoT pomocí Azure IoT a dalších prostředků Azure a za identifikaci zařízení IoT pro integraci.
- _Vývojář zařízení_ vytvoří kód, který běží na zařízení připojeném k vašemu řešení.

## <a name="use-iot-plug-and-play-devices"></a>Používání zařízení IoT Plug and Play

Jako vývojář řešení můžete vyvinout cloudové řešení IoT, které používá zařízení IoT Plug and Play. Můžete použít některou z následujících služeb Azure:

- [IoT Central](../iot-central/core/overview-iot-central.md) - plně spravované softwarové řešení IoT jako služba, které usnadňuje vytváření produktů, které spojují fyzický a digitální svět.
- [IoT Hub](../iot-hub/about-iot-hub.md) – spravovaná cloudová služba, která funguje jako centrum zpráv pro zabezpečenou obousměrnou komunikaci mezi vaší aplikací IoT a vašimi zařízeními.

Zařízení IoT Plug and Play najdete v katalogu zařízení Azure Certified for IoT. Každé zařízení IoT Plug and Play v katalogu bylo ověřeno a má model schopností zařízení. Zobrazte si model schopností zařízení, abyste porozuměli funkcím zařízení, nebo ho použijte k simulaci zařízení v Azure IoT Central.

Když připojíte zařízení IoT Plug and Play, můžete zobrazit jeho model schopností zařízení, rozhraní zahrnutá v modelu a telemetrická data, vlastnosti a příkazy definované v těchto rozhraních.

## <a name="develop-an-iot-device-application"></a>Vývoj aplikace zařízení IoT

Jako vývojář zařízení můžete vyvinout hardwarový produkt IoT, který podporuje IoT Plug and Play. Tento proces zahrnuje dva klíčové kroky:

1. Definujte model a rozhraní schopností zařízení. Vytvoření sady souborů JSON, které deklarují možnosti vašeho zařízení pomocí [DTDL](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). Model schopností zařízení popisuje úplnou entitu, například fyzický produkt, a definuje sadu rozhraní implementovaných touto entitou. Rozhraní jsou sdílené smlouvy, které jednoznačně identifikují telemetrii, vlastnosti a příkazy podporované zařízením. Rozhraní lze znovu použít v různých modelech schopností zařízení.

1. Author zařízení software nebo firmware, který implementuje funkce deklarované v modelu schopnosti zařízení a rozhraní. Sada Azure IoT SDK obsahuje rozhraní API pro implementaci modelů funkcí zařízení.

Sada rozšíření [Azure IoT Tools for VS Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) poskytuje mnoho funkcí, které vám pomohou. Například jako vývojář zařízení můžete použít rozšíření ke generování projektu kostry C z modelu schopností. Však můžete použít libovolný ide k vytváření a implementaci modelů schopností zařízení.

## <a name="certify-an-iot-plug-and-play-device"></a>Certifikace zařízení IoT Plug and Play

Jako vývojář zařízení můžete odesílat hardwarové produkty IoT k certifikaci. Certifikované zařízení můžete publikovat v katalogu certifikovaných zařízení IoT. Kroky certifikačního procesu zahrnují:

- Připojte se k [programu Microsoft Partner Network](https://partner.microsoft.com).
- Na palubě k certifikovanému portálu Azure IoT.
- Odešlete model schopností zařízení IoT Plug and Play a marketingové informace a vytvořte nový záznam zařízení.
- Projděte automatickou sadou ověřovacích testů pro zařízení.
- Publikujte do katalogu zařízení Certified for IoT.

## <a name="regional-availability"></a>Regionální dostupnost

Během veřejné verze Preview je IoT Plug and Play k dispozici ve všech oblastech.

## <a name="message-quotas-in-iot-hub"></a>Kvóty zpráv v centru IoT Hub
Během veřejné verze Preview odesílají zařízení IoT Plug and Play samostatné zprávy pro jednotlivá rozhraní, což může zvýšit počet zpráv započítaných do [kvóty zpráv](../iot-hub/iot-hub-devguide-quotas-throttling.md).

## <a name="next-steps"></a>Další kroky

Teď, když máte přehled o IoT Plug and Play, je dalším krokem vyzkoušet jeden z rychlých startů:

- [Vytvoření zařízení IoT Plug and Play pomocí modelu schopností zařízení](./quickstart-create-pnp-device-windows.md)
- [Připojení zařízení ke službě IoT Hub](./quickstart-connect-pnp-device-c-windows.md)
- [Připojení k zařízení ve vašem řešení](./quickstart-connect-pnp-device-solution-node.md)
