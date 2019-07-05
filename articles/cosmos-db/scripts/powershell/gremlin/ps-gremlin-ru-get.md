---
title: Skript Azure Powershellu – Azure Cosmos DB zjištění propustnosti (RU/s) pro rozhraní Gremlin API
description: Skript Azure Powershellu – Azure Cosmos DB zjištění propustnosti (RU/s) pro rozhraní Gremlin API
author: markjbrown
ms.service: cosmos-db
ms.topic: samples
ms.date: 07/03/2019
ms.author: mjbrown
ms.openlocfilehash: 303bd36149e2a3000ecbf724970b9a43965763fe
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/04/2019
ms.locfileid: "67566840"
---
# <a name="get-throughput-rus-for-a-database-or-graph-for-azure-cosmos-db---gremlin-api"></a>Zjištění propustnosti (RU/s) pro databázi nebo graf pro službu Azure Cosmos DB – rozhraní Gremlin API

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="sample-script"></a>Ukázkový skript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/gremlin/ps-gremlin-ru-get.ps1 "Get throughput on a database or graph for Gremlin API")]

## <a name="clean-up-deployment"></a>Vyčištění nasazení

Po spuštění ukázkového skriptu můžete pomocí následujícího příkazu odebrat skupinu prostředků a všechny k ní přidružené prostředky.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Vysvětlení skriptu

Tento skript používá následující příkazy. Každý příkaz v tabulce odkazuje na příslušnou část dokumentace.

| Příkaz | Poznámky |
|---|---|
|**Prostředky Azure**| |
| [New-AzResource](https://docs.microsoft.com/powershell/module/az.resources/new-azresource) | Vytvoří prostředek. |
|**Skupiny prostředků Azure**| |
| [Remove-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/remove-azresourcegroup) | Odstraní skupinu prostředků včetně všech vnořených prostředků. |
|||

## <a name="next-steps"></a>Další postup

Další informace o Azure PowerShellu najdete v [dokumentaci k Azure PowerShellu](https://docs.microsoft.com/powershell/).

Další ukázkové skripty PowerShellu pro službu Azure Cosmos DB najdete ve [skriptech PowerShellu pro službu Azure Cosmos DB](../../../powershell-samples.md).