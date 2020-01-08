---
title: Úvod do IoT technologie Plug and Play Preview | Microsoft Docs
description: Přečtěte si o IoT technologie Plug and Play ve verzi Preview. IoT technologie Plug and Play vychází z otevřeného modelovacího jazyka, který umožňuje zařízením IoT deklarovat své schopnosti. Zařízení IoT prezentují tuto deklaraci, která se označuje jako model schopností zařízení, když se připojují ke cloudovým řešením, jako je Azure IoT Central nebo partnerských aplikací. Cloudové řešení pak může automaticky pochopit zařízení a začít s ním spolupracovat – to vše bez psaní kódu.
author: ChrisGMsft
ms.author: chrisgre
ms.date: 12/23/2019
ms.topic: overview
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 54a18bb20ab9af3ad794ed678ea3234a712bf5cb
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531158"
---
# <a name="what-is-iot-plug-and-play-preview"></a>Co je IoT technologie Plug and Play Preview?

IoT technologie Plug and Play Preview umožňuje vývojářům řešení integrovat zařízení s jejich řešeními bez psaní vloženého kódu. V jádru IoT technologie Plug and Play je schéma _modelu schopností zařízení_ , které popisuje možnosti zařízení. Toto schéma je dokument JSON strukturovaný jako sada rozhraní, která zahrnují definice:

- _Vlastnosti_ , které reprezentují stav jen pro čtení a čtení/zápisu v zařízení nebo jiné entitě. Například sériové číslo zařízení může být vlastnost jen pro čtení a cílová teplota na termostatovi může být vlastnost pro čtení i zápis.
- _Telemetrie_ , která je daty vysílaná zařízením, ať už je to pravidelný proud čtení snímačů, příležitostná chyba nebo informační zpráva.
- _Příkazy_ , které popisují funkci nebo operaci, které lze provést na zařízení. Například příkaz může restartovat bránu nebo pořídit obrázek pomocí vzdálené kamery.

Můžete opakovaně používat rozhraní napříč modely schopností zařízení, aby se usnadnila spolupráce a urychlila vývoj.

Aby mohla aplikace IoT technologie Plug and Play bez problémů pracovat s [digitálními úkoly Azure](../digital-twins/about-digital-twins.md), je schéma IoT technologie Plug and Play definované pomocí [DTDL (Digital Definition Language)](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). IoT technologie Plug and Play a DTDL jsou otevřené komunitě a Microsoft vítá spolupráci se zákazníky, partnery a odvětvím. Obě jsou založené na otevřených standardech W3C, jako jsou JSON-LD a RDF, což umožňuje snazší přijímání mezi službami a nástroji. Kromě toho se neúčtují žádné další poplatky za použití IoT technologie Plug and Play a DTDL. Standardní sazby za [azure IoT Hub](../iot-hub/about-iot-hub.md), [Azure IoT Central](../iot-central/core/overview-iot-central.md)a další služby Azure zůstávají stejné.

Řešení založená na IoT Hub nebo IoT Central můžou těžit ze technologie Plug and Play IoT.

Tento článek popisuje:

