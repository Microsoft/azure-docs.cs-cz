---
title: Omezení služby Azure digitální dvojče veřejné ve verzi preview | Dokumentace Microsoftu
description: Principy Azure digitální dvojče veřejné limity pro služby ve verzi preview.
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: dwalthermsft
ms.openlocfilehash: f9a3d934de47630ac3fd2356001014d006c2a4eb
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50212263"
---
# <a name="public-preview-service-limits"></a>Omezení služby ve verzi Public Preview

Během **ve verzi Public Preview**, digitální dvojče Azure bude mít dočasný předplatného, instance a omezení přenosové rychlosti, které jsou popsané níže.

Existují tato omezení, která pomůže zjednodušit získání informací o nové službě a její mnoho funkcí.

> [!NOTE]
> Tato omezení se zvýšila nebo odebrání **všeobecné dostupnosti** (**GA**).

## <a name="per-subscription-limits"></a>Omezení na předplatné

Během **ve verzi Public Preview**, každé předplatné Azure můžete vytvořit nebo spuštěné přesně jednu instanci Azure digitální dvojče najednou.

> [!TIP]
> Odstranění instance vám umožní vytvořit nový.

## <a name="per-instance-limits"></a>Limity pro jednotlivé Instance

Každá instance Azure digitální dvojče zase může mít:

- Jeden **IoTHub** prostředků
- Jeden **EventHub** koncový bod pro typ události **DeviceMessage**
- Až tři **EventHub**, **služby Service Bus**, nebo **EventGrid** koncové body typu události **SensorChange**, **SpaceChange** , **TopologyOperation**, nebo **UdfCustom**

## <a name="management-api-limits"></a>Omezení rozhraní API pro správu

Žádost o omezení přenosové rychlosti pro vaše rozhraní API pro správu jsou:

- 100 požadavků za sekundu na rozhraní API pro správu
- Jednotné rozhraní API pro správu dotaz může vrátit maximálně 1000 objektů

> [!IMPORTANT]
> Pokud překročíte limit 1 000 objektů, dojde k chybě a bude nutné Zjednodušte dotaz.

## <a name="udf-rate-limits"></a>Omezení přenosové rychlosti UDF

Následující omezení nastavte celkový počet všech volání uživatelem definované funkce k vaší instanci Azure digitální dvojče:

- 400 Klientská knihovna volání za sekundu
- 100 **SendNotification** volání za sekundu

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