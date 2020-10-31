---
title: Šablony Správce prostředků pro Azure Cosmos DB rozhraní API pro tabulky
description: Pomocí Azure Resource Manager šablon můžete vytvořit a nakonfigurovat Azure Cosmos DB rozhraní API pro tabulky.
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mjbrown
ms.openlocfilehash: 41f3a4fe5afad1f993d91af83c7da27ac28e3759
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93101064"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Správa prostředků Azure Cosmos DB rozhraní API pro tabulky pomocí šablon Azure Resource Manager
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

V tomto článku se dozvíte, jak pomocí šablon Azure Resource Manager pomáhat s nasazením a správou vašich Azure Cosmos DBch účtů, databází a kontejnerů.

Tento článek obsahuje příklady jenom pro účty rozhraní API pro tabulky. Pokud chcete najít příklady pro jiné účty typu rozhraní API, přečtěte si téma: použití Azure Resource Manager šablon s rozhraním API Azure Cosmos DB pro [Cassandra](./templates-samples-cassandra.md), [Gremlin](./templates-samples-gremlin.md), [MongoDB](./templates-samples-mongodb.md)a články [SQL](./manage-with-templates.md) .

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

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Nasazení do Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-table-with-standard-provisioned-throughput"></a>Účet Azure Cosmos pro tabulku se standardní zřízenou propustností

Tato šablona vytvoří účet Azure Cosmos pro rozhraní API pro tabulky s jednou tabulkou se standardní propustností. Tato šablona je dostupná taky pro nasazení jedním kliknutím z galerie šablon pro rychlý Start Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Nasazení do Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-table%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-table/azuredeploy.json":::

## <a name="next-steps"></a>Další kroky

Tady je několik dalších zdrojů:

* [Dokumentace ke službě Azure Resource Manager](../azure-resource-manager/index.yml)
* [Azure Cosmos DB schéma poskytovatele prostředků](/azure/templates/microsoft.documentdb/allversions)
* [Šablony pro rychlý Start Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [Řešení běžných chyb při nasazení Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)