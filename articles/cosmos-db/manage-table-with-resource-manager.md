---
title: Šablony Správce prostředků pro Azure Cosmos DB rozhraní API pro tabulky
description: Pomocí Azure Resource Manager šablon můžete vytvořit a nakonfigurovat Azure Cosmos DB rozhraní API pro tabulky.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mjbrown
ms.openlocfilehash: 86c7ba53c60a27e3d2557859189148785ae6d0f3
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82200801"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Správa prostředků Azure Cosmos DB rozhraní API pro tabulky pomocí šablon Azure Resource Manager

Tento článek popisuje, jak provádět různé operace pro automatizaci správy Azure Cosmos DB účtů, databází a kontejnerů pomocí šablon Azure Resource Manager. Tento článek obsahuje příklady jenom pro účty rozhraní API pro tabulky. Pokud chcete najít příklady pro jiné účty typu rozhraní API, přečtěte si téma: použití Azure Resource Manager šablon s rozhraním API Azure Cosmos DB pro [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)a články [SQL](manage-sql-with-resource-manager.md) .

## <a name="create-azure-cosmos-account-and-table"></a>Vytvoření účtu a tabulky Azure Cosmos<a id="create-resource"></a>

Vytvořte Azure Cosmos DB prostředky pomocí šablony Azure Resource Manager. Tato šablona vytvoří účet Azure Cosmos pro rozhraní API pro tabulky s jednou tabulkou propustnosti 400 RU/s. Zkopírujte šablonu a nasazení, jak je znázorněno níže, nebo navštivte [galerii Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) a nasaďte z Azure Portal. Můžete také stáhnout šablonu do místního počítače nebo vytvořit novou šablonu a zadat místní cestu s `--template-file` parametrem.

> [!NOTE]
> Názvy účtů musí být malé a 44 nebo méně znaků.
> Pokud chcete aktualizovat RU/s, znovu nasaďte šablonu s aktualizovanými hodnotami vlastností propustnosti.

