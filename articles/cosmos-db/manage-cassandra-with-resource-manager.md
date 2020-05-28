---
title: Šablony Správce prostředků pro Azure Cosmos DB rozhraní API Cassandra
description: Pomocí Azure Resource Manager šablon můžete vytvořit a nakonfigurovat Azure Cosmos DB rozhraní API Cassandra.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: 925bad729cacfb7c342c61872f66dd059cf3560a
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117155"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Správa prostředků Azure Cosmos DB rozhraní API Cassandra pomocí šablon Azure Resource Manager

V tomto článku se dozvíte, jak pomocí šablon Azure Resource Manager pomáhat s nasazením a správou vašich Azure Cosmos DBch účtů, prostorů a tabulek.

Tento článek obsahuje příklady jenom pro účty rozhraní API Cassandra. Pokud chcete najít příklady pro jiné účty typu rozhraní API, přečtěte si téma: použití Azure Resource Manager šablon s rozhraním API Azure Cosmos DB pro [SQL](manage-sql-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)a [tabulkové](manage-table-with-resource-manager.md) články.

> [!IMPORTANT]
>
> * Názvy účtů jsou omezené na 44 znaků, a to vše malými písmeny.
> * Chcete-li změnit hodnoty propustnosti, šablonu znovu nasaďte s aktualizovanými RU/s.
> * Když přidáváte nebo odebíráte umístění účtu Azure Cosmos, nemůžete současně upravovat ostatní vlastnosti. Tyto operace se musí provádět samostatně.

Chcete-li vytvořit některý z níže uvedených prostředků Azure Cosmos DB, zkopírujte následující příklad šablony do nového souboru JSON. Volitelně můžete vytvořit parametry souboru JSON pro použití při nasazování více instancí stejného prostředku s různými názvy a hodnotami. Existuje mnoho způsobů, jak nasadit šablony Azure Resource Manager včetně [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) a [GitHubu](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-cassandra-with-autoscale-provisioned-throughput"></a>Účet Azure Cosmos pro Cassandra s zřízenou propustností automatického škálování

Tato šablona vytvoří účet Azure Cosmos ve dvou oblastech s možnostmi pro zajištění konzistence a převzetí služeb při selhání s místem a tabulkou nakonfigurovanou pro propustnost automatického škálování. Tato šablona je dostupná taky pro nasazení jedním kliknutím z galerie šablon pro rychlý Start Azure.

[![Nasazení do Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-cassandra-with-standard-provisioned-throughput"></a>Účet Azure Cosmos pro Cassandra se standardní zřízenou propustností

Tato šablona vytvoří účet Azure Cosmos ve dvou oblastech s možnostmi pro zajištění konzistence a převzetí služeb při selhání s místem a tabulkou nakonfigurovanou pro standardní propustnost. Tato šablona je dostupná taky pro nasazení jedním kliknutím z galerie šablon pro rychlý Start Azure.

[![Nasazení do Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-cassandra%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="next-steps"></a>Další kroky

Tady je několik dalších zdrojů:

* [Dokumentace k Azure Resource Manager](/azure/azure-resource-manager/)
* [Azure Cosmos DB schéma poskytovatele prostředků](/azure/templates/microsoft.documentdb/allversions)
* [Šablony pro rychlý Start Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Řešení běžných chyb při nasazení Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
