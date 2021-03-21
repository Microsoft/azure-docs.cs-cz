---
title: Architektura IoT technologie Plug and Play | Microsoft Docs
description: Jako tvůrce řešení se rozumí klíčové prvky architektury IoT technologie Plug and Play.
author: ridomin
ms.author: rmpablos
ms.date: 09/15/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 2cccb1fdfe775250f80da6cc2ecdcc4ddaa3d88e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "95484079"
---
# <a name="iot-plug-and-play-architecture"></a>Architektura technologie IoT Plug and Play

IoT Plug and Play umožňuje autorům řešení integrovat chytrá zařízení s jejich řešeními bez jakékoli ruční konfigurace. V jádru IoT technologie Plug and Play je _model_ zařízení, který popisuje možnosti zařízení pro aplikaci s podporou technologie Plug and Play IoT. Tento model je strukturovaný jako sada rozhraní definujících:

- _Vlastnosti_ , které reprezentují stav jen pro čtení nebo zapisovatelného stavu zařízení nebo jiné entity. Například sériové číslo zařízení může být vlastnost jen pro čtení a cílová teplota na termostatovi může být vlastnost s možností zápisu.
- _Telemetrii_ , která je daty vysílaná zařízením, ať už data jsou pravidelným proudem čtení senzorů, příležitostné chyby nebo informační zprávou.
- _Příkazy_ , které popisují funkci nebo operaci, které lze provést na zařízení. Například příkaz může restartovat bránu nebo pořídit obrázek pomocí vzdálené kamery.

Každý model a rozhraní má jedinečné ID.

Následující diagram znázorňuje klíčové prvky řešení IoT technologie Plug and Play:

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="Architektura technologie IoT Plug and Play":::

## <a name="model-repository"></a>Úložiště modelů

[Úložiště modelu](./concepts-model-repository.md) je úložiště pro definice modelů a rozhraní. Modely a rozhraní definujete pomocí [jazyka DTDL (Digital Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl).

Webové uživatelské rozhraní umožňuje správu modelů a rozhraní.

Úložiště modelu obsahuje předdefinované řízení přístupu na základě rolí, které umožňuje omezit přístup k definicím rozhraní.

## <a name="devices"></a>Zařízení

Tvůrce zařízení implementuje kód, který se spustí na inteligentním zařízení IoT pomocí jedné ze [sad SDK pro zařízení Azure IoT](./libraries-sdks.md). Sady SDK pro zařízení pomůžou tvůrci zařízení:

- Připojte se bezpečně ke službě IoT Hub.
- Zaregistrujte zařízení ve službě IoT Hub a nahlásit ID modelu, které identifikuje kolekci rozhraní DTDL, které zařízení implementuje.
- Synchronizuje vlastnosti definované v rozhraních DTDL mezi zařízením a centrem IoT Hub.
- Přidejte obslužné rutiny příkazu pro příkazy definované v rozhraních DTDL.
- Pošlete telemetrii do centra IoT.

## <a name="iot-edge-gateway"></a>Brána IoT Edge

Brána IoT Edge funguje jako prostředník pro připojení zařízení IoT technologie Plug and Play, která se nemůžou připojit přímo ke službě IoT Hub. Další informace najdete v tématu [jak se dá zařízení IoT Edge použít jako brána](../iot-edge/iot-edge-as-gateway.md).

## <a name="iot-edge-modules"></a>Moduly IoT Edge

_Modul IoT Edge_ umožňuje nasadit a spravovat obchodní logiku na hraničních zařízeních. Azure IoT Edge moduly jsou nejmenší jednotkou výpočtu spravovanou pomocí IoT Edge a můžou obsahovat služby Azure (například Azure Stream Analytics) nebo vlastní kód specifický pro řešení.

_IoT Edge centrum_ je jedním z modulů, které tvoří modul runtime Azure IoT Edge. Funguje jako místní proxy server pro IoT Hub tím, že zveřejňuje stejné koncové body protokolu jako IoT Hub. Tato konzistence znamená, že se klienti (zařízení nebo moduly) mohou připojit k modulu IoT Edge runtime stejným způsobem jako IoT Hub.

Sady SDK pro zařízení pomůžou tvůrci modulů:

- K zabezpečenému připojení ke službě IoT Hub použijte centrum IoT Edge.
- Zaregistrujte modul ve službě IoT Hub a nahlásit ID modelu, které identifikuje kolekci rozhraní DTDL, které zařízení implementuje.
- Synchronizuje vlastnosti definované v rozhraních DTDL mezi zařízením a centrem IoT Hub.
- Přidejte obslužné rutiny příkazu pro příkazy definované v rozhraních DTDL.
- Pošlete telemetrii do centra IoT.

## <a name="iot-hub"></a>IoT Hub

[IoT Hub](../iot-hub/about-iot-hub.md) je služba hostovaná v cloudu, která funguje jako centrální Centrum zpráv pro obousměrnou komunikaci mezi řešením IoT a zařízeními, která spravuje.

Centrum IoT:

- Zpřístupňuje ID modelu implementované zařízením dostupným pro back-end řešení.
- Udržuje digitální vlákna přidružená ke každému technologie Plug and Play zařízení připojenému k rozbočovači.
- Přepošle streamy telemetrie do jiných služeb za účelem zpracování nebo uložení.
- Směruje digitální události změny do jiných služeb, aby umožňovaly monitorování zařízení.

## <a name="backend-solution"></a>Řešení back-endu

Řešení back-end monitoruje a ovládá připojená zařízení prostřednictvím interakce s digitálními podmnožinami ve službě IoT Hub. K implementaci back-endu řešení použijte jednu ze sad SDK služby. Aby bylo možné pochopit možnosti připojeného zařízení, back-end řešení:

1. Načte ID modelu zařízení zaregistrované ve službě IoT Hub.
1. Pomocí ID modelu načte definice rozhraní z libovolného úložiště modelu.
1. Pomocí analyzátoru modelu extrahuje informace z definic rozhraní.

Back-end řešení může použít informace z definic rozhraní pro:

- Načte hodnoty vlastností hlášené zařízeními.
- Aktualizace vlastností s možností zápisu na zařízení
- Volání příkazů implementovaných zařízením.
- Pochopení formátu telemetrie odesílaného zařízením

## <a name="next-steps"></a>Další kroky

Teď, když máte přehled o architektuře řešení IoT technologie Plug and Play, další kroky jsou další informace o:

- [Úložiště modelu](./concepts-model-repository.md)
- [Integrace digitálního zdvojeného modelu](./concepts-model-discovery.md)
- [Vývoj pro IoT technologie Plug and Play](./concepts-developer-guide-device.md)