- Typické role přidružené k projektu, který používá IoT technologie Plug and Play.
- Jak používat zařízení IoT technologie Plug and Play ve vaší aplikaci.
- Jak vyvíjet aplikace zařízení IoT, která podporuje IoT technologie Plug and Play.
- Jak certifikovat technologie Plug and Play zařízení IoT a publikovat do [katalogu zařízení Certified for IoT](https://catalog.azureiotsolutions.com/).

## <a name="user-roles"></a>Role uživatelů

IoT technologie Plug and Play je užitečné pro dva typy vývojářů:

- _Vývojář řešení_ zodpovídá za vývoj řešení IoT pomocí Azure IoT a dalších prostředků Azure a pro identifikaci zařízení IoT, která se mají integrovat.
- _Vývojář zařízení_ vytvoří kód, který běží na zařízení připojeném k vašemu řešení.

## <a name="use-iot-plug-and-play-devices"></a>Použití zařízení IoT technologie Plug and Play

Jako vývojář řešení můžete vyvíjet řešení IoT hostované v cloudu, které používá zařízení IoT technologie Plug and Play. Můžete použít kteroukoli z následujících služeb Azure:

- [IoT Central](../iot-central/core/overview-iot-central.md) – plně spravované řešení IoT typu software jako služba usnadňující vytváření produktů, které spojují fyzické a digitální světů.
- [IoT Hub](../iot-hub/about-iot-hub.md) – spravovaná cloudová služba, která funguje jako centrum zpráv pro bezpečnou obousměrnou komunikaci mezi vaší aplikací IoT a vašimi zařízeními.

Zařízení IoT technologie Plug and Play můžete najít prostřednictvím katalogu zařízení Azure Certified for IoT. Každé zařízení IoT technologie Plug and Play v katalogu bylo ověřeno a má model schopností zařízení. Podívejte se na model schopností zařízení, abyste porozuměli funkcím zařízení nebo ho používali k simulaci zařízení v Azure IoT Central.

Když připojíte zařízení IoT technologie Plug and Play, můžete si zobrazit jeho model schopností, rozhraní zahrnuté v modelu a telemetrii, vlastnosti a příkazy definované v těchto rozhraních.

## <a name="develop-an-iot-device-application"></a>Vývoj aplikace zařízení IoT

Jako vývojář zařízení můžete vyvíjet hardwarový produkt IoT, který podporuje technologie Plug and Play IoT. Tento proces zahrnuje dva klíčové kroky:

1. Definujte model a rozhraní schopností zařízení. Vytvoříte sadu souborů JSON, které deklarují schopnosti vašeho zařízení pomocí [DTDL](https://github.com/Azure/IoTPlugandPlay/tree/master/DTDL). Model schopností zařízení popisuje kompletní entitu, jako je třeba fyzický produkt, a definuje sadu rozhraní implementovaných touto entitou. Rozhraní jsou sdílené kontrakty, které jednoznačně identifikují telemetrii, vlastnosti a příkazy, které zařízení podporuje. Rozhraní se dají znovu použít napříč různými modely schopností zařízení.

1. Vytvořte software nebo firmware zařízení, který implementuje možnosti deklarované v modelu a rozhraních schopností zařízení. Sada Azure IoT SDK obsahuje rozhraní API pro implementaci modelů schopností zařízení.

Rozšíření [Azure IoT Tools for vs Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Extension Pack poskytuje mnoho funkcí, které vám pomůžou. Například jako vývojář zařízení můžete použít rozšíření k vygenerování kostry projektu jazyka C z modelu schopností. K vytváření a implementaci modelů schopností zařízení ale můžete použít jakékoli integrované vývojové prostředí.

## <a name="certify-an-iot-plug-and-play-device"></a>Certifikace zařízení technologie Plug and Play IoT

Jako vývojář zařízení můžete odeslat hardwarové produkty IoT pro certifikaci. Certifikované zařízení můžete publikovat v katalogu zařízení Certified for IoT. Postup certifikace zahrnuje tyto kroky:

- Připojte se k [Microsoft Partner Network](https://partner.microsoft.com).
- Připojte se k portálu s certifikací pro Azure IoT.
- Odešlete model možnosti zařízení IoT technologie Plug and Play a marketingové informace pro vytvoření nového záznamu zařízení.
- Předat automatizovanou sadu ověřovacích testů pro zařízení.
- Publikujte do katalogu zařízení Certified for IoT.

## <a name="regional-availability"></a>Dostupnost podle oblastí

Ve verzi Public Preview je IoT technologie Plug and Play k dispozici v oblastech Severní Evropa, Střed USA a Japonsko – východ. Ujistěte se prosím, že jste vytvořili centrum v jedné z těchto oblastí.

## <a name="message-quotas-in-iot-hub"></a>Kvóty zpráv v IoT Hub
Během veřejné verze Preview zařízení IoT technologie Plug and Play odesílají samostatné zprávy na jedno rozhraní, což může zvýšit počet zpráv započítaných do vaší [kvóty zpráv](../iot-hub/iot-hub-devguide-quotas-throttling.md).

## <a name="next-steps"></a>Další kroky

Teď, když máte přehled o IoT technologie Plug and Play, navržený další krok si vyzkoušíme jedno z rychlých startů:

- [Vytvoření zařízení IoT technologie Plug and Play pomocí modelu schopností zařízení](./quickstart-create-pnp-device-windows.md)
- [Připojení zařízení k IoT Hub](./quickstart-connect-pnp-device-c-windows.md)
- [Připojení k zařízení ve vašem řešení](./quickstart-connect-pnp-device-solution-node.md)
