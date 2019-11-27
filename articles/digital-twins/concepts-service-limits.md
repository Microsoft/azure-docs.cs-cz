---
title: Omezení služby Public Preview – digitální vlákna Azure | Microsoft Docs
description: Přečtěte si o omezeních služeb, předplatném, instancí a přenosech verze Public Preview pro digitální vlákna Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: f54311af65d9678b2a51b23a38bab66111a818ca
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74383050"
---
# <a name="public-preview-service-limits"></a>Omezení služby ve verzi Public Preview

V rámci verze Public Preview má digitální vlákna Azure následující dočasné předplatné, instanci a omezení přenosové rychlosti.

Tato omezení existují, pokud chcete zjednodušit učení o nové službě a jejich mnoha funkcích.

> [!NOTE]
> Tato omezení se zvýší nebo odeberou obecnou dostupností (GA).

## <a name="per-subscription-limits"></a>Omezení pro předplatné

V rámci verze Public Preview může každé předplatné Azure vytvořit nebo spustit pouze jednu instanci digitálního vlákna Azure v jednom okamžiku.

> [!TIP]
> Odstraníte-li instanci, můžete vytvořit novou.

## <a name="per-instance-limits"></a>Omezení podle instancí

Každá instance digitálního vlákna v Azure pak může mít:

- Právě jeden integrovaný prostředek **IoTHub** , který se vytvoří automaticky během zřizování služby.
- Právě jeden koncový bod **EventHub** pro typ události **DeviceMessage**.
- Až tři koncové body **EventHub**, **ServiceBus**nebo **EventGrid** typu události **SensorChange**, **SpaceChange**, **TopologyOperation**nebo **UdfCustom**.

> [!NOTE]
> Některé parametry, které jsou obvykle definovány při vytváření výše uvedených entit Azure IoT, nejsou ve verzi Public Preview požadovány.
> - Nejnovější specifikace rozhraní API najdete v [referenční dokumentaci k Swagger](./how-to-use-swagger.md) .

## <a name="azure-digital-twins-management-api-limits"></a>Omezení rozhraní API pro správu digitálních vláken Azure

Omezení přenosové rychlosti požadavků pro rozhraní API pro správu digitálních vláken Azure je:

- 100 požadavků za sekundu rozhraní API pro správu digitálních vláken Azure.
- Až 1 000 objektů vrácených jedním dotazem rozhraní API pro správu digitálních vláken Azure.

> [!IMPORTANT]
> Pokud překročíte limit 1 000 objektů, zobrazí se chybová zpráva a bude nutné dotaz zjednodušit.

## <a name="user-defined-functions-rate-limits"></a>Omezení rychlosti uživatelem definovaných funkcí

Následující omezení nastaví celkový počet všech volání uživatelsky definovaných funkcí provedených u vaší instance digitálního vlákna Azure:

- 400 volání klientské knihovny za sekundu
- 100 **SendNotification** volání za sekundu

> [!NOTE]
> Následující akce mohou způsobit dočasné použití limitů dalších sazeb:
> - Úpravy provedené v metadatech objektu topologie
> - Aktualizace provedené v definici uživatelsky definované funkce
> - Zařízení, která poprvé odesílají telemetrii

## <a name="device-telemetry-limits"></a>Omezení telemetrie zařízení

Tato omezení omezují celkový počet všech zpráv, které vaše zařízení můžou odeslat do instance digitálních vláken Azure:

- 100 zpráv za sekundu napříč všemi zařízeními
-   25 zpráv za sekundu na zařízení

## <a name="next-steps"></a>Další kroky

- Pokud si chcete vyzkoušet ukázku digitálních vláken Azure, [vyhledejte dostupné místnosti v rychlém](./quickstart-view-occupancy-dotnet.md)startu.
