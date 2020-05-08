---
title: Šablony Správce prostředků pro Azure Cosmos DB rozhraní API pro tabulky
description: Pomocí Azure Resource Manager šablon můžete vytvořit a nakonfigurovat Azure Cosmos DB rozhraní API pro tabulky.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mjbrown
ms.openlocfilehash: d1675e6827f3684785d11ef6b081f166267a8283
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791183"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Správa prostředků Azure Cosmos DB rozhraní API pro tabulky pomocí šablon Azure Resource Manager

V tomto článku se dozvíte, jak pomocí šablon Azure Resource Manager pomáhat s nasazením a správou vašich Azure Cosmos DBch účtů, databází a kontejnerů.

Tento článek obsahuje příklady jenom pro účty rozhraní API pro tabulky. Pokud chcete najít příklady pro jiné účty typu rozhraní API, přečtěte si téma: použití Azure Resource Manager šablon s rozhraním API Azure Cosmos DB pro [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)a články [SQL](manage-sql-with-resource-manager.md) .

> [!IMPORTANT]
>
> * Názvy účtů jsou omezené na 44 znaků, a to vše malými písmeny.
> * Chcete-li změnit hodnoty propustnosti, šablonu znovu nasaďte s aktualizovanými RU/s.
> * Když přidáváte nebo odebíráte umístění účtu Azure Cosmos, nemůžete současně upravovat ostatní vlastnosti. Tyto operace se musí provádět samostatně.

Chcete-li vytvořit některý z níže uvedených prostředků Azure Cosmos DB, zkopírujte následující příklad šablony do nového souboru JSON. Volitelně můžete vytvořit parametry souboru JSON pro použití při nasazování více instancí stejného prostředku s různými názvy a hodnotami. Existuje mnoho způsobů, jak nasadit šablony Azure Resource Manager včetně [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) a [GitHubu](../azure-resource-manager/templates/deploy-to-azure-button.md).

> [!TIP]
> Pokud chcete povolit sdílenou propustnost při použití rozhraní API pro tabulky, povolte propustnost na úrovni účtu v Azure Portal.

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-table-with-autoscale-throughput"></a>Účet Azure Cosmos pro tabulku s propustností automatického škálování

Tato šablona vytvoří účet Azure Cosmos pro rozhraní API pro tabulky s jednou tabulkou s propustností automatického škálování. Tato šablona je dostupná taky pro nasazení jedním kliknutím z galerie šablon pro rychlý Start Azure.

[![Nasazení do Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-table-with-standard-manual-throughput"></a>Účet Azure Cosmos pro tabulku se standardní (ruční) propustností

Tato šablona vytvoří účet Azure Cosmos pro rozhraní API pro tabulky s jednou tabulkou se standardní propustností. Tato šablona je dostupná taky pro nasazení jedním kliknutím z galerie šablon pro rychlý Start Azure.

[![Nasazení do Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

## <a name="next-steps"></a>Další kroky

Tady je několik dalších zdrojů:

* [Dokumentace k Azure Resource Manager](/azure/azure-resource-manager/)
* [Azure Cosmos DB schéma poskytovatele prostředků](/azure/templates/microsoft.documentdb/allversions)
* [Šablony pro rychlý Start Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Řešení běžných chyb při nasazení Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
