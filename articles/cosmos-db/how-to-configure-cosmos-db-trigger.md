---
title: Azure Functions Trigger pro pokročilou konfiguraci Cosmos DB
description: Přečtěte si, jak nakonfigurovat zásady protokolování a připojení používané Azure Functions triggerem pro Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/19/2020
ms.author: maquaran
ms.openlocfilehash: c47d18726d9581b1b03aa2e676a71d6ca1bc1b7d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086461"
---
# <a name="how-to-configure-logging-and-connectivity-with-the-azure-functions-trigger-for-cosmos-db"></a>Jak nakonfigurovat protokolování a konektivitu pomocí triggeru Azure Functions pro Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tento článek popisuje pokročilé možnosti konfigurace, které můžete nastavit při použití triggeru Azure Functions pro Cosmos DB.

## <a name="enabling-trigger-specific-logs"></a>Povolení protokolů specifických pro aktivační události

Aktivační událost Azure Functions pro Cosmos DB používá interní [knihovnu Change feed Processor](./change-feed-processor.md) a knihovna generuje sadu protokolů o stavu, které lze použít k monitorování interních operací pro [účely řešení potíží](./troubleshoot-changefeed-functions.md).

Protokoly o stavu popisují, jak Azure Functions Trigger pro Cosmos DB při pokusu o provedení operací při vyrovnávání zatížení nebo při inicializaci chovat.

### <a name="enabling-logging"></a>Povolení protokolování

Pokud chcete povolit protokolování při použití triggeru Azure Functions pro Cosmos DB, vyhledejte `host.json` soubor v projektu Azure Functions nebo v aplikaci Azure functions a [nakonfigurujte úroveň požadovaného protokolování](../azure-functions/functions-monitoring.md#log-levels-and-categories). Povolte trasování pro  `Host.Triggers.CosmosDB` , jak je znázorněno v následující ukázce:

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

Po nasazení funkce Azure Functions s aktualizovanou konfigurací se v rámci trasování zobrazí Trigger Azure Functions pro Cosmos DB protokoly. Protokoly můžete zobrazit v nakonfigurovaném zprostředkovateli protokolování pod *kategorií* `Host.Triggers.CosmosDB` .

### <a name="query-the-logs"></a>Dotazování protokolů

Spusťte následující dotaz pro dotazování protokolů generovaných triggerem Azure Functions pro Cosmos DB ve [službě Azure Application Insights Analytics](../azure-monitor/log-query/log-query-overview.md):

```sql
traces
| where customDimensions.Category == "Host.Triggers.CosmosDB"
```

## <a name="configuring-the-connection-policy"></a>Konfigurace zásad připojení

Existují dva režimy připojení – přímý režim a režim brány. Další informace o těchto režimech připojení najdete v článku [režimy připojení](sql-sdk-connection-modes.md) . Ve výchozím nastavení se **Brána** používá k navázání všech připojení na Azure Functions triggeru Cosmos DB. Nemusí se ale jednat o nejlepší možnost pro scénáře řízené výkonem.

### <a name="changing-the-connection-mode-and-protocol"></a>Změna režimu připojení a protokolu

Ke konfiguraci zásad připojení klienta – **režimu připojení** a **protokolu připojení** jsou k dispozici dvě klíčová nastavení konfigurace. Můžete změnit výchozí režim připojení a protokol používaný triggerem Azure Functions pro Cosmos DB a všechny [vazby Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-output.md)). Chcete-li změnit výchozí nastavení, je třeba vyhledat `host.json` soubor v projektu Azure Functions nebo v aplikaci Azure functions a přidat následující [Další nastavení](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings):

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Kde `connectionMode` musí být požadovaný režim připojení (Direct nebo Gateway) a `protocol` požadovaný protokol připojení (TCP nebo https). 

Pokud váš Azure Functions projekt pracuje s modulem runtime Azure Functions V1, má konfigurace mírné rozdíly v nesprávném názvu, měli byste použít `documentDB` místo `cosmosDB` :

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Při práci s plánem hostování Azure Functions spotřeby má každá instance omezení množství připojení soketu, která může udržovat. Při práci s režimem Direct/TCP se vytvoří návrh většího [počtu](../azure-functions/manage-connections.md#connection-limit)připojení. v takovém případě můžete buď použít režim brány, nebo spustit Azure Functions v [režimu App Service](../azure-functions/functions-scale.md#app-service-plan).

## <a name="next-steps"></a>Další kroky

* [Omezení připojení v Azure Functions](../azure-functions/manage-connections.md#connection-limit)
* [Povolte monitorování](../azure-functions/functions-monitoring.md) v aplikacích Azure Functions.
* Naučte se [diagnostikovat a řešit běžné problémy](./troubleshoot-changefeed-functions.md) při použití triggeru Azure Functions pro Cosmos DB.