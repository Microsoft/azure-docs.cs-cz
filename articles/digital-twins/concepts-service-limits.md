---
title: Omezení služby Public Preview – digitální vlákna Azure | Microsoft Docs
description: Přečtěte si o omezeních služeb, předplatném, instancí a přenosech verze Public Preview pro digitální vlákna Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 5e323d8faa19ceb0712aa6183df17740ce2a0a1d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79370371"
---
# <a name="public-preview-service-limits"></a>Omezení služby ve verzi Public Preview

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

V rámci verze Public Preview má stávající zákazníci následující dočasné předplatné, instanci a omezení přenosové rychlosti. Tato omezení existují, pokud chcete zjednodušit učení o nové službě a jejich mnoha funkcích a budou se zvyšovat a odebírat obecnou dostupností (GA).

## <a name="per-subscription-limits"></a>Omezení pro předplatné

V rámci verze Public Preview může každé předplatné Azure vytvořit nebo spustit pouze jednu instanci digitálního vlákna Azure v jednom okamžiku. Odstraníte-li instanci, můžete vytvořit novou.

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
-    25 zpráv za sekundu na zařízení

## <a name="next-steps"></a>Další kroky

- Pokud si chcete vyzkoušet ukázku digitálních vláken Azure, [vyhledejte dostupné místnosti v rychlém](./quickstart-view-occupancy-dotnet.md)startu.
