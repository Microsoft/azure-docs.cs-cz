---
title: Vytváření a Správa Azure Cosmos DB pomocí šablon Azure Resource Manager
description: Použití šablon Azure Resource Manager k vytvoření a konfiguraci Azure Cosmos DB rozhraní API pro SQL (jádro)
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 5babcadee02da0ba3e112f75e8b4d1aed5f3339f
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73721072"
---
# <a name="manage-azure-cosmos-db-sql-core-api-resources-using-azure-resource-manager-templates"></a>Správa prostředků rozhraní API v Azure Cosmos DB SQL (jádro) pomocí šablon Azure Resource Manager

Tento článek popisuje, jak provádět různé operace pro automatizaci správy Azure Cosmos DB účtů, databází a kontejnerů pomocí šablon Azure Resource Manager. Tento článek obsahuje příklady jenom pro účty rozhraní SQL API. příklady pro jiné účty typu rozhraní API najdete v tématu: použití Správce prostředků šablon s rozhraním API Azure Cosmos DB pro [Cassandra](manage-cassandra-with-resource-manager.md), [Gremlin](manage-gremlin-with-resource-manager.md), [MongoDB](manage-mongodb-with-resource-manager.md)a [tabulkové](manage-table-with-resource-manager.md) články.

Jak vytvářet a spravovat účty Cosmos DB, databáze a kontejnery pro MongoDB, Gremlin, Cassandra a rozhraní API pro tabulky.

## Vytvoření účtu Azure Cosmos, databáze a kontejneru<a id="create-resource"></a>

Vytvořte Azure Cosmos DB prostředky pomocí šablony Azure Resource Manager. Tato šablona vytvoří účet Azure Cosmos se dvěma kontejnery, které sdílejí propustnost 400 RU/s na úrovni databáze a jeden kontejner s vyhrazenou propustností 400 RU/s. Zkopírujte šablonu a nasazení, jak je znázorněno níže, nebo navštivte [galerii Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql/) a nasaďte z Azure Portal. Můžete také stáhnout šablonu do místního počítače nebo vytvořit novou šablonu a zadat místní cestu s parametrem `--template-file`.

> [!NOTE]
>
> - Nemůžete současně přidat nebo odebrat umístění k účtu Azure Cosmos a upravovat další vlastnosti. Ty je nutné provést jako samostatné operace.
> - Název účtu musí být malými písmeny a < 44 znaků.
> - Chcete-li aktualizovat RU/s, šablonu znovu odešlete s aktualizovanými hodnotami vlastností propustnosti.

[!code-json[create-cosmosdb-sql](~/quickstart-templates/101-cosmosdb-sql/azuredeploy.json)]

> [!NOTE]
> Pokud chcete vytvořit kontejner s velkým klíčem oddílu, uveďte vlastnost `"version":2` v rámci objektu `partitionKey` v předchozí šabloně.

### <a name="deploy-via-powershell"></a>Nasazení prostřednictvím PowerShellu

Pokud chcete nasadit šablonu Správce prostředků pomocí prostředí PowerShell, **zkopírujte** skript a vyberte **zkusit** pro otevření služby Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$sharedThroughput = Read-Host -Prompt "Enter the shared database throughput (i.e. 400)"
$sharedContainer1Name = Read-Host -Prompt "Enter the first shared container name"
$sharedContainer2Name = Read-Host -Prompt "Enter the second shared container name"
$dedicatedContainer1Name = Read-Host -Prompt "Enter the dedicated container name"
$dedicatedThroughput = Read-Host -Prompt "Enter the dedicated container throughput (i.e. 400)"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -sharedThroughput $ $sharedThroughput `
    -sharedContainer1Name $sharedContainer1Name `
    -sharedContainer2Name $sharedContainer2Name `
    -dedicatedContainer1Name $dedicatedContainer1Name `
    -dedicatedThroughput $dedicatedThroughput

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
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
read -p 'Enter the shared database throughput: sharedThroughput
read -p 'Enter the first shared container name: ' sharedContainer1Name
read -p 'Enter the second shared container name: ' sharedContainer2Name
read -p 'Enter the dedicated container name: ' dedicatedContainer1Name
read -p 'Enter the dedicated container throughput: dedicatedThroughput

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql/azuredeploy.json \
   --parameters accountName=$accountName \
   primaryRegion=$primaryRegion \
   secondaryRegion=$secondaryRegion \
   databaseName=$databaseName \
   sharedThroughput=$sharedThroughput \
   sharedContainer1Name=$sharedContainer1Name \
   sharedContainer2Name=$sharedContainer2Name \
   dedicatedContainer1Name=$dedicatedContainer1Name \
   dedicatedThroughput=$dedicatedThroughput

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

