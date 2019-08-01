---
title: Směrování událostí a zpráv pomocí digitálních vláken Azure | Microsoft Docs
description: Přehled událostí a zpráv o směrování do koncových bodů služby pomocí digitálních vláken Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: alinast
ms.openlocfilehash: a013525109fe85ad70e5aaa5895da20f5abc3237
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68638528"
---
# <a name="routing-events-and-messages"></a>Směrování událostí a zpráv

Řešení IoT často jednotkují několik výkonných služeb, které zahrnují úložiště, analýzu a další. Tento článek popisuje, jak propojit aplikace Azure Digital proniknout do Azure Analytics, AI a služby úložiště a poskytnout jim hlubší přehledy a funkce.

## <a name="route-types"></a>Typy tras  

Funkce digitálních vláken Azure nabízí dva způsoby, jak propojit události IoT s jinými službami nebo obchodními aplikacemi Azure:

* **Směrování událostí digitálních vláken Azure**: Objekt v prostorovém grafu, který změní, získaná data telemetrie nebo uživatelsky definovanou funkci, která vytvoří oznámení na základě předdefinovaných podmínek, může aktivovat události digitálních vláken Azure. Uživatelé můžou tyto události poslat do [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [Azure Service Bus témata](https://azure.microsoft.com/services/service-bus/)nebo [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) pro další zpracování.

* **Telemetrii směrování zařízení**: Kromě událostí směrování můžou digitální vlákna Azure také směrovat zprávy telemetrie nezpracovaného zařízení na Event Hubs pro další poznatky a analýzy. Tyto typy zpráv nejsou zpracovávány pomocí digitálních vláken Azure. A přenáší se pouze do centra událostí.

Uživatelé mohou zadat jeden nebo více koncových bodů odchozích zpráv pro odeslání událostí nebo přeposílat zprávy. Události a zprávy budou odesílány do koncových bodů podle těchto předdefinovaných předvoleb směrování. Jinými slovy uživatelé můžou určit určitý koncový bod pro příjem událostí operací grafu, další pro příjem událostí telemetrie zařízení a tak dále.

![Směrování událostí digitálních vláken Azure][1]

Směrování do Event Hubs udržuje pořadí, ve kterém se odesílají zprávy telemetrie. Proto dorazí na koncový bod ve stejném pořadí, ve kterém byly původně přijaty. Event Grid a Service Bus nezaručují, že koncové body budou přijímat události ve stejném pořadí, ve kterém se objevily. Schéma událostí však zahrnuje časové razítko, které lze použít k identifikaci pořadí po doručení událostí do koncového bodu.

## <a name="route-implementation"></a>Implementace trasy

Služba digitálních vláken Azure v současné době podporuje následující **EndpointTypes**:

* **EventHub** je koncový bod připojovacího řetězce Event Hubs.
* **ServiceBus** je koncový bod připojovacího řetězce Service Bus.
* **EventGrid** je koncový bod připojovacího řetězce Event Grid.

Digitální vlákna Azure v současné době podporují následující **EventType** , které se odešlou na zvolený koncový bod:

* **DeviceMessages** jsou zprávy telemetrie odesílané ze zařízení uživatelů a předávané systémem.
* **TopologyOperation** je operace, která změní graf nebo metadata grafu. Příkladem je přidání nebo odstranění entity, například mezera.
* **SpaceChange** je změna vypočítané hodnoty v prostoru, která je výsledkem zprávy telemetrie zařízení.
* **SensorChange** je změna vypočítané hodnoty senzoru, která je výsledkem zprávy telemetrie zařízení.
* **UdfCustom** je vlastní oznámení z uživatelsky definované funkce.

> [!IMPORTANT]  
> Ne všechny **EndpointTypes** podporují všechny **EventType**.
> V následující tabulce jsou pro **EventType** , které jsou povoleny pro jednotlivé **EndpointType**.

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>Další informace o vytváření koncových bodů a příkladů "schématu událostí" najdete v tématu [výstupní informace a koncové body](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Další kroky

- Další informace o omezeních Azure Digital Forms Preview najdete v tématu [omezení služby Public Preview](concepts-service-limits.md).

- Ukázku digitálních vláken Azure si můžete vyzkoušet v rychlém startu, kde najdete [dostupné místnosti](quickstart-view-occupancy-dotnet.md).

<!-- Images -->
[1]: media/concepts/digital-twins-events-routing.png
