---
title: Omezení služby Azure digitální dvojče veřejné ve verzi preview | Dokumentace Microsoftu
description: Principy Azure digitální dvojče veřejné limity pro služby ve verzi preview.
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: dwalthermsft
ms.openlocfilehash: aa5f6053bf1c98d2b84c02617da30f5d856ed3fc
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324049"
---
# <a name="public-preview-service-limits"></a>Omezení služby ve verzi Public preview

Ve verzi Public Preview bude mít digitální dvojče Azure dočasné předplatného, instance a omezení přenosové rychlosti, které jsou popsané níže.

Existují tato omezení, která pomůže zjednodušit získání informací o nové službě a její mnoho funkcí.

> [!NOTE]
> Tato omezení se zvýšila nebo odstraněny pomocí obecné dostupnosti (GA).

## <a name="per-subscription-limits"></a>Omezení na předplatné

Každé předplatné Azure ve verzi Public Preview, můžete vytvořit nebo spuštěné přesně jednu instanci Azure digitální dvojče najednou.

> [!TIP]
> Odstranění instance vám umožní vytvořit nový.

## <a name="per-instance-limits"></a>Limity pro jednotlivé Instance

Každá instance Azure digitální dvojče zase může mít:

- Jeden `IoTHub` prostředků
- Jeden `EventHub` koncový bod pro typ události DeviceMessage
- Až tři `EventHub`, `ServiceBus`, nebo `EventGrid` koncové body typu události `SensorChange`, `SpaceChange`, `TopologyOperation`, nebo `UdfCustom`

## <a name="management-api-limits"></a>Omezení rozhraní API pro správu

Žádost o omezení přenosové rychlosti pro vaše rozhraní API pro správu jsou:

- 100 požadavků za sekundu na rozhraní API pro správu
- Jednotné rozhraní API pro správu dotaz může vrátit maximálně 1000 objektů

> [!IMPORTANT]
> Pokud překročíte limit 1 000 objektů, dojde k chybě a bude nutné Zjednodušte dotaz.

## <a name="udf-rate-limits"></a>Omezení přenosové rychlosti UDF

Následující omezení nastavte celkový počet všech volání uživatelem definované funkce k vaší instanci Azure digitální dvojče:

- 400 Klientská knihovna volání za sekundu
- 100 SendNotification volání za sekundu

> [!NOTE]
> Následující akce může způsobit omezení přenosové rychlosti další dočasně použít:
> - Úpravy metadat objektu topologie
> - Aktualizace definic UDF
> - Prvním odesílání telemetrických dat ze zařízení

## <a name="device-telemetry-limits"></a>Limity telemetrie zařízení

Omezení pod limit celkový počet všech zpráv, které může vaše zařízení odesílají do vaší instanci Azure digitální dvojče:

- 100 zpráv za sekundu

## <a name="next-steps"></a>Další postup

Vyzkoušejte si ukázkové digitální dvojče Azure, přejděte na [rychlý start se mají najít místnosti k dispozici](./quickstart-view-occupancy-dotnet.md).