Příkaz `az cosmosdb show` zobrazuje nově vytvořený účet Azure Cosmos po zřízení. Pokud se rozhodnete použít místně nainstalovanou verzi Azure CLI místo používání Cloudshellu, přečtěte si článek [rozhraní příkazového řádku Azure (CLI)](/cli/azure/) .

## Vytvoření kontejneru Azure Cosmos DB pomocí funkce na straně serveru<a id="create-sproc"></a>

Vytvořte kontejner Azure Cosmos DB s uloženou procedurou, triggerem a uživatelsky definovanou funkcí pomocí šablony Azure Resource Manager. Zkopírujte šablonu a nasazení, jak je znázorněno níže, nebo navštivte [galerii Azure pro rychlý Start](https://azure.microsoft.com/resources/templates/101-cosmosdb-sql-container-sprocs/) a nasaďte z Azure Portal. Můžete také stáhnout šablonu do místního počítače nebo vytvořit novou šablonu a zadat místní cestu s parametrem `--template-file`.

[!code-json[create-cosmosdb-sql-sprocs](~/quickstart-templates/101-cosmosdb-sql-container-sprocs/azuredeploy.json)]

### <a name="deploy-stored-procedure-template-via-powershell"></a>Nasazení šablony uložené procedury přes PowerShell

Pokud chcete nasadit šablonu Správce prostředků pomocí prostředí PowerShell, **zkopírujte** skript a vyberte **zkusit** pro otevření služby Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**:

```azurepowershell-interactive

$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$accountName = Read-Host -Prompt "Enter the account name"
$location = Read-Host -Prompt "Enter the location (i.e. westus2)"
$primaryRegion = Read-Host -Prompt "Enter the primary region (i.e. westus2)"
$secondaryRegion = Read-Host -Prompt "Enter the secondary region (i.e. eastus2)"
$databaseName = Read-Host -Prompt "Enter the database name"
$containerName = Read-Host -Prompt "Enter the container name"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json" `
    -accountName $accountName `
    -location $location `
    -primaryRegion $primaryRegion `
    -secondaryRegion $secondaryRegion `
    -databaseName $databaseName `
    -containerName $containerName

 (Get-AzResource --ResourceType "Microsoft.DocumentDb/databaseAccounts" --ApiVersion "2019-08-01" --ResourceGroupName $resourceGroupName).name
```

Pokud se rozhodnete použít místně nainstalovanou verzi PowerShellu místo z Azure Cloud shellu, musíte [nainstalovat](/powershell/azure/install-az-ps) modul Azure PowerShell. Verzi zjistíte spuštěním příkazu `Get-Module -ListAvailable Az`.

### <a name="deploy-stored-procedure-template-via-azure-cli"></a>Nasazení šablony uložené procedury přes rozhraní příkazového řádku Azure

Pokud chcete nasadit šablonu Správce prostředků pomocí rozhraní příkazového řádku Azure, vyberte **zkusit** , aby se otevřelo prostředí Azure Cloud Shell. Skript vložíte tak, že kliknete pravým tlačítkem na prostředí a pak vyberete **Vložit**:

```azurecli-interactive
read -p 'Enter the Resource Group name: ' resourceGroupName
read -p 'Enter the location (i.e. westus2): ' location
read -p 'Enter the account name: ' accountName
read -p 'Enter the primary region (i.e. westus2): ' primaryRegion
read -p 'Enter the secondary region (i.e. eastus2): ' secondaryRegion
read -p 'Enter the database name: ' databaseName
read -p 'Enter the container name: ' containerName

az group create --name $resourceGroupName --location $location
az group deployment create --resource-group $resourceGroupName \
   --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/101-cosmosdb-sql-container-sprocs/azuredeploy.json \
   --parameters accountName=$accountName primaryRegion=$primaryRegion secondaryRegion=$secondaryRegion databaseName=$databaseName \
   containerName=$containerName

az cosmosdb show --resource-group $resourceGroupName --name accountName --output tsv
```

## <a name="next-steps"></a>Další kroky

Tady je několik dalších prostředků:

- [Dokumentace k Azure Resource Manager](/azure/azure-resource-manager/)
- [Azure Cosmos DB schéma poskytovatele prostředků](/azure/templates/microsoft.documentdb/allversions)
- [Šablony pro rychlý Start Azure Cosmos DB](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
- [Řešení běžných chyb při nasazení Azure Resource Manager](../azure-resource-manager/resource-manager-common-deployment-errors.md)