```json
{
"$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": {
   "accountName": {
      "type": "string",
      "defaultValue": "",
      "metadata": {
         "description": "Cosmos DB account name"
      }
   },
   "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]",
      "metadata": {
         "description": "Location for the Cosmos DB account."
      }
   },
   "primaryRegion":{
      "type":"string",
      "metadata": {
         "description": "The primary replica region for the Cosmos DB account."
      }
   },
   "secondaryRegion":{
      "type":"string",
      "metadata": {
        "description": "The secondary replica region for the Cosmos DB account."
     }
   },
   "defaultConsistencyLevel": {
      "type": "string",
      "defaultValue": "Session",
      "allowedValues": [ "Eventual", "ConsistentPrefix", "Session", "BoundedStaleness", "Strong" ],
      "metadata": {
         "description": "The default consistency level of the Cosmos DB account."
      }
   },
   "maxStalenessPrefix": {
      "type": "int",
      "defaultValue": 100000,
      "minValue": 10,
      "maxValue": 1000000,
      "metadata": {
         "description": "Max stale requests. Required for BoundedStaleness. Valid ranges, Single Region: 10 to 1000000. Multi Region: 100000 to 1000000."
      }
   },
   "maxIntervalInSeconds": {
      "type": "int",
      "defaultValue": 300,
      "minValue": 5,
      "maxValue": 86400,
      "metadata": {
         "description": "Max lag time (seconds). Required for BoundedStaleness. Valid ranges, Single Region: 5 to 84600. Multi Region: 300 to 86400."
      }
   },
   "automaticFailover": {
      "type": "bool",
      "defaultValue": true,
      "allowedValues": [ true, false ],
      "metadata": {
         "description": "Enable automatic failover for regions. Ignored when Multi-Master is enabled"
      }
   },
   "tableName": {
      "type": "string",
      "metadata": {
         "description": "The name for the table"
      }
   },
   "throughput": {
      "type": "int",
      "defaultValue": 400,
      "minValue": 400,
      "maxValue": 1000000,
      "metadata": {
         "description": "The throughput for the table"
      }
   }
},
"variables": {
   "accountName": "[toLower(parameters('accountName'))]",
   "consistencyPolicy": {
      "Eventual": {
         "defaultConsistencyLevel": "Eventual"
      },
      "ConsistentPrefix": {
         "defaultConsistencyLevel": "ConsistentPrefix"
      },
      "Session": {
         "defaultConsistencyLevel": "Session"
      },
      "BoundedStaleness": {
         "defaultConsistencyLevel": "BoundedStaleness",
         "maxStalenessPrefix": "[parameters('maxStalenessPrefix')]",
         "maxIntervalInSeconds": "[parameters('maxIntervalInSeconds')]"
      },
      "Strong": {
         "defaultConsistencyLevel": "Strong"
      }
   },
   "locations":
   [
      {
         "locationName": "[parameters('primaryRegion')]",
         "failoverPriority": 0,
         "isZoneRedundant": false
      },
      {
         "locationName": "[parameters('secondaryRegion')]",
         "failoverPriority": 1,
         "isZoneRedundant": false
      }
   ]
},
"resources": 
[
   {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2020-03-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
         "capabilities": [{ "name": "EnableTable" }],
         "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
         "locations": "[variables('locations')]",
         "databaseAccountOfferType": "Standard",
         "enableAutomaticFailover": "[parameters('automaticFailover')]"
      }
   },
   {
      "type": "Microsoft.DocumentDB/databaseAccounts/tables",
      "name": "[concat(variables('accountName'), '/', parameters('tableName'))]",
      "apiVersion": "2020-03-01",
      "dependsOn": [ "[resourceId('Microsoft.DocumentDB/databaseAccounts/', variables('accountName'))]" ],
      "properties":{
         "resource":{
            "id": "[parameters('tableName')]"
         },
         "options": { "throughput": "[parameters('throughput')]" }
      }
   }
]
}
```

### <a name="deploy-via-powershell"></a>Nasazení prostřednictvím PowerShellu

Pokud chcete šablonu Správce prostředků nasadit pomocí prostředí PowerShell, **zkopírujte** skript a vyberte **zkusit,** aby se otevřela Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$tableName = Read-Host -Prompt "Enter the table name"
$throughput = Read-Host -Prompt "Enter the throughput"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json" `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -tableName $tableName `
    -throughput $throughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2020-03-01" --ResourceGroupName $resourceGroupName).name
```

Pokud se rozhodnete použít místně nainstalovanou verzi PowerShellu místo z Azure Cloud Shell, je nutné [nainstalovat](/powershell/azure/install-az-ps) modul Azure PowerShell. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`.

### <a name="deploy-via-the-azure-cli"></a>Nasazení prostřednictvím rozhraní příkazového řádku Azure

Pokud chcete nasadit šablonu Azure Resource Manager pomocí rozhraní příkazového řádku Azure, **zkopírujte** skript a vyberte **zkusit,** aby se otevřela Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the table name: ' tableName
read -p 'Enter the throughput: ' throughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion \
     tableName=$tableName throughput=$throughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` Příkaz zobrazí nově vytvořený účet Azure Cosmos po zřízení. Pokud se rozhodnete použít místně nainstalovanou verzi rozhraní příkazového řádku Azure CLI místo používání služby Cloud Shell, přečtěte si článek [Azure CLI](/cli/azure/) .

## <a name="next-steps"></a>Další kroky

Tady je několik dalších zdrojů:

- [Dokumentace k Azure Resource Manager](/azure/azure-resource-manager/)
- [Azure Cosmos DB schéma poskytovatele prostředků](/azure/templates/microsoft.documentdb/allversions)
- [Šablony pro rychlý Start Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Řešení běžných chyb při nasazení Azure Resource Manager](../azure-resource-manager/templates/common-deployment-errors.md)
