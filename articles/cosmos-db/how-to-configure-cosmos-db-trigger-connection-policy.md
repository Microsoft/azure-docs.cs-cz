---
title: Zásady připojení služby Azure Cosmos DB triggeru
description: Další informace o konfiguraci zásad připojení používá aktivační událost Azure Cosmos DB
author: ealsur
ms.service: cosmos-db
ms.topic: sample
ms.date: 06/05/2019
ms.author: maquaran
ms.openlocfilehash: 584d59884b70d2ee8243216e6f907fc9ec2d8ad4
ms.sourcegitcommit: 45e4466eac6cfd6a30da9facd8fe6afba64f6f50
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2019
ms.locfileid: "66755329"
---
# <a name="how-to-configure-the-connection-policy-used-by-azure-cosmos-db-trigger"></a>Jak nakonfigurovat zásady připojení používá aktivační událost Azure Cosmos DB

Tento článek popisuje, jak nakonfigurovat zásady připojení při použití aktivační událost Azure Cosmos DB k připojení k vašemu účtu Azure Cosmos.

## <a name="why-is-the-connection-policy-important"></a>Proč je důležité zásady připojení?

Existují dva režimy připojení – přímý režim a režim brány. Další informace o těchto režimech připojení, najdete v článku [tipy ke zvýšení výkonu](./performance-tips.md#networking) článku. Ve výchozím nastavení **brány** se používá k navázání všechna připojení na aktivační událost Azure Cosmos DB. Však nemusí být nejvhodnější pro scénáře výkon řízená.

## <a name="changing-the-connection-mode-and-protocol"></a>Změna režimu připojení a protokol

Existují dvě klíčové konfigurace nastavení k dispozici ke konfiguraci zásad připojení klienta – **režim připojení** a **připojení protokolu**. Můžete změnit výchozí režim připojení a protokol používá aktivační událost Azure Cosmos DB a všechny [vazby Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#output)). Chcete-li změnit výchozí nastavení, je potřeba najít `host.json` souboru v projektu Azure Functions nebo aplikace funkcí Azure a přidejte následující [dalších nastavení](../azure-functions/functions-bindings-cosmosdb-v2.md#hostjson-settings):

```js
{
  "cosmosDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

Kde `connectionMode` musí mít režim požadované připojení (přímo nebo brány) a `protocol` požadované připojení protokolu (Tcp nebo Https). 

Pokud váš projekt Azure Functions pracuje s modulem runtime Azure Functions V1, konfigurace má název mírné rozdíl, měli byste použít `documentDB` místo `cosmosDB`:

```js
{
  "documentDB": {
    "connectionMode": "Direct",
    "protocol": "Tcp"
  }
}
```

> [!NOTE]
> Při práci s plánem Azure Functions spotřeby plán hostování, každá instance má limit množství připojení soketu, která můžete spravovat. Při práci s přímým / TCP režimu návrhu, se vytvoří další připojení a může přístupů [plánu Consumption limit](../azure-functions/manage-connections.md#connection-limit), v takovém případě můžete použít režim brány nebo spouštění Azure Functions [režim aplikací služby](../azure-functions/functions-scale.md#app-service-plan).

## <a name="next-steps"></a>Další postup

* [Omezení počtu připojení ve službě Azure Functions](../azure-functions/manage-connections.md#connection-limit)
* [Tipy ke zvýšení výkonu Azure Cosmos DB](./performance-tips.md)
* [Ukázky kódu](https://github.com/ealsur/serverless-recipes/tree/master/connectionmode)
