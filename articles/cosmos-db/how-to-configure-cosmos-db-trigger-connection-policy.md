---
title: Aktivační událost Funkce Azure pro zásady připojení Cosmos DB
description: Zjistěte, jak nakonfigurovat zásady připojení používané aktivační událostí Azure pro Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: maquaran
ms.openlocfilehash: 953121a9a15d4fef56d381e3aab85329fadacce2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604963"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-functions-trigger-for-cosmos-db"></a>Jak nakonfigurovat zásady připojení používané azure funkce aktivační událost pro Cosmos DB

Tento článek popisuje, jak můžete nakonfigurovat zásady připojení při použití aktivační události Azure Functions pro Cosmos DB pro připojení k účtu Azure Cosmos.

## <a name="why-is-the-connection-policy-important"></a>Proč jsou zásady připojení důležité?

Existují dva režimy připojení - Přímý režim a Režim brány. Další informace o těchto režimech připojení najdete v článku [tipy pro výkon.](./performance-tips.md#networking) Ve výchozím nastavení **gateway** se používá k navázání všech připojení na aktivační události Azure Functions pro Cosmos DB. Však nemusí být nejlepší volbou pro scénáře řízené výkonem.

## <a name="changing-the-connection-mode-and-protocol"></a>Změna režimu připojení a protokolu

Ke konfiguraci zásad připojení klienta jsou k dispozici dvě nastavení konfigurace klíčů – **režim připojení** a **protokol připojení**. Můžete změnit výchozí režim připojení a protokol používaný aktivační událostí Azure Funkce pro Cosmos DB a všechny [vazby Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-output.md)). Chcete-li změnit výchozí nastavení, `host.json` musíte najít soubor v projektu Azure Functions nebo v aplikaci Azure Functions a přidat následující [další nastavení](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings):

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Kde `connectionMode` musí mít požadovaný režim připojení (přímé nebo brány) a `protocol` požadovaný protokol připojení (Tcp nebo Https). 

Pokud váš projekt Azure Functions pracuje s runtime Azure Functions V1, konfigurace `documentDB` má `cosmosDB`mírný rozdíl v názvu, měli byste použít místo:

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Při práci s plánem hostingu plánu spotřeby funkcí Azure má každá instance limit v množství připojení soketu, které může udržovat. Při práci s režimem Direct / TCP se podle návrhu vytvoří více připojení, která mohou zasáhnout [limit plánu spotřeby](../azure-functions/manage-connections.md#connection-limit), v takovém případě můžete buď použít režim brány, nebo spustit funkce Azure v [režimu služby App Service](../azure-functions/functions-scale.md#app-service-plan).

## <a name="next-steps"></a>Další kroky

* [Omezení připojení ve funkcích Azure](../azure-functions/manage-connections.md#connection-limit)
* [Tipy pro výkon Azure Cosmos DB](./performance-tips.md)
* [Ukázky kódu](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
