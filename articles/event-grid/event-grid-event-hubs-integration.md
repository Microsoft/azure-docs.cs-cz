---
title: 'Kurz: odeslání Event Hubs dat do datového skladu – Event Grid'
description: Popisuje, jak ukládat Event Hubs zachycená data ve službě Azure synapse Analytics prostřednictvím aktivačních událostí Azure Functions a Event Grid.
ms.topic: tutorial
ms.date: 12/07/2020
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 7b0e471e32650490e1896bb6ea171c8223b21378
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854712"
---
# <a name="tutorial-stream-big-data-into-a-data-warehouse"></a>Kurz: streamování velkých objemů dat do datového skladu
Azure [Event Grid](overview.md) je inteligentní služba Směrování událostí, která umožňuje reagovat na oznámení nebo události z aplikací a služeb. Například může aktivovat funkci Azure Functions pro zpracování Event Hubs dat zachycených do úložiště objektů BLOB nebo Data Lake Storage. V této [ukázce](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo) se dozvíte, jak pomocí Event Grid a Azure Functions migrovat zachycená Event Hubs data z úložiště objektů blob do služby Azure synapse Analytics, konkrétně z vyhrazeného fondu SQL.

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>Další kroky

* Pokud chcete poznat rozdíly ve službách Azure pro zasílání zpráv, přečtěte si článek [Výběr mezi službami Azure, které doručují zprávy](compare-messaging-services.md).
* Úvod do Event Gridu najdete v článku [Informace o službě Event Grid](overview.md).
* Úvod do služby Event Hubs Capture najdete v článku o [povolení služby Event Hubs Capture na webu Azure Portal](../event-hubs/event-hubs-capture-enable-through-portal.md).
* Další informace o nastavení a spuštění ukázky najdete v článku s [ukázkou služeb Event Hubs Capture a Event Grid](https://github.com/Azure/azure-event-hubs/tree/master/samples/e2e/EventHubsCaptureEventGridDemo).
