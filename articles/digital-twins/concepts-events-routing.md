---
title: Události směrování a zprávy – digitální dvojčata Azure | Dokumenty společnosti Microsoft
description: Přehled událostí směrování a zpráv do koncových bodů služby pomocí Azure Digital Twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: 65b760eaf28d907fab3654ed92f960be7556b0d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75862352"
---
# <a name="routing-iot-events-and-messages"></a>Směrování událostí a zpráv IoT

Řešení Internetu věcí často spojují několik výkonných služeb, které zahrnují úložiště, analýzy a další. Tento článek popisuje, jak propojit aplikace Azure Digital Twins se službami Azure Analytics, AI a storage services, aby získaly hlubší přehledy a funkce.

## <a name="route-types"></a>Typy tras  

Azure Digital Twins nabízí dva způsoby, jak propojit události IoT s jinými službami Azure nebo obchodními aplikacemi:

* **Směrování událostí Azure Digital Twins**: Objekt v prostorovém grafu, který mění, telemetrická data, která je přijata, nebo uživatelem definovaná funkce, která vytvoří oznámení na základě předdefinovaných podmínek, může aktivovat události Azure Digital Twins. Uživatelé můžou tyto události odesílat do [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), [témata Azure Service Bus](https://azure.microsoft.com/services/service-bus/)nebo Azure Event [Grid](https://azure.microsoft.com/services/event-grid/) k dalšímu zpracování.

* **Telemetrie směrovacího zařízení**: Kromě událostí směrování mohou digitální dvojčata Azure také směrovat nezpracované zprávy telemetrie zařízení do centra událostí pro další přehledy a analýzy. Tyto typy zpráv nejsou zpracovávány Azure Digital Twins. A jsou přepojeny pouze do centra událostí.

Uživatelé mohou zadat jeden nebo více koncových bodů odchozího přenosu, které mají odesílat události nebo předávat zprávy. Události a zprávy budou odeslány koncovým bodům podle těchto předdefinovaných předvoleb směrování. Jinými slovy, uživatelé mohou určit určitý koncový bod pro příjem událostí operace grafu, jiný přijímat události telemetrie zařízení a tak dále.

[![Směrování událostí Azure Digital Twins](media/concepts/digital-twins-events-routing.png)](media/concepts/digital-twins-events-routing.png#lightbox)

Směrování do centra událostí udržuje pořadí, ve kterém jsou odesílány telemetrické zprávy. Takže dorazí na koncový bod ve stejném pořadí jako původně přijaté. 

Grid událostí a service bus nezaručují, že koncové body budou přijímat události ve stejném pořadí, v jakém došlo. Schéma události však obsahuje časové razítko, které lze použít k identifikaci pořadí po události dorazí do koncového bodu.

## <a name="route-implementation"></a>Implementace trasy

Služba Azure Digital Twins aktuálně podporuje následující **typy koncových bodů**:

* **EventHub** je koncový bod připojovacího řetězce Centra událostí.
* **ServiceBus** je koncový bod řetězce připojení služby Service Bus.
* **EventGrid** je koncový bod připojovacího řetězce Event Grid.

Azure Digital Twins aktuálně podporuje následující **EventTypes,** které budou odeslány do vybraného koncového bodu:

* **DeviceMessages** jsou telemetrické zprávy odeslané ze zařízení uživatelů a předávané systémem.
* **TopologyOperation** je operace, která mění graf nebo metadata grafu. Příkladem je přidání nebo odstranění entity, například mezery.
* **SpaceChange** je změna vypočítané hodnoty prostoru, která je výsledkem telemetrické zprávy zařízení.
* **SensorChange** je změna vypočítané hodnoty senzoru, která je výsledkem telemetrické zprávy zařízení.
* **UdfCustom** je vlastní oznámení z uživatelem definované funkce.

> [!IMPORTANT]  
> Ne všechny **typy koncových bodů** podporují všechny **typy událostí**.
> Zkontrolujte následující tabulku pro **EventTypes,** které jsou povoleny pro každý **EndpointType**.

|             | Zprávy o zařízení | Operace topologie | Změna prostoru | Změna senzoru | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| Centrum událostí|     ×          |         ×         |     ×       |      ×       |   ×       |
| ServiceBus|              |         ×         |     ×       |      ×       |   ×       |
| EventGrid|               |         ×         |     ×       |      ×       |   ×       |

>[!NOTE]  
>Další informace o tom, jak vytvořit koncové body a příklady schématu událostí, přečtěte [si odchozí a koncové body](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Další kroky

- Další informace o limitech náhledu Azure Digital Twins najdete v článek [Limity služby Public Preview](concepts-service-limits.md).

- Chcete-li vyzkoušet ukázku azure digitální dvojčata, přečtěte si [úvodní příručku k nalezení dostupných místností](quickstart-view-occupancy-dotnet.md).