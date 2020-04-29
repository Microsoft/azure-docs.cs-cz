---
title: Azure Functions Trigger pro zásady připojení Cosmos DB
description: Zjistěte, jak nakonfigurovat zásady připojení používané Azure Functions triggerem pro Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 953121a9a15d4fef56d381e3aab85329fadacce2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77604963"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-functions-trigger-for-cosmos-db"></a>Postup konfigurace zásad připojení používaných Azure Functions triggerem pro Cosmos DB

Tento článek popisuje, jak můžete nakonfigurovat zásady připojení při použití triggeru Azure Functions pro připojení k účtu Azure Cosmos Cosmos DB.

## <a name="why-is-the-connection-policy-important"></a>Proč jsou zásady připojení důležité?

Existují dva režimy připojení – přímý režim a režim brány. Další informace o těchto režimech připojení najdete v článku [tipy pro výkon](./performance-tips.md#networking) . Ve výchozím nastavení se **Brána** používá k navázání všech připojení na Azure Functions triggeru Cosmos DB. Nemusí se ale jednat o nejlepší možnost pro scénáře řízené výkonem.

## <a name="changing-the-connection-mode-and-protocol"></a>Změna režimu připojení a protokolu

Ke konfiguraci zásad připojení klienta – **režimu připojení** a **protokolu připojení**jsou k dispozici dvě klíčová nastavení konfigurace. Můžete změnit výchozí režim připojení a protokol používaný triggerem Azure Functions pro Cosmos DB a všechny [vazby Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-output.md)). Chcete-li změnit výchozí nastavení, je třeba vyhledat `host.json` soubor v projektu Azure Functions nebo v aplikaci Azure functions a přidat následující [Další nastavení](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings):

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Kde `connectionMode` musí být požadovaný režim připojení (Direct nebo Gateway) a `protocol` požadovaný protokol připojení (TCP nebo https). 

Pokud váš Azure Functions projekt pracuje s modulem runtime Azure Functions V1, má konfigurace mírné rozdíly v nesprávném názvu, `documentDB` měli byste `cosmosDB`použít místo:

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
* [Tipy k výkonu Azure Cosmos DB](./performance-tips.md)
* [Ukázky kódu](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
