---
title: Šablony Azure Resource Manager pro Azure Cosmos DB rozhraní API pro tabulky
description: Pomocí Azure Resource Manager šablon můžete vytvořit a nakonfigurovat Azure Cosmos DB rozhraní API pro tabulky.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: mjbrown
ms.openlocfilehash: 4dd636be60233beafca8e8680551bd7c711a4ccc
ms.sourcegitcommit: c8a102b9f76f355556b03b62f3c79dc5e3bae305
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68814872"
---
# <a name="manage-azure-cosmos-db-table-api-resources-using-azure-resource-manager-templates"></a>Správa prostředků Azure Cosmos DB rozhraní API pro tabulky pomocí šablon Azure Resource Manager

## Vytvoření účtu a tabulky Azure Cosmos<a id="create-resource"></a>

Vytvořte Azure Cosmos DB prostředky pomocí šablony Azure Resource Manager. Tato šablona vytvoří účet Azure Cosmos pro rozhraní API pro tabulky s jednou tabulkou propustnosti 400 RU/s. Zkopírujte šablonu a nasazení, jak je znázorněno níže, nebo navštivte [galerii Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-cosmosdb-table/) a nasaďte z Azure Portal. Můžete také stáhnout šablonu do místního počítače nebo vytvořit novou šablonu a zadat místní cestu s `--template-file` parametrem.

> [!NOTE]
> Název účtu musí být malými písmeny a < 31 znaků.

[!code-json[create-cosmos-table](~/quickstart-templates/101-cosmosdb-table/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>Nasazení prostřednictvím PowerShellu

Pokud chcete nasadit šablonu Správce prostředků pomocí prostředí PowerShell, **zkopírujte** skript a vyberte **zkusit** pro otevření služby Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**:

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

Pokud se rozhodnete použít místně nainstalovanou verzi PowerShellu místo z Azure Cloud shellu, musíte [nainstalovat](/powershell/azure/install-az-ps) modul Azure PowerShell. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`.

### <a name="deploy-via-azure-cli"></a>Nasazení prostřednictvím rozhraní příkazového řádku Azure

Pokud chcete nasadit šablonu Správce prostředků pomocí rozhraní příkazového řádku Azure, **zkopírujte** skript a vyberte **zkusit,** aby se otevřelo Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**:

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

`az cosmosdb show` Příkaz zobrazí nově vytvořený účet Azure Cosmos po zřízení. Pokud se rozhodnete použít místně nainstalovanou verzi Azure CLI místo používání Cloudshellu, přečtěte si článek [rozhraní příkazového řádku Azure (CLI)](/cli/azure/) .

## Aktualizace propustnosti (RU/s) v tabulce<a id="table-ru-update"></a>

Následující šablona aktualizuje propustnost tabulky. Zkopírujte šablonu a nasazení, jak je znázorněno níže, nebo navštivte [galerii Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-cosmosdb-table-ru-update/) a nasaďte z Azure Portal. Můžete také stáhnout šablonu do místního počítače nebo vytvořit novou šablonu a zadat místní cestu s `--template-file` parametrem.

[!code-json[cosmosdb-table-ru-update](~/quickstart-templates/101-cosmosdb-table-ru-update/azuredeploy.json)]

### <a name="deploy-table-throughput-via-powershell"></a>Nasazení propustnosti tabulky přes PowerShell

Pokud chcete nasadit šablonu Správce prostředků pomocí prostředí PowerShell, **zkopírujte** skript a vyberte **zkusit** pro otevření služby Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$tableName = Read-Host -Prompt "Enter the table name"
$throughput = Read-Host -Prompt "Enter new throughput for table"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-table-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -tableName $tableName `
    -throughput $throughput
```

### <a name="deploy-table-template-via-azure-cli"></a>Nasazení šablony tabulky přes Azure CLI

Pokud chcete nasadit šablonu Správce prostředků pomocí rozhraní příkazového řádku Azure, vyberte **zkusit** , aby se otevřelo prostředí Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the table name: ' tableName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-table-ru-update/azuredeploy.json \
   --parameters accountName=$accountName tableName=$tableName throughput=$throughput
```

## <a name="next-steps"></a>Další kroky

Tady je několik dalších prostředků:

- [Dokumentace k Azure Resource Manager](/azure/azure-resource-manager/)
- [Azure Cosmos DB schéma poskytovatele prostředků](/azure/templates/microsoft.documentdb/allversions)
- [Šablony pro rychlý Start Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Řešení běžných chyb při nasazení Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)