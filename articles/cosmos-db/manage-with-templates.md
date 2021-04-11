---
title: Vytvoření a Správa Azure Cosmos DB s využitím šablon Správce prostředků
description: Použití šablon Azure Resource Manager k vytvoření a konfiguraci Azure Cosmos DB rozhraní API pro jádro (SQL)
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 03/24/2021
ms.author: mjbrown
ms.openlocfilehash: dd70e196180c68d6a498d147493411e6d1703e59
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105034040"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-azure-resource-manager-templates"></a>Správa prostředků Azure Cosmos DB Core (SQL) API pomocí šablon Azure Resource Manageru

[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

V tomto článku se dozvíte, jak pomocí šablon Azure Resource Manager pomáhat s nasazením a správou vašich Azure Cosmos DBch účtů, databází a kontejnerů.

Tento článek ukazuje jenom Azure Resource Manager příklady šablon pro účty rozhraní API Core (SQL). Můžete také najít příklady šablon pro rozhraní API pro [Cassandra](templates-samples-cassandra.md), [Gremlin](templates-samples-gremlin.md), [MongoDB](templates-samples-mongodb.md)a [Table](templates-samples-table.md) .

> [!IMPORTANT]
>
> * Názvy účtů jsou omezené na 44 znaků, a to vše malými písmeny.
> * Chcete-li změnit hodnoty propustnosti, šablonu znovu nasaďte s aktualizovanými RU/s.
> * Když přidáváte nebo odebíráte umístění účtu Azure Cosmos, nemůžete současně upravovat ostatní vlastnosti. Tyto operace se musí provádět samostatně.
> * Prostředky Azure Cosmos DB nelze přejmenovat, protože jsou v rozporu s tím, jak Azure Resource Manager pracuje s identifikátory URI prostředků.

Chcete-li vytvořit některý z níže uvedených prostředků Azure Cosmos DB, zkopírujte následující příklad šablony do nového souboru JSON. Volitelně můžete vytvořit parametry souboru JSON pro použití při nasazování více instancí stejného prostředku s různými názvy a hodnotami. Existuje mnoho způsobů, jak nasadit šablony Azure Resource Manager včetně [Azure Portal](../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md) a [GitHubu](../azure-resource-manager/templates/deploy-to-azure-button.md).

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>Účet Azure Cosmos s propustností automatického škálování

Tato šablona vytvoří účet Azure Cosmos ve dvou oblastech s možnostmi pro zajištění konzistence a převzetí služeb při selhání s databází a kontejnerem nakonfigurovaným pro propustnost automatického škálování s povolenými možnostmi většiny zásad. Tato šablona je dostupná taky pro nasazení jedním kliknutím z galerie šablon pro rychlý Start Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Nasazení do Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-autoscale/azuredeploy.json":::

<a id="create-analytical-store"></a>

## <a name="azure-cosmos-account-with-analytical-store"></a>Účet Azure Cosmos s analytickým úložištěm

Tato šablona vytvoří účet Azure Cosmos v jedné oblasti s kontejnerem s povoleným analytickým standardem TTL a možnostmi ručního škálování nebo propustnosti automatického škálování. Tato šablona je dostupná taky pro nasazení jedním kliknutím z galerie šablon pro rychlý Start Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Nasazení do Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-analytical-store%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-analytical-store/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-provisioned-throughput"></a>Účet Azure Cosmos se standardní zřízenou propustností

Tato šablona vytvoří účet Azure Cosmos ve dvou oblastech s možnostmi pro zajištění konzistence a převzetí služeb při selhání s databází a kontejnerem nakonfigurovaným pro standardní propustnost, u kterých je povolená většina možností zásad. Tato šablona je dostupná taky pro nasazení jedním kliknutím z galerie šablon pro rychlý Start Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Nasazení do Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>Kontejner Azure Cosmos DB s funkcí na straně serveru

Tato šablona vytvoří účet Azure Cosmos, databázi a kontejner s uloženou procedurou, triggerem a uživatelsky definovanou funkcí. Tato šablona je dostupná taky pro nasazení jedním kliknutím z galerie šablon pro rychlý Start Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Nasazení do Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-container-sprocs%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json":::

<a id="create-rbac"></a>

## <a name="azure-cosmos-db-account-with-azure-ad-and-rbac"></a>Účet Azure Cosmos DB s využitím Azure AD a RBAC

Tato šablona vytvoří účet SQL Cosmos, nativně udržovanou definici role a nativně udržuje přiřazení role pro identitu AAD. Tato šablona je dostupná taky pro nasazení jedním kliknutím z galerie šablon pro rychlý Start Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Nasazení do Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-sql-rbac%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-sql-rbac/azuredeploy.json":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>Účet Azure Cosmos DB úrovně Free

Tato šablona vytvoří bezplatný účet Azure Cosmos a databázi se sdílenou propustností, kterou lze sdílet s až 25 kontejnery. Tato šablona je dostupná taky pro nasazení jedním kliknutím z galerie šablon pro rychlý Start Azure.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Nasazení do Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-free%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/101-cosmosdb-free/azuredeploy.json":::

## <a name="next-steps"></a>Další kroky

Tady je několik dalších zdrojů:

* [Dokumentace ke službě Azure Resource Manager](../azure-resource-manager/index.yml)
* [Azure Cosmos DB schéma poskytovatele prostředků](/azure/templates/microsoft.documentdb/allversions)
* [Šablony pro rychlý Start Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [Řešení běžných chyb při nasazení Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)