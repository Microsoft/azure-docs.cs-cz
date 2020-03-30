---
title: Omezení veřejných služeb preview – Azure Digital Twins | Dokumenty společnosti Microsoft
description: Přečtěte si o službě Public Preview, předplatném, instancích a omezeních sazby pro Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/17/2020
ms.openlocfilehash: 5e323d8faa19ceb0712aa6183df17740ce2a0a1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370371"
---
# <a name="public-preview-service-limits"></a>Omezení služby ve verzi Public Preview

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Během veřejné verze Preview má Azure Digital Twins následující dočasné předplatné, instanci a omezení rychlosti pro stávající zákazníky. Tato omezení existují, aby se zjednodušilo učení o nové službě a jejích mnoha funkcích, a budou zvýšena nebo odebrána obecnou dostupností (GA).

## <a name="per-subscription-limits"></a>Limity pro předplatné

Během veřejné verze preview může každé předplatné Azure vytvořit nebo spustit jenom jednu instanci Azure Digital Twins najednou. Pokud instanci odstraníte, můžete vytvořit novou.

## <a name="per-instance-limits"></a>Limity pro instancí

Každá instance Azure Digital Twins zase může mít:

- Přesně jeden vložený prostředek **IoTHub,** který se automaticky vytvoří během zřizování služeb.
- Přesně jeden **eventhub** koncový bod pro typ události **DeviceMessage**.
- Až tři koncové body **EventHub**, **ServiceBus**nebo **EventGrid** typu události **SensorChange**, **SpaceChange**, **TopologyOperation**nebo **UdfCustom**.

> [!NOTE]
> Některé parametry, které jsou obvykle definovány při vytváření výše uvedených entit Azure IoT nejsou vyžadovány během public preview.
> - Nejnovější specifikace rozhraní API naleznete v [referenční dokumentaci Swagger.](./how-to-use-swagger.md)

## <a name="azure-digital-twins-management-api-limits"></a>Limity rozhraní API pro správu digitálních dvojčat Azure

Omezení rychlosti požadavků pro rozhraní AZURE Digital Twins Management API jsou:

- 100 požadavků za sekundu rozhraní API pro správu digitálních dvojčat Azure.
- Až 1 000 objektů vrácených jedním dotazem rozhraní API azure digital twins.

> [!IMPORTANT]
> Pokud překročíte limit 1 000 objektů, zobrazí se chyba a je nutné zjednodušit dotaz.

## <a name="user-defined-functions-rate-limits"></a>Uživatelsky definované limity rychlosti funkcí

Následující omezení nastavují celkový počet všech uživatelem definovaných volání funkcí provedených v instanci Azure Digital Twins:

- 400 volání klientské knihovny za sekundu
- 100 volání **SendNotification** za sekundu

> [!NOTE]
> Následující akce mohou způsobit, že budou dočasně použita další omezení rychlosti:
> - Úpravy metadat objektu topologie
> - Aktualizace provedené v definici uživatelem definované funkce
> - Zařízení, která poprvé odesílají telemetrii

## <a name="device-telemetry-limits"></a>Limity telemetrie zařízení

Následující omezení omezují celkový počet všech zpráv, které můžou vaše zařízení poslat do instance Azure Digital Twins:

- 100 zpráv za sekundu na všech zařízeních
-    25 zpráv za sekundu na zařízení

## <a name="next-steps"></a>Další kroky

- Chcete-li vyzkoušet ukázku azure digitálnídvojčata, přejděte na [úvodní příručku a vyhledejte dostupné místnosti](./quickstart-view-occupancy-dotnet.md).
