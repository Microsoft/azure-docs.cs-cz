---
title: Vytváření a Správa Azure Cosmos DB pomocí šablon Azure Resource Manager
description: Použití šablon Azure Resource Manager k vytvoření a konfiguraci Azure Cosmos DB rozhraní API pro SQL (jádro)
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: mjbrown
ms.openlocfilehash: 4cd66c9da0650c9eb9de5b51ce82b48fe781c6f4
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68500503"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-using-azure-resource-manager-templates"></a>Správa prostředků rozhraní API v Azure Cosmos DB SQL (jádro) pomocí šablon Azure Resource Manager

## Vytvoření účtu Azure Cosmos, databáze a kontejneru<a id="create-resource"></a>

Vytvořte Azure Cosmos DB prostředky pomocí šablony Azure Resource Manager. Tato šablona vytvoří účet Azure Cosmos se dvěma kontejnery, které sdílejí propustnost 400 RU/s na úrovni databáze. Zkopírujte šablonu a nasazení, jak je znázorněno níže, nebo navštivte [galerii Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) a nasaďte z Azure Portal. Můžete také stáhnout šablonu do místního počítače nebo vytvořit novou šablonu a zadat místní cestu s `--template-file` parametrem.

> [!NOTE]
>
> - V současné době nemůžete nasadit uživatelsky definované funkce (UDF), uložené procedury a triggery pomocí šablon Správce prostředků.
> - Nemůžete současně přidat nebo odebrat umístění k účtu Azure Cosmos a upravovat další vlastnosti. Ty je nutné provést jako samostatné operace.

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

### <a name="deploy-via-powershell"></a>Nasazení prostřednictvím PowerShellu

Pokud chcete nasadit šablonu Správce prostředků pomocí prostředí PowerShell, **zkopírujte** skript a vyberte **zkusit** pro otevření služby Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$container1Name = Read-Host -Prompt "Enter the first container name"
$container2Name = Read-Host -Prompt "Enter the second container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -container1Name $container1Name `
    -container2Name $container2Name

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2015-04-08" --ResourceGroupName $resourceGroupName).name
```

Pokud se rozhodnete použít místně nainstalovanou verzi PowerShellu místo z Azure Cloud shellu, musíte [nainstalovat](/powershell/azure/install-az-ps) modul Azure PowerShell. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`.

### <a name="deploy-via-azure-cli"></a>Nasazení prostřednictvím rozhraní příkazového řádku Azure

Pokud chcete nasadit šablonu Správce prostředků pomocí rozhraní příkazového řádku Azure, vyberte **zkusit** , aby se otevřelo prostředí Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the first container name: ' container1Name
read -p 'Enter the second container name: ' container2Name

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   container1Name=$container1Name container2Name=$container2Name

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

`az cosmosdb show` Příkaz zobrazí nově vytvořený účet Azure Cosmos po zřízení. Pokud se rozhodnete použít místně nainstalovanou verzi Azure CLI místo používání Cloudshellu, přečtěte si článek [rozhraní příkazového řádku Azure (CLI)](/cli/azure/) .

## Aktualizace propustnosti (RU/s) v databázi<a id="database-ru-update"></a>

Následující šablona aktualizuje propustnost databáze. Zkopírujte šablonu a nasazení, jak je znázorněno níže, nebo navštivte [galerii Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-database-ru-update/) a nasaďte z Azure Portal. Můžete také stáhnout šablonu do místního počítače nebo vytvořit novou šablonu a zadat místní cestu s `--template-file` parametrem.

[!code-json[cosmosdb-sql-database-ru-update](~/quickstart-templates/101-cosmosdb-sql-database-ru-update/azuredeploy.json)]

### <a name="deploy-database-template-via-powershell"></a>Nasazení šablony databáze prostřednictvím PowerShellu

Pokud chcete nasadit šablonu Správce prostředků pomocí prostředí PowerShell, **zkopírujte** skript a vyberte **zkusit** pro otevření služby Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$databaseName = Read-Host -Prompt "Enter the database name"
$throughput = Read-Host -Prompt "Enter new throughput for database"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-database-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -databaseName $databaseName `
    -throughput $throughput
```

### <a name="deploy-database-template-via-azure-cli"></a>Nasazení šablony databáze prostřednictvím rozhraní příkazového řádku Azure

Pokud chcete nasadit šablonu Správce prostředků pomocí rozhraní příkazového řádku Azure, vyberte **zkusit** , aby se otevřelo prostředí Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-database-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName throughput=$throughput
```

## Aktualizace propustnosti (RU/s) na kontejneru<a id="container-ru-update"></a>

Následující šablona aktualizuje propustnost kontejneru. Zkopírujte šablonu a nasazení, jak je znázorněno níže, nebo navštivte [galerii Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-ru-update/) a nasaďte z Azure Portal. Můžete také stáhnout šablonu do místního počítače nebo vytvořit novou šablonu a zadat místní cestu s `--template-file` parametrem.

[!code-json[cosmosdb-sql-container-ru-update](~/quickstart-templates/101-cosmosdb-sql-container-ru-update/azuredeploy.json)]

### <a name="deploy-container-template-via-powershell"></a>Nasazení šablony kontejneru přes PowerShell

Pokud chcete nasadit šablonu Správce prostředků pomocí prostředí PowerShell, **zkopírujte** skript a vyberte **zkusit** pro otevření služby Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"
$throughput = Read-Host -Prompt "Enter new throughput for container"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-ru-update/azuredeploy.json" `
    -accountName $accountName `
    -databaseName $databaseName `
    -containerName $containerName `
    -throughput $throughput
```

### <a name="deploy-container-template-via-azure-cli"></a>Nasazení šablony kontejneru přes Azure CLI

Pokud chcete nasadit šablonu Správce prostředků pomocí rozhraní příkazového řádku Azure, vyberte **zkusit** , aby se otevřelo prostředí Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the account name: ' accountName
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName
read -p 'Enter the new throughput: ' throughput

az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-ru-update/azuredeploy.json \
   --parameters accountName=$accountName databaseName=$databaseName containerName=$containerName throughput=$throughput
```

## <a name="next-steps"></a>Další kroky

Tady je několik dalších prostředků:

- [Dokumentace k Azure Resource Manager](/azure/azure-resource-manager/)
- [Azure Cosmos DB schéma poskytovatele prostředků](/azure/templates/microsoft.documentdb/allversions)
- [Šablony pro rychlý Start Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Řešení běžných chyb při nasazení Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)