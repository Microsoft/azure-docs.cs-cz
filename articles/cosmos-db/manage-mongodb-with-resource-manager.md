---
title: Šablony Azure Resource Manageru pro rozhraní API služby Azure Cosmos DB pro MongoDB
description: Pomocí šablon Azure Resource Manageru k vytvoření a konfigurace rozhraní API služby Azure Cosmos DB pro MongoDB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: bcf9e0492e58de79efbb16f9ee13adbd0f27b572
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077767"
---
# <a name="create-azure-cosmos-db-api-for-mongodb-resources-from-a-resource-manager-template"></a>Vytvoření rozhraní API služby Azure Cosmos DB pro MongoDB prostředky z šablony Resource Manageru

Informace o vytvoření Azure Cosmos DB API pro MongoDB prostředky pomocí šablony Azure Resource Manageru. Následující příklad vytvoří účet služby Azure Cosmos DB pro rozhraní MongoDB API z [šablona Azure Quickstart](https://aka.ms/mongodb-arm-qs). Tato šablona vytvoří účet Azure Cosmos pro rozhraní API MongoDB s dvě kolekce, které sdílejí propustnost 400 RU/s na úrovni databáze.

Tady je kopie šablony:

[!code-json[create-cosmos-mongo](~/quickstart-templates/101-cosmosdb-mongodb/azuredeploy.json)]

## <a name="deploy-via-azure-cli"></a>Nasazení prostřednictvím rozhraní příkazového řádku Azure

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

V předchozím příkladu neodkazujete šablonu, která je uložena v Githubu. Můžete také stáhnout šablonu do místního počítače nebo vytvořit novou šablonu a zadejte do místní cesty `--template-file` parametru.

## <a name="next-steps"></a>Další kroky

Tady jsou některé další prostředky:

- [Dokumentace ke službě Azure Resource Manageru](/azure/azure-resource-manager/)
- [Schéma poskytovatele prostředků služby Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions)
- [Šablony služby Azure Cosmos DB Quickstart](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Řešení potíží s běžnými chybami nasazení Azure Resource Manageru](../azure-resource-manager/resource-manager-common-deployment-errors.md)