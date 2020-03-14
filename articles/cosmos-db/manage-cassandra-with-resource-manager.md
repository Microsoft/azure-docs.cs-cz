---
title: Šablony Správce prostředků pro Azure Cosmos DB rozhraní API Cassandra
description: Pomocí Azure Resource Manager šablon můžete vytvořit a nakonfigurovat Azure Cosmos DB rozhraní API Cassandra.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: thvankra
ms.openlocfilehash: c4dc97453fe50865db74f8918ef3dffdb4013b4f
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251892"
---
# <a name="manage-azure-cosmos-db-cassandra-api-resources-using-azure-resource-manager-templates"></a>Správa prostředků Azure Cosmos DB rozhraní API Cassandra pomocí šablon Azure Resource Manager

Tento článek popisuje, jak provádět různé operace pro automatizaci správy Azure Cosmos DB účtů, databází a kontejnerů pomocí šablon Azure Resource Manager. Tento článek obsahuje příklady jenom pro účty rozhraní SQL API. příklady pro jiné účty typu rozhraní API najdete v tématu: použití Azure Resource Manager šablon s rozhraním API Azure Cosmos DB pro [SQL](manage-sql-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)a [tabulkové](manage-table-with-resource-manager.md) články.

## Vytvoření účtu Azure Cosmos, místa a tabulky<a id="create-resource"></a>

Vytvořte Azure Cosmos DB prostředky pomocí šablony Azure Resource Manager. Tato šablona vytvoří účet Azure Cosmos pro rozhraní API Cassandra se dvěma tabulkami, které sdílejí propustnost 400 RU/s na úrovni prostoru. Zkopírujte šablonu a nasazení, jak je znázorněno níže, nebo navštivte [galerii Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-cosmosdb-cassandra/) a nasaďte z Azure Portal. Můžete také stáhnout šablonu do místního počítače nebo vytvořit novou šablonu a zadat místní cestu s parametrem `--template-file`.

> [!NOTE]
> Názvy účtů musí být malé a 44 nebo méně znaků.
> Chcete-li aktualizovat RU/s, šablonu znovu odešlete s aktualizovanými hodnotami vlastností propustnosti.

:::code language="json" source="~/quickstart-templates/101-cosmosdb-cassandra/azuredeploy.json":::

## <a name="deploy-with-the-azure-cli"></a>Nasazení pomocí Azure CLI

Pokud chcete nasadit šablonu Azure Resource Manager pomocí rozhraní příkazového řádku Azure, **zkopírujte** skript a vyberte **zkusit,** aby se otevřela Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**:

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

Příkaz `az cosmosdb show` zobrazuje nově vytvořený účet Azure Cosmos po zřízení. Pokud se rozhodnete použít místně nainstalovanou verzi rozhraní příkazového řádku Azure CLI místo používání služby Cloud Shell, přečtěte si článek [Azure CLI](/cli/azure/) .


## <a name="next-steps"></a>Další kroky

Tady je několik dalších prostředků:

- [Dokumentace k Azure Resource Manager](/azure/azure-resource-manager/)
- [Azure Cosmos DB schéma poskytovatele prostředků](/azure/templates/microsoft.documentdb/allversions)
- [Šablony pro rychlý Start Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Řešení běžných chyb při nasazení Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
