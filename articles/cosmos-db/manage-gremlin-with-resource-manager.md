---
title: Šablony Správce prostředků pro rozhraní API Azure Cosmos DB Gremlin
description: K vytvoření a konfiguraci rozhraní API Azure Cosmos DB Gremlin použijte šablony Azure Resource Manageru.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: e4c18d93f07cb2143dcc5bf9c93b9ac7298d2f7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246744"
---
# <a name="manage-azure-cosmos-db-gremlin-api-resources-using-azure-resource-manager-templates"></a>Správa prostředků rozhraní API Azure Cosmos DB Gremlin pomocí šablon Azure Resource Manager

Tento článek popisuje, jak provádět různé operace k automatizaci správy účtů Azure Cosmos DB, databází a kontejnerů pomocí šablon Azure Resource Manager. Tento článek obsahuje příklady pouze pro účty Gremlin API, chcete-li najít příklady pro jiné účty typu rozhraní API, viz: použití šablon Azure Resource Manager s rozhraním API Azure Cosmos DB pro [Cassandra](manage-cassandra-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), [Tabulka](manage-table-with-resource-manager.md) články.

## <a name="create-azure-cosmos-db-api-for-mongodb-account-database-and-collection"></a>Vytvoření rozhraní API Azure Cosmos DB pro účet MongoDB, databázi a kolekci<a id="create-resource"></a>

Vytvořte prostředky Azure Cosmos DB pomocí šablony Azure Resource Manager. Tato šablona vytvoří účet Azure Cosmos pro rozhraní GREMLIN API se dvěma grafy, které sdílejí propustnost 400 RU/s na úrovni databáze. Zkopírujte šablonu a nasaďte, jak je znázorněno níže, nebo navštivte [Azure QuickStart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-gremlin/) a nasaďte je z webu Azure Portal. Šablonu můžete také stáhnout do místního počítače nebo vytvořit novou `--template-file` šablonu a zadat místní cestu s parametrem.

> [!NOTE]
> Názvy účtů musí být malá a 44 nebo méně znaků.
> Chcete-li aktualizovat ru/s, odešlete šablonu s aktualizovanými hodnotami vlastností propustnosti.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-gremlin/azuredeploy.json":::

## <a name="deploy-with-the-azure-cli"></a>Nasazení pomocí azure cli

Pokud chcete nasadit šablonu Azure Resource Manager pomocí azure CLI, **zkopírujte** skript a vyberte **Vyzkoušet,** chcete-li otevřít Azure Cloud Shell. Chcete-li skript vložit, klepněte pravým tlačítkem myši na prostředí a pak vyberte **Vložit**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first graph name: ' graph1Name
read -p 'Enter the second graph name: ' graph2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-gremlin/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   graph1Name=$graph1Name graph2Name=$graph2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Příkaz `az cosmosdb show` zobrazuje nově vytvořený účet Azure Cosmos po jeho zřízení. Pokud se rozhodnete použít místně nainstalovanou verzi azure cli namísto použití cloudového prostředí, najdete v článku [Azure CLI.](/cli/azure/)

## <a name="next-steps"></a>Další kroky

Tady je několik dalších zdrojů:

- [Dokumentace správce prostředků Azure](/azure/azure-resource-manager/)
- [Schéma zprostředkovatele prostředků Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Šablony rychlých startů Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Poradce při potížích s běžnými chybami nasazení Azure Resource Manageru](../azure-resource-manager/templates/common-deployment-errors.md)