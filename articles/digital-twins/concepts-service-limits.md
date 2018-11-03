---
title: Omezení služby Azure digitální dvojče veřejné ve verzi preview | Dokumentace Microsoftu
description: Vysvětlení, že Azure digitální dvojče veřejné limity pro služby ve verzi preview.
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/26/2018
ms.author: dwalthermsft
ms.openlocfilehash: 86ae75118dd1311ea2ae92fb718fe4c58b8e5673
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50961751"
---
# <a name="public-preview-service-limits"></a>Omezení služby ve verzi Public Preview

Digitální dvojče Azure ve verzi public preview, má následující dočasné předplatné, instanci a omezení přenosové rychlosti.

Existují tato omezení, která pomůže zjednodušit získání informací o nové službě a její mnoho funkcí.

> [!NOTE]
> Tato omezení se zvýší nebo odebrání všeobecné dostupnosti (GA).

## <a name="per-subscription-limits"></a>Omezení na předplatné

Každé předplatné Azure ve verzi public preview, můžete vytvořit nebo spustit pouze jedna instance Azure digitální dvojče najednou.

> [!TIP]
> Pokud odstraníte instanci, můžete vytvořit nový.

## <a name="per-instance-limits"></a>Limity pro jednotlivé instance

Každá instance Azure digitální dvojče zase může mít:

- Jeden **IoTHub** prostředků.
- Jeden **EventHub** koncový bod pro typ události **DeviceMessage**.
- Až tři **EventHub**, **služby Service Bus**, nebo **EventGrid** koncové body typu události **SensorChange**, **SpaceChange** , **TopologyOperation**, nebo **UdfCustom**.

## <a name="management-api-limits"></a>Omezení rozhraní API pro správu

Žádost o omezení přenosové rychlosti pro vaše rozhraní API pro správu jsou:

- 100 požadavků za sekundu na rozhraní API pro správu.
- Až 1 000 objektů vrácených dotazem jednotné rozhraní API pro správu. 

> [!IMPORTANT]
> Pokud překročíte limit 1 000 objektů, dojde k chybě a musí Zjednodušte dotaz.

## <a name="udf-rate-limits"></a>Omezení přenosové rychlosti UDF

Následující omezení nastavte celkový počet všech volání uživatelem definované funkce k vaší instanci Azure digitální dvojče:

- 400 Klientská knihovna volání za sekundu
- 100 **SendNotification** volání za sekundu

> [!NOTE]
> Následující akce může způsobit omezení přenosové rychlosti další dočasně použít:
> - Úpravy metadat objektu topologie
> - Aktualizace provedené definice UDF
> - Zařízení, které odesílají telemetrii poprvé

## <a name="device-telemetry-limits"></a>Limity telemetrie zařízení

Následující omezení cap celkový počet všechny zprávy zasílané vašich zařízení k vaší instanci Azure digitální dvojče:

- 100 zpráv za sekundu

## <a name="next-steps"></a>Další postup

Vyzkoušejte si ukázkové digitální dvojče Azure, přejděte na [rychlý start se mají najít místnosti k dispozici](./quickstart-view-occupancy-dotnet.md).