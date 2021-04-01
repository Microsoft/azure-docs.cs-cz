---
title: 'Kurz: migrace dat událostí do Azure synapse Analytics – Azure Event Hubs'
description: Popisuje, jak pomocí Azure Event Grid a funkcí migrovat Event Hubs zachycená data do služby Azure synapse Analytics.
services: event-hubs
ms.date: 12/07/2020
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.openlocfilehash: 77a2b35dea57c71e9e6f07056bd106df2ac109b8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96854142"
---
# <a name="tutorial-migrate-captured-event-hubs-data-to-azure-synapse-analytics-using-event-grid-and-azure-functions"></a>Kurz: migrace zachycených Event Hubs dat do služby Azure synapse Analytics pomocí Event Grid a Azure Functions
Služba Azure Event Hubs [Capture](./event-hubs-capture-overview.md) umožňuje automaticky zachytit streamovaná data v Event Hubs ve službě Azure Blob Storage nebo v Azure Data Lake Storage. V tomto kurzu se dozvíte, jak migrovat zachycená Event Hubsová data z úložiště do Azure synapse Analytics pomocí funkce Azure, která se aktivuje [Event Grid](../event-grid/overview.md).

[!INCLUDE [event-grid-event-hubs-functions-synapse-analytics.md](../../includes/event-grid-event-hubs-functions-synapse-analytics.md)]

## <a name="next-steps"></a>Další kroky 
Pomocí kombinace datového skladu s výkonnými nástroji pro vizualizaci dat můžete získat užitečné přehledy.

Tento článek ukazuje, jak používat [Power BI se službou Azure synapse Analytics](/power-bi/connect-data/service-azure-sql-data-warehouse-with-direct-connect)