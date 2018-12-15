---
title: Směrování události a zprávy s Dvojčaty digitální Azure | Dokumentace Microsoftu
description: Přehled směrování událostí a zpráv do koncových bodů služby s Dvojčaty digitální Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: e16d27314a159b124e35560ffb8cd9685fc5c7a0
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438009"
---
# <a name="routing-events-and-messages"></a>Směrování událostí a zpráv

Řešení IoT často seskupení několik výkonných služeb, které zahrnují úložiště, analýzu a další. Tento článek popisuje, jak připojit aplikace Azure digitální dvojče ke službám Azure úložiště, analýzy a AI a umožnit jim podrobnější přehledy a funkce.

## <a name="route-types"></a>Typy směrování  

Digitální dvojče Azure nabízí dva způsoby, jak integrace událostí IoT do jiných služeb Azure nebo obchodních aplikací:

* **Směrování událostí Azure digitální dvojče**: Objekt prostorový graf změny, telemetrická data, která se zobrazila, nebo uživatelem definovanou funkci, která vytvoří oznámení na základě předdefinovaných podmínek, mohou aktivovat události digitální dvojče Azure. Uživatelé můžou posílat událostí do [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [témat Azure Service Bus](https://azure.microsoft.com/services/service-bus/), nebo [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) k dalšímu zpracování.

* **Směrování telemetrii zařízení**: Kromě směrování událostí digitální dvojče Azure můžete také směrovat nezpracované zařízení telemetrické zprávy do služby Event Hubs pro další přehledy a analýzy. Tyto typy zpráv, které nejsou zpracovávány digitální dvojče Azure. A jste pouze předávají do centra událostí.

Uživatelé mohou zadat jednu nebo více koncových bodů odchozího přenosu dat k odeslání události nebo předávání zpráv. Události a zprávy se odešlou do koncových bodů podle těchto předdefinovaných směrování požadavků. Jinými slovy můžou uživatelé nastavit určité koncový bod pro příjem událostí operace grafu, jiné příjem událostí telemetrie zařízení, a tak dále.

![Azure událostí digitálním Dvojčat směrování][1]

Směrování do služby Event Hubs udržuje pořadí, ve kterém jsou odesílány telemetrické zprávy. Tak jejich doručení na koncovém bodu ve stejném pořadí jako původně přijaté. Event Grid a Service Bus není zaručit, že koncové body se zobrazí události ve stejném pořadí, že k nim došlo. Schéma událostí ale obsahuje časové razítko, které slouží k identifikaci pořadí po doručení událostí v koncovém bodě.

## <a name="route-implementation"></a>Implementace tras

Služba Azure digitální dvojče aktuálně podporuje následující **EndpointTypes**:

* **Centrum EventHub** je koncový bod služby Event Hubs připojovací řetězec.
* **Služby Service Bus** je koncový bod služby Service Bus připojovací řetězec.
* **EventGrid** je koncový bod služby Event Grid připojovací řetězec.

Digitální dvojče Azure aktuálně podporuje následující **EventTypes** , který se pošle na zvoleném koncový bod:

* **DeviceMessages** telemetrických zpráv odesílaných ze zařízení uživatelů a předané v systému.
* **TopologyOperation** je operace, která se mění grafu nebo metadata grafu. Příkladem je přidání nebo odstranění entity, jako je například mezeru.
* **SpaceChange** se mění místo vypočítaná hodnota, která je výsledkem telemetrické zprávy zařízení.
* **SensorChange** se mění senzoru vypočítaná hodnota, která je výsledkem telemetrické zprávy zařízení.
* **UdfCustom** je vlastní oznámení z uživatelem definované funkce.

> [!IMPORTANT]  
> Ne všechny **EndpointTypes** podporují všechny **EventTypes**.
> V následující tabulce najdete **EventTypes** , které jsou povolené pro každou **EndpointType**.

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| Centrum událostí|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>Další informace o tom, jak vytvořit koncové body a příklady schématu událostí v tématu [koncové body a výchozí přenos dat](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Další postup

- Další informace o Azure digitální dvojče limity ve verzi preview, najdete v článku [limity pro služby ve verzi Public preview](concepts-service-limits.md).
- Vyzkoušejte si ukázkové digitální dvojče Azure, najdete v článku [rychlý start se mají najít místnosti k dispozici](quickstart-view-occupancy-dotnet.md).

<!-- Images -->
[1]: media/concepts/digital-twins-events-routing.png
