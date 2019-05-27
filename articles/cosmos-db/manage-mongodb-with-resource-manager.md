---
title: Šablony Azure Resource Manageru pro rozhraní API služby Azure Cosmos DB pro MongoDB
description: Pomocí šablon Azure Resource Manageru k vytvoření a konfigurace rozhraní API služby Azure Cosmos DB pro MongoDB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: mjbrown
ms.openlocfilehash: 99f1e41107c277c8b3f1b21f81952d5d5cadaa29
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968866"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Správa prostředků rozhraní MongoDB API služby Azure Cosmos DB pomocí šablon Azure Resource Manageru

## Vytvoření rozhraní API služby Azure Cosmos DB pro účet, databázi a kolekci MongoDB <a id="create-resource"></a>

Vytvořte prostředky služby Azure Cosmos DB pomocí šablony Azure Resource Manageru. Tato šablona vytvoří účet Azure Cosmos pro rozhraní API MongoDB s dvě kolekce, které sdílejí propustnost 400 RU/s na úrovni databáze. Zkopírujte šablonu a nasadili, jak je znázorněno níže nebo navštivte [galerii pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb/) a nasadit z webu Azure portal. Můžete také stáhnout šablonu do místního počítače nebo vytvořit novou šablonu a zadejte do místní cesty `--template-file` parametru.

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

### <a name="deploy-via-azure-cli"></a>Nasazení prostřednictvím rozhraní příkazového řádku Azure

K nasazení šablony Resource Manageru pomocí rozhraní příkazového řádku Azure, **kopírování** skriptu a vyberte **vyzkoušet** a otevřete Azure Cloud shell. Vložte skript, klikněte pravým tlačítkem na prostředí a pak vyberte **vložte**:

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

`az cosmosdb show` Příkaz zobrazí nově vytvořeného účtu Azure Cosmos po jeho zřízení. Pokud se rozhodnete použít místně nainstalovanou verzi Azure CLI místo použití cloud shell, přečtěte si téma [rozhraní příkazového řádku Azure (CLI)](/cli/azure/) článku.

## Aktualizovat propustnost (RU/s) na databázi <a id="database-ru-update"></a>

Následující šablony se aktualizuje propustnosti databáze. Zkopírujte šablonu a nasadili, jak je znázorněno níže nebo navštivte [galerii pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-database-ru-update/) a nasadit z webu Azure portal. Můžete také stáhnout šablonu do místního počítače nebo vytvořit novou šablonu a zadejte do místní cesty `--template-file` parametru.

[!code-json[cosmosdb-mongodb-database-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-azure-cli"></a>Nasazení šablony databáze s využitím rozhraní příkazového řádku Azure

Pokud chcete nasadit šablonu Resource Manageru pomocí rozhraní příkazového řádku Azure, vyberte **vyzkoušet** a otevřete Azure Cloud shell. Vložte skript, klikněte pravým tlačítkem na prostředí a pak vyberte **vložte**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## Aktualizovat propustnost (RU/s) na kolekci <a id="collection-ru-update"></a>

Následující šablony se aktualizuje propustnost kolekce. Zkopírujte šablonu a nasadili, jak je znázorněno níže nebo navštivte [galerii pro rychlý start Azure](https://azure.microsoft.com/resources/templates/101-cosmosdb-mongodb-collection-ru-update/) a nasadit z webu Azure portal. Můžete také stáhnout šablonu do místního počítače nebo vytvořit novou šablonu a zadejte do místní cesty `--template-file` parametru.

[!code-json[cosmosdb-mongodb-collection-ru-update](~/quickstart-templates/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json)]

### <a name="deploy-collection-template-via-azure-cli"></a>Nasazení šablony kolekce s využitím rozhraní příkazového řádku Azure

Pokud chcete nasadit šablonu Resource Manageru pomocí rozhraní příkazového řádku Azure, vyberte **vyzkoušet** a otevřete Azure Cloud shell. Vložte skript, klikněte pravým tlačítkem na prostředí a pak vyberte **vložte**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the collection name: ' collectionName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-mongodb-collection-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName collectionName=$collectionName throughput=$throughput
```

## <a name="next-steps"></a>Další kroky

Tady jsou některé další prostředky:

- [Dokumentace ke službě Azure Resource Manageru](/azure/azure-resource-manager/)
- [Schéma poskytovatele prostředků služby Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Šablony služby Azure Cosmos DB Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Řešení potíží s běžnými chybami nasazení Azure Resource Manageru](../azure-resource-manager/resource-manager-common-deployment-errors.md)