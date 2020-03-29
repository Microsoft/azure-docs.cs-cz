---
title: Konfigurace a čtení protokolů pomocí aktivační události Azure Functions pro Cosmos DB
description: Zjistěte, jak zpřístupnit protokoly kanálu protokolování funkcí Azure při použití aktivační události Azure pro Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 5ff747b225f8984bcaafd80015e85a9f014bdb50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441832"
---
# <a name="how-to-configure-and-read-the-logs-when-using-azure-functions-trigger-for-cosmos-db"></a>Jak nakonfigurovat a číst protokoly při použití aktivační události Azure funkce pro Cosmos DB

Tento článek popisuje, jak můžete nakonfigurovat prostředí Azure Functions tak, aby odesílalo aktivační událost Azure Functions pro protokoly Cosmos DB do vašeho nakonfigurovaného [řešení monitorování](../azure-functions/functions-monitoring.md).

## <a name="included-logs"></a>Zahrnuté protokoly

Aktivační událost Funkce Azure pro Cosmos DB používá [knihovnu kanálu změn interně](./change-feed-processor.md) a knihovna generuje sadu protokolů stavu, které lze použít ke sledování interních operací pro [účely řešení potíží](./troubleshoot-changefeed-functions.md).

Protokoly stavu popisují, jak se aktivační událost Azure Functions pro Cosmos DB chová při pokusu o operace během scénářů vyrovnávání zatížení nebo inicializace.

## <a name="enabling-logging"></a>Povolení protokolování

Chcete-li povolit protokolování při použití aktivační `host.json` události Azure pro Cosmos DB, vyhledejte soubor v projektu Azure Functions nebo Aplikaci Azure Functions a [nakonfigurujte úroveň požadovaného protokolování](../azure-functions/functions-monitoring.md#log-configuration-in-hostjson). Musíte povolit trasování, `Host.Triggers.CosmosDB` jak je znázorněno v následujícím vzorku:

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

Po nasazení funkce Azure s aktualizovanou konfigurací se jako součást trasování zobrazí aktivační událost Funkce Azure pro protokoly Cosmos DB. Protokoly v nakonfigurovaném poskytovateli protokolování můžete zobrazit v kategorii *.* `Host.Triggers.CosmosDB`

## <a name="query-the-logs"></a>Dotaz na protokoly

Spusťte následující dotaz k dotazování protokolů generovaných aktivační událostí Azure pro Cosmos DB v [Azure Application Insights ' Analytics](../azure-monitor/app/analytics.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="next-steps"></a>Další kroky

* [Povolte monitorování](../azure-functions/functions-monitoring.md) ve svých aplikacích Azure Functions.
* Zjistěte, jak [diagnostikovat a řešit běžné problémy](./troubleshoot-changefeed-functions.md) při použití aktivační události Azure Funkce pro Cosmos DB.