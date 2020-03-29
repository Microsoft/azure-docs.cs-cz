---
title: Reakce na události Mediálních služeb Azure | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak používat Azure Event Grid k odběru událostí Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/08/2019
ms.author: juliako
ms.openlocfilehash: e24bacb0ea7ab406442022915872fc77e9cc1a5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74887880"
---
# <a name="handling-event-grid-events"></a>Zpracování událostí služby Event Grid

Události media services umožňují aplikacím reagovat na různé události (například událost změny stavu úlohy) pomocí moderních architektur bez serveru. Činí tak bez nutnosti složitého kódu nebo nákladné a neefektivní volební služby. Místo toho se události přessazují přes [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) na obslužné rutiny událostí, jako jsou Azure [Functions](https://azure.microsoft.com/services/functions/), Azure [Logic Apps](https://azure.microsoft.com/services/logic-apps/)nebo dokonce do vlastního Webhooku, a platíte jenom za to, co používáte. Informace o cenách naleznete v [tématu Event Grid pricing](https://azure.microsoft.com/pricing/details/event-grid/).

Dostupnost událostí media services je vázána na [dostupnost](../../event-grid/overview.md) služby Event Grid a bude k dispozici v jiných oblastech jako Event Grid.  

## <a name="media-services-events-and-schemas"></a>Události a schémata mediálních služeb

Mřížka událostí používá [odběry událostí](../../event-grid/concepts.md#event-subscriptions) ke směrování zpráv událostí odběratelům. Události služby Media Services obsahují všechny informace, které potřebujete k reakci na změny dat. Událost Služby Media Services můžete identifikovat, protože vlastnost eventType začíná na "Microsoft.Media".

Další informace naleznete v [tématu Schémata událostí služby Media Services](media-services-event-schemas.md).

## <a name="practices-for-consuming-events"></a>Postupy pro náročné události

Aplikace, které zpracovávají události služby Media Services, by se měly řídit několika doporučenými postupy:

* Jako více odběry lze nakonfigurovat pro směrování událostí do stejné obslužné rutiny události, je důležité nepředpokládat, že události jsou z určitého zdroje, ale zkontrolovat téma zprávy, aby bylo zajištěno, že pochází z účtu úložiště, který očekáváte.
* Podobně zkontrolujte, zda eventType je ten, který jste připraveni ke zpracování a nepředpokládejte, že všechny události, které obdržíte, budou typy, které očekáváte.
* Ignorujte pole, kterým nerozumíte.  Tento postup vám pomůže udržet odolnost vůči novým funkcím, které by mohly být přidány v budoucnu.
* Pomocí shody předpony a přípony předmětu omezte události na určitou událost.

> [!NOTE]
> Události podléhají smlouvě o úrovni služby Grid událostí [(SLA).](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) Pokud chcete dostávat oznámení o událostech pomocí rozhraní API, podívejte se na příklady, jak využívat události, pomocí [sady .NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet) nebo [Java SDK](https://github.com/Azure-Samples/media-services-v3-java).

## <a name="next-steps"></a>Další kroky

* [Sledovat události - portál](monitor-events-portal-how-to.md)
* [Monitorování událostí – rozhraní příkazového řádku](job-state-events-cli-how-to.md)
