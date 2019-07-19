---
title: Omezení služby Azure Digital Service ve verzi Public Preview | Microsoft Docs
description: Seznamte se s omezeními služby Azure Digital revlákens pro veřejné verze Preview.
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: dwalther
ms.openlocfilehash: f3239fa5d21078795e7b063cb0364e905cba25c1
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846626"
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

- zprávy 100 za sekundu

## <a name="next-steps"></a>Další postup

- Pokud si chcete vyzkoušet ukázku digitálních vláken Azure, [vyhledejte dostupné místnosti](./quickstart-view-occupancy-dotnet.md)v rychlém startu.