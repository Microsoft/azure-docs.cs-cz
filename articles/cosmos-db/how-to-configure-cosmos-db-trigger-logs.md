---
title: Zaznamená aktivační událost Azure Cosmos DB
description: Zjistěte, jak vystavit protokoly aktivační událost Azure Cosmos DB a Azure funkce protokolování kanálu
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 5/2/2019
ms.author: maquaran
ms.openlocfilehash: a598842ecde9508a6c2185a6097f689252fda60c
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65515078"
---
# <a name="how-to-configure-and-read-the-azure-cosmos-db-trigger-logs"></a>Jak nakonfigurovat a čtení protokolů aktivační událost Azure Cosmos DB

Tento článek popisuje, jak nakonfigurovat prostředí Azure Functions k odeslání protokolů aktivační událost Azure Cosmos DB s vaší nakonfigurovanou [řešení pro monitorování](../azure-functions/functions-monitoring.md).

## <a name="included-logs"></a>Zahrnuté protokoly

Aktivační událost Azure Cosmos DB používá [knihovna procesoru kanálu změn](./change-feed-processor.md) interně a knihovně generuje sadu protokoly stavu, které můžete použít ke sledování interní operace pro [řešení potíží](./troubleshoot-changefeed-functions.md).

Protokoly stavu popisují, jak se aktivační událost Azure Cosmos DB chová při pokusu o operací během scénáře Vyrovnávání zatížení nebo inicializace.

## <a name="enabling-logging"></a>Povolení protokolování

Chcete-li povolit protokolování aktivační událost Azure Cosmos DB, vyhledejte `host.json` soubor v projektu Azure Functions nebo aplikace funkcí Azure a [nakonfigurovat úroveň protokolování požadované](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). Budete muset povolit trasování pro `Host.Triggers.CosmosDB` jak je znázorněno v následujícím příkladu:

```js
{
  "version": "2.0",
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "Host.Triggers.CosmosDB": "Trace"
    }
  }
}
```

Po nasazení funkce Azure Functions s aktualizovanou konfigurací, zobrazí se aktivační událost Azure Cosmos DB protokoly jako součást trasování. Můžete zobrazit protokoly ve zprostředkovateli nakonfigurované protokolování v části *kategorie* `Host.Triggers.CosmosDB`.

## <a name="query-the-logs"></a>Dotazování protokolů

Spusťte následující dotaz k dotazu generované protokoly aktivační událost Azure Cosmos DB v [Azure Application Insights Analytics](../azure-monitor/app/analytics.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Další postup

* [Povolit monitorování](../azure-functions/functions-monitoring.md) v aplikacích Azure Functions.
* Zjistěte, jak [Diagnostika a řešení běžných problémů s](./troubleshoot-changefeed-functions.md) při použití aktivační událost Azure Cosmos DB ve službě Azure Functions.