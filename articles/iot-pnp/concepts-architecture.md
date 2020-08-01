---
title: Architektura IoT technologie Plug and Play | Microsoft Docs
description: Jako tvůrce řešení se rozumí klíčové prvky architektury IoT technologie Plug and Play.
author: ridomin
ms.author: rmpablos
ms.date: 07/06/2020
ms.topic: conceptual
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: f656de0bb2e5244e137ae21a6d7af88f3430b12c
ms.sourcegitcommit: 5f7b75e32222fe20ac68a053d141a0adbd16b347
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2020
ms.locfileid: "87475681"
---
# <a name="iot-plug-and-play-preview-architecture"></a>Architektura IoT Plug and Play Preview

IoT technologie Plug and Play Preview umožňuje tvůrcům řešení integrovat inteligentní zařízení s jejich řešeními bez jakékoli ruční konfigurace. V jádru IoT technologie Plug and Play je _model_ zařízení, který popisuje možnosti zařízení pro aplikaci s podporou technologie Plug and Play IoT. Tento model je strukturovaný jako sada rozhraní definujících:

- _Vlastnosti_ , které reprezentují stav jen pro čtení nebo zapisovatelného stavu zařízení nebo jiné entity. Například sériové číslo zařízení může být vlastnost jen pro čtení a cílová teplota na termostatovi může být vlastnost s možností zápisu.
- _Telemetrii_ , která je daty vysílaná zařízením, ať už data jsou pravidelným proudem čtení senzorů, příležitostné chyby nebo informační zprávou.
- _Příkazy_ , které popisují funkci nebo operaci, které lze provést na zařízení. Například příkaz může restartovat bránu nebo pořídit obrázek pomocí vzdálené kamery.

Každý model a rozhraní má jedinečné ID.

Následující diagram znázorňuje klíčové prvky řešení IoT technologie Plug and Play:

:::image type="content" source="media/concepts-architecture/pnp-architecture.png" alt-text="Architektura IoT technologie Plug and Play":::

## <a name="model-repository"></a>Úložiště modelů

[Úložiště modelu](./concepts-model-repository.md) je úložiště pro definice modelů a rozhraní. Modely a rozhraní definujete pomocí [jazyka DTDL (Digital Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl).

Webové uživatelské rozhraní umožňuje správu modelů a rozhraní.

Úložiště modelů používá RBAC a umožňuje omezit přístup k definicím rozhraní.

## <a name="devices"></a>Zařízení

Tvůrce zařízení implementuje kód, který se spustí na inteligentním zařízení IoT pomocí jedné ze [sad SDK pro zařízení Azure IoT](./libraries-sdks.md). Sady SDK pro zařízení pomůžou tvůrci zařízení:

- Připojte se bezpečně ke službě IoT Hub.
- Zaregistrujte zařízení ve službě IoT Hub a oznámíte ID modelu, které identifikuje kolekci rozhraní, které zařízení implementuje.
- Aktualizuje vlastnosti definované v rozhraních DTDL, které zařízení implementuje. Tyto vlastnosti jsou implementované pomocí digitálních vláken, která spravují synchronizaci ve službě IoT Hub.
- Přidejte obslužné rutiny příkazu pro příkazy definované v rozhraních DTDL, které zařízení implementuje.
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
- [Vývoj pro IoT technologie Plug and Play](./concepts-developer-guide.md)
