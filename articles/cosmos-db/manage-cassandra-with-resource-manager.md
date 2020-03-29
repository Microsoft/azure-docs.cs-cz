---
title: Šablony Správce prostředků pro rozhraní API Azure Cosmos DB Cassandra
description: K vytvoření a konfiguraci rozhraní API Azure Cosmos DB Cassandra použijte šablony Azure Resource Manageru.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: c4dc97453fe50865db74f8918ef3dffdb4013b4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251892"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Správa prostředků rozhraní API Azure Cosmos DB Cassandra pomocí šablon Azure Resource Manager

Tento článek popisuje, jak provádět různé operace k automatizaci správy účtů Azure Cosmos DB, databází a kontejnerů pomocí šablon Azure Resource Manager. Tento článek obsahuje pouze příklady pro účty rozhraní SQL API, kde najdete příklady pro jiné účty typu rozhraní API: Použijte šablony Azure Resource Manager u rozhraní API Azure Cosmos DB pro [SQL](manage-sql-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md), [Table](manage-table-with-resource-manager.md) articles.

## <a name="create-azure-cosmos-account-keyspace-and-table"></a>Vytvoření účtu Azure Cosmos, klíčového prostoru a tabulky<a id="create-resource"></a>

Vytvořte prostředky Azure Cosmos DB pomocí šablony Azure Resource Manager. Tato šablona vytvoří účet Azure Cosmos pro rozhraní API Cassandra se dvěma tabulkami, které sdílejí propustnost 400 RU/s na úrovni keyspace. Zkopírujte šablonu a nasaďte, jak je znázorněno níže, nebo navštivte [Azure QuickStart Gallery](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) a nasaďte je z webu Azure Portal. Šablonu můžete také stáhnout do místního počítače nebo vytvořit novou `--template-file` šablonu a zadat místní cestu s parametrem.

> [!NOTE]
> Názvy účtů musí být malá a 44 nebo méně znaků.
> Chcete-li aktualizovat ru/s, odešlete šablonu s aktualizovanými hodnotami vlastností propustnosti.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="deploy-with-the-azure-cli"></a>Nasazení pomocí azure cli

Pokud chcete nasadit šablonu Azure Resource Manager pomocí azure CLI, **zkopírujte** skript a vyberte **Vyzkoušet,** chcete-li otevřít Azure Cloud Shell. Chcete-li skript vložit, klepněte pravým tlačítkem myši na prostředí a pak vyberte **Vložit**:

```azurecli-interactive

read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the keyset name: ' keysetName
read -p 'Enter the first table name: ' table1Name
read -p 'Enter the second table name: ' table2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-cassandra/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion keysetName=$keysetName \
   table1Name=$table1Name table2Name=$table2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Příkaz `az cosmosdb show` zobrazuje nově vytvořený účet Azure Cosmos po jeho zřízení. Pokud se rozhodnete použít místně nainstalovanou verzi azure cli namísto použití cloudového prostředí, najdete v článku [Azure CLI.](/cli/azure/)


## <a name="next-steps"></a>Další kroky

Tady je několik dalších zdrojů:

- [Dokumentace správce prostředků Azure](/azure/azure-resource-manager/)
- [Schéma zprostředkovatele prostředků Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Šablony rychlých startů Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Poradce při potížích s běžnými chybami nasazení Azure Resource Manageru](../azure-resource-manager/templates/common-deployment-errors.md)
