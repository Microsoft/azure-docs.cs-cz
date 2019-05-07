---
title: Šablony Azure Resource Manageru pro službu Azure Cosmos DB Table API
description: Vytvoření a konfigurace Azure Cosmos DB Table API pomocí šablony Azure Resource Manageru.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: mjbrown
ms.openlocfilehash: 33e47d67365e76142d5b584d49d8e7265445bf03
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077602"
---
# <a name="create-azure-cosmos-db-table-api-resources-from-a-resource-manager-template"></a>Vytvoření prostředků Azure Cosmos DB Table API pomocí šablony Resource Manageru

Zjistěte, jak vytvořit prostředky Azure Cosmos DB Table API pomocí šablony Azure Resource Manageru. Následující příklad vytvoří Azure Cosmos DB Table API z [šablona Azure Quickstart](https://aka.ms/table-arm-qs). Tato šablona vytvoří účet Azure Cosmos pro rozhraní Table API s jednou tabulkou s propustnost 400 RU/s.

Tady je kopie šablony:

[!code-json[create-cosmos-table](~/quickstart-templates/101-cosmosdb-table/azuredeploy.json)]

## <a name="deploy-via-powershell"></a>Nasazení přes PowerShell

K nasazení šablony Resource Manageru pomocí Powershellu, **kopírování** skriptu a vyberte **vyzkoušet** a otevřete Azure Cloud shell. Vložte skript, klikněte pravým tlačítkem na prostředí a pak vyberte **vložte**:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$tableName = Read-Host -Prompt "Enter the table name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json" `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -tableName $tableName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Pokud se rozhodnete použít místně nainstalovanou verzi Powershellu místo ze služby Azure Cloud shell, budete muset [nainstalovat](/powershell/azure/install-az-ps) modulu Azure PowerShell. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`. 

V předchozím příkladu neodkazujete šablonu, která je uložena v Githubu. Můžete také stáhnout šablonu do místního počítače nebo vytvořit novou šablonu a zadejte do místní cesty `--template-file` parametru.


## <a name="deploy-via-azure-cli"></a>Nasazení prostřednictvím rozhraní příkazového řádku Azure

K nasazení šablony Resource Manageru pomocí rozhraní příkazového řádku Azure, **kopírování** skriptu a vyberte **vyzkoušet** a otevřete Azure Cloud shell. Vložte skript, klikněte pravým tlačítkem na prostředí a pak vyberte **vložte**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the table name: ' tableName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion tableName=$tableName

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