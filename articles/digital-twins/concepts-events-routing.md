---
title: Směrování události a zprávy s Azure digitální dvojče | Dokumentace Microsoftu
description: Přehled směrování událostí a zpráv do koncových bodů služby s Dvojčaty digitální Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 9b861f0991b11637c7b27b645d4506e658ea53b3
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324103"
---
# <a name="routing-events-and-messages"></a>Směrování události a zprávy

Řešení IoT často seskupení několik výkonných služeb, včetně úložiště, analýzu a další. Tento článek popisuje, jak připojit aplikace Azure digitální dvojče ke službám Azure úložiště, analýzy a AI rozšiřuje díky hlubší analýze a funkce.

## <a name="route-types"></a>Typy směrování

Digitální dvojče nabízí dva způsoby, jak integrace událostí IoT do jiných služeb Azure nebo obchodních aplikací:

* **Směrování událostí digitálním Dvojčat**: Azure digitální dvojče události lze aktivovat, když objekt v prostorových grafu se změní při příjmu telemetrických dat, nebo když uživatelsky definovaná funkce vytvoří oznámení na základě předdefinovaných podmínek. Uživatelé můžou posílat událostí do [Event Hubs](https://azure.microsoft.com/services/event-hubs/), [témat sběrnice Service Bus](https://azure.microsoft.com/services/service-bus/), nebo [události mřížky](https://azure.microsoft.com/services/event-grid/) k dalšímu zpracování.

* **Směrování telemetrii zařízení**: kromě směrování událostí, digitální dvojče Azure můžete také směrovat zprávy typu zařízení nezpracovaná telemetrická data do služby Event Hubs pro další přehledy a analýzy. Tyto typy zpráv, které nejsou zpracovávány digitální dvojče a pouze se předávají do **centra událostí**.

Uživatelé mohou zadat jednu nebo více koncových bodů odchozího přenosu dat k odeslání události nebo předávání zpráv. Události a zprávy se odešlou do koncových bodů podle těchto předdefinovaných směrování požadavků. Jinými slovy můžou uživatelé nastavit určité jeden koncový bod pro příjem událostí operace graf, jiné příjem událostí telemetrie zařízení, a tak dále.

![Událostí digitálním Dvojčat směrování][1]

Směrování **Event Hubs** zachová pořadí, ve které telemetrická data jsou zprávy odesílány, tak, aby jejich doručení na koncovém bodu ve stejném pořadí jako původně přijaté. **Event Grid** a **služby Service Bus** není zaručit, že koncové body se zobrazí události ve stejném pořadí, že k nim došlo. Schéma událostí obsahují časové razítko, které slouží k identifikaci pořadí po doručení událostí v koncovém bodě.

## <a name="route-implementation"></a>Implementace tras

Služba Azure digitální dvojče aktuálně podporuje následující **EndpointTypes**:

* **Centrum EventHub**: je koncový bod připojovací řetězec centra událostí.
* **Služby Service Bus**: je koncový bod služby Service Bus připojovací řetězec.
* **EventGrid**: je koncový bod služby Event Grid připojovací řetězec.

Digitální dvojče Azure aktuálně podporuje následující **EventTypes** , který se pošle na zvoleném koncový bod:

* **DeviceMessages**: telemetrické zprávy odeslané ze zařízení uživatelů se předávají v systému.
* **TopologyOperation**: jsou operace, které změní grafu nebo metadata grafu. Například přidáním nebo odstraněním entity, jako je například mezeru.
* **SpaceChange**: jsou změny do prostoru vypočítanou hodnotu jako výsledek telemetrické zprávy zařízení.
* **SensorChange**: se změnami senzor vypočítanou hodnotu jako výsledek telemetrické zprávy zařízení.
* **UdfCustom**: jsou vlastní oznámení z uživatelem definované funkce.

> [!IMPORTANT]
> Ne všechny **EndpointTypes** podporují všechny **EventTypes**.
> V následující tabulce najdete v článku **EventTypes** , které jsou povolené pro každou **EndpointType**.

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| **centra událostí**|     X          |         X         |     X       |      X       |   X       |
| **služby Service Bus**|              |         X         |     X       |      X       |   X       |
| **EventGrid**|               |         X         |     X       |      X       |   X       |

>[!NOTE]
>Další podrobnosti o tom, jak vytvořit koncové body a příklady schématu události, najdete v tématu [koncové body a výchozí přenos dat](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Další postup

Informace o omezeních ve verzi public preview, přečtěte si [digitální dvojče Azure ve verzi preview omezení](concepts-service-limits.md).

Vyzkoušejte si ukázkové digitální dvojče Azure, přečtěte si téma [rychlý start se mají najít místnosti k dispozici](quickstart-view-occupancy-dotnet.md).

<!-- Images -->
[1]: media/concepts/digital-twins-events-routing.png