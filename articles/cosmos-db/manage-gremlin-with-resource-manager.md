---
title: Šablony Správce prostředků pro rozhraní API pro Azure Cosmos DB Gremlin
description: Pomocí Azure Resource Manager šablon můžete vytvořit a nakonfigurovat rozhraní API pro Azure Cosmos DB Gremlin.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: 2ce6020d31f52a81450bfb7f8be499b13f2ce356
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86028191"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>Správa Azure Cosmos DBch prostředků rozhraní API Gremlin pomocí šablon Azure Resource Manager

V tomto článku se dozvíte, jak pomocí šablon Azure Resource Manager pomáhat s nasazením a správou vašich Azure Cosmos DBch účtů, databází a grafů.

Tento článek obsahuje příklady jenom pro účty rozhraní Gremlin API. příklady pro jiné účty typu rozhraní API najdete v tématu: použití Azure Resource Manager šablon s rozhraním API Azure Cosmos DB pro [Cassandra](manage-cassandra-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)a [tabulkové](manage-table-with-resource-manager.md) články.

> [!IMPORTANT]
>
> * Názvy účtů jsou omezené na 44 znaků, a to vše malými písmeny.
> * Chcete-li změnit hodnoty propustnosti, šablonu znovu nasaďte s aktualizovanými RU/s.
> * Když přidáváte nebo odebíráte umístění účtu Azure Cosmos, nemůžete současně upravovat ostatní vlastnosti. Tyto operace se musí provádět samostatně.

Chcete-li vytvořit některý z níže uvedených prostředků Azure Cosmos DB, zkopírujte následující příklad šablony do nového souboru JSON. Volitelně můžete vytvořit parametry souboru JSON pro použití při nasazování více instancí stejného prostředku s různými názvy a hodnotami. Existuje mnoho způsobů, jak nasadit šablony Azure Resource Manager včetně [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) a [GitHubu](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-db-account-for-gremlin-with-autoscale-provisioned-throughput"></a>Azure Cosmos DB účet pro Gremlin s zajištěnou propustností automatického škálování

Tato šablona vytvoří účet Azure Cosmos pro rozhraní Gremlin API s databází a grafem s propustností automatického škálování. Tato šablona je dostupná taky pro nasazení jedním kliknutím z galerie šablon pro rychlý Start Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Nasazení do Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-gremlin-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-gremlin-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-db-account-for-gremlin-with-standard-provisioned-throughput"></a>Účet Azure Cosmos DB pro Gremlin se standardní zřízenou propustností

Tato šablona vytvoří účet Azure Cosmos pro rozhraní Gremlin API s databází a grafem se standardní (ruční) propustností. Tato šablona je dostupná taky pro nasazení jedním kliknutím z galerie šablon pro rychlý Start Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Nasazení do Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-gremlin%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-gremlin/azuredeploy.json":::

## <a name="next-steps"></a>Další kroky

Tady je několik dalších zdrojů:

* [Dokumentace ke službě Azure Resource Manager](/azure/azure-resource-manager/)
* [Azure Cosmos DB schéma poskytovatele prostředků](/azure/templates/microsoft.documentdb/allversions)
* [Šablony pro rychlý Start Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Řešení běžných chyb při nasazení Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
