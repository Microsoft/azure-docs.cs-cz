---
title: Šablony Správce prostředků pro rozhraní API DB Azure Cosmos pro MongoDB
description: Pomocí šablon Azure Resource Manager vytvořte a nakonfigurujte rozhraní API Azure Cosmos DB pro MongoDB.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: 531f122679c463b11c84eba2fca9f30b09e0935f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063641"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Správa prostředků rozhraní API Azure Cosmos DB MongoDB pomocí šablon Azure Resource Manager

Tento článek popisuje, jak provádět různé operace k automatizaci správy účtů Azure Cosmos DB, databází a kontejnerů pomocí šablon Azure Resource Manager. Tento článek obsahuje příklady rozhraní API Azure Cosmos DB pouze pro MongoDB, chcete-li najít příklady pro jiné účty typu rozhraní API, viz: použití šablon Azure Resource Manager s rozhraním API Azure Cosmos DB pro [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [SQL](manage-sql-with-resource-manager.md), [Table](manage-table-with-resource-manager.md) articles.

## <a name="create-azure-cosmos-db-api-for-mongodb-account-database-and-collection"></a>Vytvoření rozhraní API Azure Cosmos DB pro účet MongoDB, databázi a kolekci<a id="create-resource"></a>

Vytvořte prostředky Azure Cosmos DB pomocí šablony Azure Resource Manager. Tato šablona vytvoří účet Azure Cosmos pro rozhraní API MongoDB se dvěma kolekcemi, které sdílejí propustnost 400 RU/s na úrovni databáze. Zkopírujte šablonu a nasaďte, jak je znázorněno níže, nebo navštivte [Azure QuickStart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) a nasaďte je z webu Azure Portal. Šablonu můžete také stáhnout do místního počítače nebo vytvořit novou `--template-file` šablonu a zadat místní cestu s parametrem.

> [!NOTE]
> Názvy účtů musí být malá a 44 nebo méně znaků.
> Chcete-li aktualizovat ru/s, odešlete šablonu s aktualizovanými hodnotami vlastností propustnosti.
>
> V současné době můžete vytvořit jenom verzi 3.2 (to `*.documents.azure.com`znamená účty pomocí koncového bodu ve formátu) rozhraní API Azure Cosmos DB pro účty MongoDB pomocí PowerShellu a CLI. Chcete-li vytvořit verzi účtů 3.6, použijte šablony Správce prostředků (níže) nebo portál Azure.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json":::

### <a name="deploy-via-the-azure-cli"></a>Nasazení pomocí azure cli

Pokud chcete nasadit šablonu Azure Resource Manager pomocí azure CLI, **zkopírujte** skript a vyberte **Vyzkoušet,** chcete-li otevřít Azure Cloud Shell. Chcete-li skript vložit, klepněte pravým tlačítkem myši na prostředí a pak vyberte **Vložit**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first collection name: ' collection1Name
read -p 'Enter the second collection name: ' collection2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
  --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb/azuredeploy.json \
  --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
  databaseName=$databaseName collection1Name=$collection1Name collection2Name=$collection2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Příkaz `az cosmosdb show` zobrazuje nově vytvořený účet Azure Cosmos po jeho zřízení. Pokud se rozhodnete použít místně nainstalovanou verzi azure cli namísto použití cloudového prostředí, najdete v článku [Azure CLI.](/cli/azure/)

## <a name="next-steps"></a>Další kroky

Tady je několik dalších zdrojů:

- [Dokumentace správce prostředků Azure](/azure/azure-resource-manager/)
- [Schéma zprostředkovatele prostředků Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Šablony rychlých startů Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Poradce při potížích s běžnými chybami nasazení Azure Resource Manageru](../azure-resource-manager/templates/common-deployment-errors.md)
