---
title: Omezení služby Azure digitální dvojče veřejné ve verzi preview | Dokumentace Microsoftu
description: Vysvětlení, že Azure digitální dvojče veřejné limity pro služby ve verzi preview.
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2019
ms.author: dwalthermsft
ms.openlocfilehash: 7d9686b9bcc6cb89fabf4fdaa79bf5b8c6c45ddc
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54020621"
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

- Přesně jeden **IoTHub** prostředků.
- Přesně jeden **EventHub** koncový bod pro typ události **DeviceMessage**.
- Až tři **EventHub**, **služby Service Bus**, nebo **EventGrid** koncové body typu události **SensorChange**, **SpaceChange** , **TopologyOperation**, nebo **UdfCustom**.

> [!NOTE]
> Některé parametry, které jsou obvykle definovány při vytváření výše entit Azure IoT nejsou nutné ve verzi public preview.
> - Najdete [Swagger referenční dokumentaci](./how-to-use-swagger.md) pro nejnovější specifikace rozhraní API.

## <a name="azure-digital-twins-management-api-limits"></a>Omezení pro rozhraní API pro správu digitálních Dvojčat v Azure

Omezení přenosové rychlosti žádost pro vaši digitální dvojče API pro správu Azure jsou:

- 100 požadavků za sekundu na digitální dvojče správy rozhraní API Azure.
- Až 1 000 objektů vrácených podle jednoho dotazu Azure digitální dvojče rozhraní API pro správu.

> [!IMPORTANT]
> Pokud překročíte limit 1 000 objektů, dojde k chybě a musí Zjednodušte dotaz.

## <a name="user-defined-functions-rate-limits"></a>Uživatelem definované funkce omezení přenosové rychlosti

Následující omezení nastavte celkový počet všech volání uživatelem definované funkce k vaší instanci Azure digitální dvojče:

- 400 Klientská knihovna volání za sekundu
- 100 **SendNotification** volání za sekundu

> [!NOTE]
> Následující akce může způsobit omezení přenosové rychlosti další dočasně použít:
> - Úpravy metadat objektu topologie
> - Aktualizace provedené k definici uživatelsky definované funkce
> - Zařízení, které odesílají telemetrii poprvé

## <a name="device-telemetry-limits"></a>Limity telemetrie zařízení

Následující omezení cap celkový počet všechny zprávy zasílané vašich zařízení k vaší instanci Azure digitální dvojče:

- 100 zpráv za sekundu

## <a name="next-steps"></a>Další postup

Vyzkoušejte si ukázkové digitální dvojče Azure, přejděte na [rychlý start se mají najít místnosti k dispozici](./quickstart-view-occupancy-dotnet.md).